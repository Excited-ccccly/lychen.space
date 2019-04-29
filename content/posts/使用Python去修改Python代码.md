---
title: "使用Python去修改Python代码"
date: 2019-04-28T22:16:50+08:00
draft: false
categories: ["Programming Language"]
tags: ["Python", "AST", "Metaprogramming", "Code Generation", "Template Engine"]

---

Python 是一门很方便优雅的语言，广泛地被应用于 Web, AI 和自动化领域，受到了工程师和科学家的喜爱。我们可以使用 Python 去处理各种数据，生成我们想要的结果。有没有想过，被操作的数据也可以是 Python 代码，我们使用一段 Python 代码去修改另外一段 Python 代码，去生成一个新的 py 文件。

我们知道，代码文件其实就是有结构的字符串，理论上我们可以直接操作源码字符串来达到我们的目的，所以可行性上完全可以。显而易见，这种方法太原始简陋了，需要面对处理很多细节，很容易出错。既然原始的数据难以操作，我们就需要用一种数据结构来抽象地表示它，只要抽象数据结构和原始的数据可以互相转化就行。这种数据结构的专业术语叫 [AST(Abstract Syntax Trees)](https://en.wikipedia.org/wiki/Abstract_syntax_tree).

```
       lexer                   parser
code ---------> tokens array ----------> ast
```

AST 需要经过 [Lexer](https://en.wikipedia.org/wiki/Lexical_analysis) 将源码字符串转变成 Token 数组，然后使用 [CFG(Context-free grammar)](https://en.wikipedia.org/wiki/Context-free_grammar) 来构造出 AST 。由于 Python 经过这么多年的发展，语法规则已经足够复杂，所以裸写一个 parser 来构造 AST 还是有难度的，虽然可以使用 [Yacc](http://dinosaur.compilertools.net/yacc/) 或者 [Antlr](https://www.antlr.org/) 来生成一个 parser，但也略微繁琐。好在 Python 官方提供了 [ast](https://docs.python.org/3/library/ast.html) 库来让我们直接获得 Python 代码的 AST.

```
In [1]: import ast

In [2]: add_expr = ast.parse("1+1")

In [3]: ast.dump(add_expr)
Out[3]: 'Module(body=[Expr(value=BinOp(left=Num(n=1), op=Add(), right=Num(n=1)))])'
```

可以看到 `1+1`被 parse 成了一个 Expr(Expression), 这个 Expr 的 *value* 是一个 BinOp(Binary Operation)，BinOp 这个二元操作的操作符 (*opcode*) 是 Add(), 左边的操作数 (*operand*) 是 Num(n=1), 右边的操作数也是 Num(n=1)

```
                  Add
                /     \
               /       \
           Num(n=1)  Num(n=1)
```

在 AST 上，使用 ast 库内置的方法，我们可以大胆地修改，裁剪，新增树中的每一个元素。

## 适用场景

很明显，当你需要修改 Python 的源码文件时，你可能需要使用 `ast`. 为什么说可能？因为如果修改的量不大，也不繁琐的时候，直接就去改更简单高效。而我很不幸，遇到了一个设计不好，抽象不足的第三方项目，一点也不 [SOLID](https://en.wikipedia.org/wiki/SOLID)。我需要为这个项目添加插件来完成我的工作，但是它没有提供一个良好的插件接口让我去实现，而是让我去修改它的源代码。添加一个插件需要改动3个源代码文件，生成一个新文件，一共是4个文件操作。在可预见的未来里，我需要为重复做十几个这样的操作，50多个相似的文件操作足以使人崩溃。内心是拒绝的，但工作还是要完成。短时间内我不可能为这个第三方项目设计一个插件架构，来向作者提 PR，还可能被拒绝。既然操作很类似，那么用代码来自动化进行这些操作是个不错的选择，这时候就轮到 `ast` 上场了。

> 并没有说 AST 只适用以上场景，所有的编译器、解释器的前端部分都会用到 AST。 IDE, Editor 里提供的丰富重构、代码静态检查等功能就是基于 AST 操作的。前端领域中常用的 [Babel](https://babeljs.io/) 和 [tsc](https://www.typescriptlang.org/) 这类 code transformer 基本上就是基于 AST 操作的。

## 具体问题

前面提到，我需要修改3个源代码文件，生成一个新文件。它们分别是

* 在 settings.py 里添加一些字符串常量定义
* 在 rules.py 里将刚刚定义的常量填充到一些 dict 和 list 中去
* 生成一个 validator
* 将生成的 validator 添加到一个 list 中去

撇开第三个操作，可以发现其实就是需要修改或新增一些 AST 中特定的 Assign 节点和 Import 节点。Assign 节点的 value 可能是 dict 或者 list.

## 实现

由于要写的插件是针对一个 website 的，所以我们先定义一个基类

```py
class BaseNodeVisitor(ast.NodeVisitor):
    def __init__(self, website):
        self.website: Website = website
```

NodeVisitor 方便我们遍历 AST；还有一个 NodeTransformer 也能遍历，只不过每一个遍历方法需要 `return modified_node`. NodeVisitor 类似于在一个 void function 里修改变量；NodeTransformer 类似于将函数的返回值赋值给变量。

### 修改 settings.py

```py
class SettingsVisitor(BaseNodeVisitor):
    def visit_Module(self, node):
        name = self.website.name
        temp_assign = Assign(targets=[Name(id=self.website.temp_queue(), ctx=Store())],
                             value=Str(s=f'haipproxy:{name}:temp'))
        node.body.extend([temp_assign])
        self.generic_visit(node)
```
`visit_Module` 方法会去修改 AST 中的 Module 节点。Module 就是 Python 中的模块，它的 *body* 属性是它的 children 节点们。我们往这个 *body* 添加了一个 Assign 节点，这个 Assign 节点是一条 Statement（有别于 Expression），它有 *targets* 和 *value* 属性，为什么是 *targets* ？因为可能有 `a=b=1` 这种语句，会有 a,b 两个 targets. 这里的 target 只有一个，是一个变量，使用 Name 表示。Name 的 *id* 是它的字面量，比如 `a=1` 中的 a； ctx=Store() 表示这个一个存值操作，类似的还有 Load() 表示取值操作。这里的 Assign 中的 *value* 是一个字符串。

`generic_visit` 方法是为了能够继续遍历当前 node 节点的 children. 具体可以查看 ast 库中的 NodeVisitor 源码理解。源码足够简单短小，这里不过多解释。

如果把 settings.py 的 AST 转化为源码，可以发现最后一行会新增了一条 `TEMP_WEBSITENAME_QUEUE = 'haipproxy:websitename:temp'`

Python AST 中所有的 Node 类型可以参考 [Meet the Nodes](https://greentreesnakes.readthedocs.io/en/latest/nodes.html)

### 修改 rules.py

```py
class RulesVisitor(BaseNodeVisitor):
    def visit_ImportFrom(self, node):
        module = node.module
        if module == "config.settings":
            aliases = [alias(name=x, asname=None) for x in [self.website.temp_queue()]]
            node.names.extend(aliases)

    def visit_Assign(self, node):
        name = self.website.name
        if 'SPEED_MAPS' == node.targets[0].id:
            node.value.keys.append(Str(s=name))
            node.value.values.append(
                Name(id=self.website.speed_queue(), ctx=Load()))
        if 'HTTPS_TASKS' == node.targets[0].id and self.website.is_https():
            node.value.elts.append(Str(s=name))
```

`visit_ImportFrom` 给 `from ..config.settings import XXX` 这句 Statement 加了一个新被 imported 的 alias， alias 的 *name* 是被引入的项的名字，*asname* 可以为它取一个别名，这里不需要别名，设为 None.

`visit_Assign` 给 `SPEED_MAPS` 这个 dict 变量新增了一个 key-value pair. 同时也给 `HTTPS_TASKS` 这个 list 新增了一个字符串。

### AST 转化回源代码

第四步和第二步中的 `HTTPS_TASKS` 类似，所以不再讨论。

使用 SettingsVisitor, RulesVisitor 和 遍历 AST 树，修改新增节点

```py
self.settings_tree = ast.parse(settings_sourcecode)
self.rule_tree = ast.parse(rules_sourcecode)
SettingsVisitor(website).visit(self.settings_tree)
RulesVisitor(website).visit(self.rule_tree)
```

现在考虑怎么把 AST 中的修改 flush 到源代码中。

`astor` 这个库可以帮我们做到这个

```py
import astor
ast.fix_missing_locations(self.settings_tree)
settings_source = astor.to_source(self.settings_tree)
```

这里的 `fix_missing_locations` 是为了给 settings_tree 里新增的节点填充一些源代码行号和列位移信息。使用 `astor.to_source` 来将 AST 转化回源代码


### 生成一个 validator

之前略过了第三步，是因为它需要写一个 validator class. 使用 AST 来构造出一个 class，还是很繁琐的。考虑到手写 `1+1` 的 AST 就已经很麻烦了，想象一下一个 class 需要手动构造多少 AST 节点，哈哈，害怕！

可以使用模板引擎来帮我们生成这个 class，首先定义这个模板

```
from haipproxy.config.settings import (
    TEMP_{{name_uppercase}}_QUEUE)
from ..redis_spiders import ValidatorRedisSpider
from .base import BaseValidator

class {{validator_classname}}(BaseValidator, ValidatorRedisSpider):
    """This validator checks the liveness of {{name_lowercase}} proxy resources"""
    name = '{{name_lowercase}}'
    urls = ['{{url}}']
    task_queue = TEMP_{{name_uppercase}}_QUEUE
    success_key = '{{success_key}}'
```

然后替换其中被 `{{}}` 包裹的模板变量

```py
from jinja2 import Template
template = Template(f.read())
r = template.render(name_lowercase=website.name_lowercase(), name_uppercase=website.name_uppercase(),
            validator_classname=website.validator_classname(), url=website.url, success_key=website.success_key)
```

这里模板引擎使用 jinja2，其实用其它模板引擎也无所谓，只要 Python 支持就好，甚至可以直接粗暴地用字符串替换。最后把替换好的 r 写入到期望中的 py 文件路径就好了。

## 总结

使用Python去修改Python代码，听上去很厉害，实际上还是挺容易的。我们使用了 ast 库去准确地修改某一处的代码，使用 jinja2 模板引擎去生成一个 py 代码文件。

这种使用代码来写代码的编程模式，专业术语叫 [Metaprogramming](https://en.wikipedia.org/wiki/Metaprogramming). Python 里的 decorator, metaclass 都属于 Metaprogramming. decorator 和 metaclass 都是程序运行时修改自己的代码，和这里离线修改不同。

## 更多

* [Compiler](https://en.wikipedia.org/wiki/Compiler)
* [Reflection](https://en.wikipedia.org/wiki/Reflection_(computer_programming))
* [Template metaprogramming](https://en.wikipedia.org/wiki/Template_metaprogramming)