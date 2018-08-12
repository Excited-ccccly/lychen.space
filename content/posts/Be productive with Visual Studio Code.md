---
title: "Be productive with Visual Studio Code"
date: 2018-08-11T13:14:54+08:00
draft: true
toc: true
---

曾经有一位 Code Ninja 教导我，“在软件工程领域中，厉害的人都有一款用得很溜的 IDE/Editor”。虽说不完全同意，毕竟比起写代码，思想更重要，但熟练掌握一款编辑器确实能大幅提高软件工程师的工作效率。

我用的最多的 Editor 是 [Visual Studio Code](https://code.visualstudio.com/), 其次是 [Intellij Idea](https://www.jetbrains.com/idea/), 最后是 [Vim](www.vim.org). 根据场景，我会使用不同编辑器。

场景  | Editor 
---  | --- 
JVM 上的语言, PHP | Intellij Idea 
服务端代码的小修改，配置文件的修改 | Vim 
其它所有场景 | VSCode 

本文主要关注如何使用 VSCode 提高编码效率，最后会说一下和 Intellij idea 的对比。不会具体到语言，即如何使用 VSCode 编写和调试 XXX 语言不在本文的讨论范围之内，更多的关注在 VSCode 本身上。

## Basic

> Being able to keep your hands on the keyboard when writing code is crucial for high productivity. --- VSCode Doc

当我在谈生产力的时候我一般在谈快捷键。道理大家都懂，但这个过程历来很痛苦。比如在学习 Vim 快捷键的时候，有的人会在命令行中 `vimtutor` 把整个 Vim 的基础教程过一遍；有的人会去搜索引擎里查询；有的人会把别人的博客提到的 Vim 快捷键摘录下来。每一次都能记住一些快捷键，但许久不用，就又忘记了，又要查资料、看教程。很多软件都是如此，都是 **查快捷键->记忆快捷键->用快捷键->忘记快捷键->查快捷键** 的循环，难受。

[Atom(Github 出品的现代编辑器)](atom.io) 改变了这一切，Atom 提供了 Command Palette 功能

![](/images/Be productive with Visual Studio Code/Atom Command Palette.png)

只要 `ctrl shift p` (Linux/Windows) 或者 `cmd shift p` (macOS) 打开 *Command Palette*, 告诉 Atom 你想做的事情，Atom 会自动帮你选出一些待选项，并且时刻提醒你，它们的快捷键是什么。使用 *Command Palette* ，即使你不熟悉快捷键，你也可以只用键盘来完成大部分操作，每一次，Atom 都会悄悄提醒一下快捷键，慢慢地，你自然而然地学会了这个快捷键。即使忘记了，*Command Palette* 时刻都能帮你找回来，你的手不需要离开键盘。很人性化，很高效，很让人开心！

这个功能很棒，于是 VSCode 也照搬了过来。

![](/images/Be productive with Visual Studio Code/Go Back.gif)

我不知道 **后退** 的快捷键是什么，只要 `ctrl shift p`, 然后输入 *Back*, VSCode 就会让我选择 *Go Back* 。哦，原来 **后退** 的快捷键是 `ctrl alt -`，我记下了。

*Command Palette* 让我彻底喜欢上 VSCode 和 Atom 这类 Modern Editor, 以至于所有项目都尽可能用 VSCode 写。

接下来会介绍一些 VSCode 上好用的快捷键。由于 Linux/Windows 和 macOS 的快捷键不一样，用户也可能会更改自己的快捷键，所以我会用 **命令名称** 来介绍，遇到喜欢的快捷键，就在 *Command Palette* 中试一试吧。

## Editing Hack



## Mastering Keyboard Shortcuts

## Personalization Makes You Different

### Themes

![](/images/Be productive with Visual Studio Code/Theme.gif)

以上是 VSCode 中选择主题以及安装新主题的过程，猜猜用了多少快捷键？(注：主题也是一个扩展)

*Command Palette* 里选择 *Preferences: Color Theme* -> 方向键上下浏览主题 -> Page Down 快速翻到最底下的 *Install Additional Themes* -> *Command Palette* 里选择 *View: Show Extensions* 聚焦在扩展 Sidebar -> *Tab* 聚焦在扩展列表 -> *Enter* 预览主题 -> *Tab* 聚焦在扩展的 *Install* 选项，*Enter* 确认安装 -> *Tab* 聚焦在扩展的 *Reload* 选项，*Enter* 确认重启

我尝试了很多主题，但由于个人原因，最后还是选择了默认主题，其它主题看起来眼睛会难受，默认的最舒服，即使不好看。只能推荐安装这个 **Material Icon Theme** 这个图标集合。

![](/images/Be productive with Visual Studio Code/Material Icon Theme.png)

### Font

我推荐 [**Fira Code**](https://github.com/tonsky/FiraCode)，因为它支持 *Font Ligatures(连写字符)* 特性。

![](/images/Be productive with Visual Studio Code/Font Ligatures.png)

可以看出图中的 =>, ===, >=, !==, ++ 都已经连写了。连写更符合人眼的阅读习惯，原先的2个或3个字符变成了一个，减轻了人眼扫描的压力；但连写不符合人的键盘输入习惯，需要适应一段时间；连写也不适合展示代码，新手会不适应；连写只适合自己爽。

Fira Code 字体安装请参考[官方 wiki](https://github.com/tonsky/FiraCode/wiki)

安装好之后，在 *Command Palette* 里选择 *Preferences: Open User Settings* 打开设置 settings.json, 加上以下配置

```json
"editor.fontFamily": "Fira Code",
"editor.fontLigatures": true,
```

Fira Code 也支持 Intellij Idea, Visual Studio 等其它编辑器，感兴趣的话不妨去 wiki 看看。

## Extensions Make You Coding Life Easy and Cool

推荐3个扩展

* Gitlens
* Wakatime
* Settings Sync

Gitlens 可以大幅增强 VSCode 上的 Git 使用体验。直接安装试试吧

Wakatime 可以统计你的编码时间分布(我是名副其实的 Markdown 程序员)

![](/images/Be productive with Visual Studio Code/Wakatime.png)

Settings Sync 可以同步你的扩展、设置、主题、快捷键、代码片段，可以说是日常编码必备扩展了，显著提升多平台的使用体验。

## Tricks Make You Proud of VSCode



## Comparing With Jetbrains Family Products


