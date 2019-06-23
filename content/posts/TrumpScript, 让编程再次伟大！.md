---
title: "TrumpScript, 让编程再次伟大！"
date: 2019-06-23T23:34:09+08:00
draft: false
categories: ["Programming Language"]
tags: ["TrumpScript", "Trump", "America", "Python", "Compiler"]

---

[TrumpScript](https://github.com/samshadwell/TrumpScript) 是一门使用特朗普风格的语句来编程的脚本语言，十分的欢乐有趣。作者 Sam “认为” 当今所有的编程语言，都得不到伟大的特朗普总统的认同。在 [HackRice 2016](https://www.youtube.com/watch?v=qFH8nKsSwB0) 这场 Hackthon 上，Sam 和另外两个小伙伴(Chris 和 Dan)，合力开发了 [TrumpScript，一举拿下第一名](https://devpost.com/software/trumpscript)，设计的图标也很有意思，像特朗普那飘逸的头发。

![](/images/TrumpScript/TrumpScript.jpg)

在立马学习 TrumpScript(什么？又有新语言要学习了？卒...) 语言之前，我们先看看它的 feature，才能知道为什么非学不可！

## 语言特性

为了得到最大份的快乐，在保留原文的基础上，简单翻译。

* No floating point numbers, only integers. America never does anything halfway.

TrumpScript 没有浮点数，只能用整数。美利坚永远不半途而废。

* All numbers must be strictly greater than 1 million. The small stuff is inconsequential to us.

所有的数字必须大于100W，其它小数字对富有的特朗普来说无关紧要。

* There are no import statements allowed. All code has to be home-grown and American made.

没有 import(和“进口”双关) 语句。所有的代码都必须由美国制造。

* Instead of True and False, we have the keywords fact and lie.

TrumpScript 使用 fact 和 lie，而不是 True 和 False

* Only the most popular English words, Trump's favorite words, and current politician names can be used as variable names.

只有最流行的、特朗普喜爱的英语单词，以及政治家的名字才能作为变量的名字。

* Error messages are mostly quotes directly taken from Trump himself.

错误信息几乎都引用自特朗普的言论。

* All programs must end with America is great.

所有的 TrumpScript 必须以 "America is great" 结尾，否则会报错。

* Our language will automatically correct Forbes' $4.5B to $10B.

由于福布斯错误只承认特朗普有45亿美元的身价，而不是特朗普声称的100亿美元。TrumpScript 会自动纠正这个错误，所有的 4500000000 会被修正为 10000000000，来彰显特朗普的富贵。

* In its raw form, TrumpScript is not compatible with Windows, because Trump isn't the type of guy to believe in PC.

TrumpScript 不兼容 Windows, 因为特朗普对 PC 没有信心！PC 既指 Personal Computer，还指 Political Correctness, 此处为双关语。

* TrumpScript boycotts OS X and all Apple products until such time as Apple gives cellphone info to authorities regarding radical Islamic terrorist couple from Cal.

TrumpScript 拒绝在所有的苹果设备上运行，除非苹果同意向政府提供恐怖分子的手机信息。

* The language is completely case insensitive.

TrumpScript 对大小写不敏感。

* If the running computer is from China, TrumpScript will not compile. We don't want them stealing our American technological secrets.

TrumpScript 不会在中国的电脑上运行，特朗普不想中国来窃取美国的科技机密。

* By constructing a wall (providing the --Wall flag), TrumpScript will refuse to run on machines with Mexican locales

如果运行带上了 --Wall 这个标记，TrumpScript 会拒绝在墨西哥的电脑上运行。--Wall 来自于 GCC，表示会打开所有的警告开关，它也是双关语，指美国单方面在美国和墨西哥边界修建围墙，来阻止墨西哥非法移民的行为。

* Warns you if you have any Communists masquerading as legitimate "SSL Certificates" from China on your system.

当你的系统中有来自中国机构信任的 SSL 证书时，TrumpScript 会警告你。

* Won't run in root mode because America doesn't need your help being great. Trump is all we need.

TrumpScript 不支持在 Root 用户下运行，因为美利坚很强大，特朗普很伟大，不需要你的支持(root 有支持的意思)。

* Easy to type with small hands

手小也能愉快地使用 TrumpScript 编程（特朗普被嘲笑手小）。

![](/images/TrumpScript/trumpthumbsweb.jpg)


* If you find you can't get any TrumpScript to run on your computer (probably because we disallow the two most popular operating systems), you can specify the --shut-up flag to let the interpreter know you just want your code to run, damn it.

如果你发现你的电脑不能运行 TrumpScript（多数情况下会是如此，因为我们禁止了两大流行操作系统），你可以附加上 `--shut-up` 这个参数来绕过这个限制。

## 语法

略去基本的语法不提，来看看激动人心的部分！

* Strings are denoted by double quotes ("I love Trump")

字符串使用双引号包含（这就是激动人心的部分？作者出来挨打！

* The is and are keywords are used both to check for equality, and for assignment. To use for assignment, say something like Trump is great or Democrats are dumb. To use to check for equality, do the same but append a ?. For example, you may need to ask yourself Trump is "the best"? (although we all know that would evaluate to fact anyway)

**is** 和 **are** 用于判断是否相等和赋值，比如 `Trump is great` 把 great 这个值赋给了 Trump 这个变量；但如果带上 **?**，就变成了判断是否相等，比如 `Trump is "the best"?` 就是在判断 Trump 这个变量是不是等于 "the best"，结果显而易见，这是一个 **fact**!

* Assignment can also be done via the make keyword. E.g. Make America great assigns the value of the variable great to America

赋值可以通过 **make** 语句完成，比如 `Make America great` 就将 great 赋给了 America 这个变量。

* Printing to stdout can be done via tell or say

使用 **tell** 和 **say** 可以向标准输出打印

* While loops are denoted via as long as. And that's the only type of loop you need anyway

循环使用 **as long as**，这是你唯一需要使用的循环语法。

* If a 'word' (so anything that could be a variable name) is deemed unnecessary by the compiler, it's simply thrown away. So you can make truly self documenting code, or code that appear to read very very similarly to real speeches by the big man himself. You can find some interesting examples in our test/test_files directory

TrumpScript 会过滤到一些无关紧要的单词，来确保使用 TrumpScript 写出的程序有着最佳的可读性以及诗歌般的韵律

```
What are we in this country
Hillary speaks nothing but lies
But look at me I came to this election to make guys
believe again
believe in fact
if, all of us real lies the light; : say "VOTE TRUMP" !
but I know we should be free
else the result will be bad: all the work of George
Washington was for nothing
so this election say "Hello World" say "TRUMP FOR PRESIDENT"!
America is great.
```

* But most importantly, Trump doesn't like to talk about his failures. So a lot of the time your code will fail, and it will do so silently. Just think of debugging as a fun little game.

最重要的是，伟大的特朗普总统不喜欢讨论他的失败。所以大部分时间你的代码会报错，而且这个错误不会抛出，这就好比在调试一个有趣的小游戏。

## “硬核”源码分析

一般的公众号文章至多到这里就收尾了，但是我不一样。TrumpScript 承包了我一晚上的欢乐，不带上源码分析有点对不住伟大的特朗普总统。事实上，阅读源码还发现了更多的梗，带来了更大更强的欢乐。

看完了之前 TrumpScript 的语言特性和语法，人民群众普遍认为它和那些死板无趣的流行编程语言完全不一样！有部分人甚至对 TrumpScript 是如何实现的产生了极大的兴趣。那就继续往下看吧，TrumpScript 源码分析，世间仅此一篇，只有在这里，赶快收藏 studymakesmehappy.club.

![](/images/TrumpScript/trump-invite.jpg)

TrumpScript 是一个 Python 程序，将代码 clone 下来，然后使用编辑器打开 TrumpScript/src/ 目录。可以发现它是一个短小精悍的脚本语言解释器(interpreter)，一共只有9个 .py 文件，其中 `tokenizer.py` 和 `utils.py` 是最有意思的，不过万物生于 main, 我们先从 `main.py` 开始。

```py
    # main.py
    if not args.shut_up:
        Utils.verify_system(args.Wall)
    # Compile and go
    Compiler().compile(sys.argv[-1])
```

`main.py` 使用 argparse 来解析参数，可以发现 shut_up 在个 flag 控制着是否校验使用者的电脑系统，`verify_system` 这个函数稍后分析，先看主要的。

`sys.argv[-1]`取最后一个参数，也就是 TrumpScript 脚本程序的路径，然后编译

```py
    # compiler.py
    def compile(self, source):
        modu = self.parse(self.tokenize(source))
        fix_missing_locations(modu)
        exec(compile(modu, filename="<ast>", mode="exec"))
```

如我们所料，词法分析(tokenize)和语法分析(parse)少不了，`fix_missing_locations` 是 python 的 ast 包里的函数，会修正 python 语法树中缺少了一些位置信息。咦？我们写的不是 TrumpScript 吗，怎么去修正 python 的语法树了，按道理要修复也是修复 TrumpScript 的语法树啊！别着急，我们做一个猜测，很有可能是 parse 这个函数直接返回了 python 格式的语法树。继续往下看，`exec` 和 `compile` 是 python 内置的函数，`compile` 可以将 ast 树编译成可以执行的 code object，`exec` 可以执行 code object. 这印证了我们的想法，TrumpScript 的作者们很好地发挥了不乱造轮子的精神，没有自己写一个 ast interpreter，而是复用了 python 的 interpreter, 所以才需要将 TrumpScript 程序转换成 python 格式的语法树。

### Tokenizer

再来看看 tokenize

```py
    # tokenizer.py
    def tokenize(filename):
        tokens = Tokenizer._first_pass(filename)
        tokens = Tokenizer._second_pass(tokens)
        return tokens
```

这里有 first_pass 和 second_pass，first_pass 将代码纯文本转化成一个个 token, second_pass 在 first_pass 的基础上做一次检查和修正的工作

所有的 tokenizer 模式都差不多，都是逐字符解析源码文件。

```py
    # tokenizer.py _first_pass
    while i < len(data):
        '''
            Facts and Lies will flip/flop depending on Trump's mood every few minutes.
            If your code fails, try again in a bit. Trump might have changed his mind.
        '''
        random.seed(datetime.now().time().minute)
        flip_flop = bool(random.getrandbits(1))
        ......
        if c.isspace() or c == ".":
            if c == "\n":
                line += 1
        # Operators (special symbol form) and punctuation
        elif c == "+":
            tokens.append(Tokenizer.toke(T_Plus, None, line))
        ......
        elif word == "true" or word == "facts" or word == "truth" or word == "fact":
            if (flip_flop):
                tokens.append(Tokenizer.toke(T_False, None, line))
            else:
                tokens.append(Tokenizer.toke(T_True, None, line))
        elif word == "false" or word == "lies" or word == "nonsense" or word == "lie":
            if (flip_flop):
                tokens.append(Tokenizer.toke(T_True, None, line))
            else:
                tokens.append(Tokenizer.toke(T_False, None, line))
```

```py
{"type": token_type, "value": token_value, "line": line}
```

一个 token 是一个 dict。type 是 token 的类型；在 `constants.py` 里定义, value 是 token 的值（比如数字类型的 token 是有值的，但是 `?` 这种 token 是没有值的，会为 None；line 记录 token 对应源代码中的行号，方便报错的时候带上行号。



这个 flip_flop 是最骚的, 它每分钟会跟随特朗普的不稳定的情绪变化而变化（讽刺特朗普变化无常），从而决定是否将 fact 解析成 T_False 还是 T_True.  xswl😆

```py
        # tokenizer.py _first_pass
        # Closures and precedence
        elif c == ",":
            tokens.append(Tokenizer.toke(T_LParen, None, line))
        elif c == ";":
            tokens.append(Tokenizer.toke(T_RParen, None, line))
        elif c == ":":
            tokens.append(Tokenizer.toke(T_LBrace, None, line))
        elif c == "!":
            tokens.append(Tokenizer.toke(T_RBrace, None, line))
```

诗歌般韵律的 TrumpScript 不会出现 `(){}` 这些不优美的字符，所以用 `,;:!` 替代

```py
      # tokenizer.py _first_pass
      elif c.isdigit():
          num = ""
          while data[i].isdigit():
              num += data[i]
              i += 1
          else:
              tokens.append(Tokenizer.toke(T_Num, int(num), line))
```

常规的解析数字的操作，字符串解析也是类似的操作

```py
      # tokenizer.py _first_pass
      elif word == "over":
          tokens.append(Tokenizer.toke(T_Over, None, line))
      elif word == "safe" or word == "safer":
          tokens.append(Tokenizer.toke(T_Mod, None,line))
```

这里提议下, T_Over 是除法运算符的 Token Type，T_Mod 是将字符串转化为 `int(float(variable))` 整形数字的操作

first_pass 的操作比较常规，再来看看 second_pass

```py
    # tokenizer.py _second_pass
    def _second_pass(tokens):
        # Make sure we do "America is great"
        if not Tokenizer._check_for_freedom(tokens):
            Tokenizer._error(tokens[-1]['line'], 'freedom')
        # Convert "as long as" to while
        tokens = Tokenizer._combine_whiles(tokens)
        # Ensure words are English
        Tokenizer._ensure_freedom(tokens)
        # Ensure all numbers are greater than 1 million, and that 4.5B is converted to 10B
        Tokenizer._fudge_the_numbers(tokens)
        # They Say He Doesn't Like China... He Loves China
        Tokenizer._love_china(tokens)
        return tokens
```

* `_check_for_freedom` 确保每个程序以 America is great 结尾，否则报错。
* `_combine_whiles` 将 as long as 这三个字符串 Token 转换成 While 类型的 Token.
* `_ensure_freedom` 确保代码中用到的单词仅仅来自有 `allowed_words.py` 里的单词, `allowed_words.py` 通过 `vocabulary.py` 生成，不管它。同时确保不能出现 `disallowed_words.py` 中的单词。
* `_fudge_the_numbers` 负责确保数字不小于100W，同时会把 4.5B 替换成 10B
* `_love_china` 把 i don't like china 替换成 i don't like china. i love china

tokenizer 到此结束，接下来是 parser

### Parser

parse 比较无聊，就是一个 [Recursive Descent Parser](https://en.wikipedia.org/wiki/Recursive_descent_parser)，Recursive Descent Parser 可以用来解析 LL(K) 的 [Context Free Grammar](https://en.wikipedia.org/wiki/Context-free_grammar).

```py
    # parser.py
    def parse(self, tokens) -> AST:
        filtered = self.filter_tokens(tokens)
        # Build the entirety of the Abstract Syntax tree
        return self.handle_module(filtered)
```

首先过滤一些，不属于任何一个语法规则、无关紧要的 token. 比如 People Make Trump great 中的 People.

```py
    # parser.py
    def handle_module(self, tokens) -> Module:
        body_list = []
        while len(tokens) > 1:
            state, tokens = self.handle_anything(tokens)
```

parser 在 while 循环中逐 token 分析

```py
    # parser.py
    def handle_anything(self, tokens):
        start = self.peek(tokens)
        return self._token_to_function_map[start](tokens)
```

handle_anything 根据 token 的 Type 来 dispatch 不同的解析函数。

```py
    def handle_binop(self, left, op, tokens):
        valid_tokens = [T_LParen, T_Quote, T_Num, T_True, T_False]
        tokens.pop(0)
        nxt = self.peek(tokens)
        if nxt == T_Word:
            right = self._get_value_from_word_token(tokens)
        elif nxt in valid_tokens:
            right, tokens = self._token_to_function_map[nxt](tokens)
        else:
            right = self._temporary_error(msg="binop_error")

        return BinOp(left=left, op=op, right=right), tokens
```

如果发现是一个 binop (二元操作), 将它转化成 python 的 ast 中的 BinOp 节点。注意，这个实现其实有 bug，忽略了操作符的优先级。比如

```
1000000 plus 1000000 over 1000000;
```

这个会被正确地解析为 1000000+(1000000/1000000)，但

```
1000000 over 1000000 plus 1000000;
```

会被错误地解析为 1000000/(1000000+1000000)

(不要为什么要用这么大的数字来举例子，一点也不直观。这得去问特朗普，因为小于100W的数字无关紧要)

Parser 的其它部分都差不多，就此打住。

### Utils

最后再回到之前提到的 Utils

```py
    # utils.py
    def verify_system(wall) -> None:
        """
        Verifies that this system is Trump-approved, throwing
        a SystemException otherwise
        :return:
        """
        Utils.no_wimps()
        Utils.no_pc()
        Utils.boycott_apple()
        Utils.no_commies_mexicans_or_kenyans(wall)
        Utils.no_commie_network()
```

* `no_wimps` 确保在 root 用户执行下会失败，因为强大的美国不需要 root（支持）
* `no_pc` 确保在 Windows 下会失败，特朗普不相信 PC
* `boycott_apple` 确保在 Apple 的产品下会失败，Apple 拒绝提供恐怖分子的手机信息给政府，公然抵抗特朗普，该打
* `no_commies_mexicans_or_kenyans` 不能运行在共产党、墨西哥、肯尼亚（奥巴马祖籍肯尼亚）的电脑上
* `no_commie_network` 共产党的证书机构不可信

最后再看看 TrumpScript 的报错机制

```py
    # utils.py
    class SystemException(Exception):
        def __init__(self, msg_code) -> Exception:
            if msg_code in ERROR_CODES:
                raise Exception(random.choice(ERROR_CODES[msg_code]))
            else:
                raise Exception(random.choice(ERROR_CODES['default']))
```

自定义一个异常，异常消息根据 msg_code 而随机选，具体在 `constants.py` 中的 `ERROR_CODES` 中，截取看一段

```py
ERROR_CODES = {
    # For quotes that didn't get terminated
    'unterminated_quote': ["And, believe me, if I win, if I become President, that will end.", "Anyone who thinks my "
                           "story is near over is sadly mistaken."],
    # If they try to use a word that isn't common English
    'nonword': ["This is a country where we speak English",
                "We have a country where to assimilate you have to speak English"],
    # If they try to use a word that we've explicitly banned
    'badword': ["Trump doesn't want to hear it"],
    ......
}
```

这就是 `Error messages are mostly quotes directly taken from Trump himself.` 这个 feature 的实现方法

## 结语

TrumpScript 是一场36小时的 Hackthon 上诞生的产物，很佩服作者们的脑洞和执行力。在代码中看到这段注释

```py
    # BinOp(s)
    # TODO: what the fuck is going on here? I wrote this at 9am after nigh 24 hours of wakefulness
    def handle_binop(self, left, op, tokens):
        ......
```

也是让参与过 Hackthon 的人会心一笑。







