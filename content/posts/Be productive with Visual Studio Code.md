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

本文主要关注如何使用 VSCode 提高编码效率，最后会说一下和 Intellij Idea 的对比。不会具体到语言，即如何使用 VSCode 编写和调试 XXX 语言不在本文的讨论范围之内，更多的关注在 VSCode 本身上。

## Basic

> Being able to keep your hands on the keyboard when writing code is crucial for high productivity. --- VSCode Doc

当我在谈生产力的时候我一般在谈快捷键。道理大家都懂，但这个过程历来很痛苦。比如在学习 Vim 快捷键的时候，有的人会在命令行中 `vimtutor` 把整个 Vim 的基础教程过一遍；有的人会去搜索引擎里查询；有的人会把别人的博客提到的 Vim 快捷键摘录下来。每一次都能记住一些快捷键，但许久不用，就又忘记了，又要查资料、看教程。很多软件都是如此，都是 **查快捷键->记忆快捷键->用快捷键->忘记快捷键->查快捷键** 的循环，难受。

[Atom(Github 出品的现代编辑器)](https://atom.io/) 改变了这一切，Atom 提供了 Command Palette 功能

![](/images/Be productive with Visual Studio Code/Atom Command Palette.png)

只要 `Ctrl Shift P` (Linux/Windows) 或者 `Cmd Shift P` (macOS) 打开 *Command Palette*, 告诉 Atom 你想做的事情，Atom 会自动帮你选出一些待选项，并且时刻提醒你，它们的快捷键是什么。使用 *Command Palette* ，即使你不熟悉快捷键，你也可以只用键盘来完成大部分操作，每一次，Atom 都会悄悄提醒一下快捷键，慢慢地，你自然而然地学会了这个快捷键。即使忘记了，*Command Palette* 时刻都能帮你找回来，你的手不需要离开键盘。很人性化，很高效，很让人开心！

这个功能很棒，于是 VSCode 也照搬了过来。

![](/images/Be productive with Visual Studio Code/Go Back.gif)

我不知道 **后退** 的快捷键是什么，只要 `Ctrl Shift P`, 然后输入 *Back*, VSCode 就会让我选择 *Go Back* 。哦，原来 **后退** 的快捷键是 `Ctrl Alt -`，我记下了。

*Command Palette* 让我彻底喜欢上 VSCode 和 Atom 这类 Modern Editor, 以至于所有项目都尽可能用 VSCode 写。

接下来会介绍一些 VSCode 上好用的快捷键。由于 Linux/Windows 和 macOS 的快捷键不一样，用户也可能会更改自己的快捷键，所以我会用 **命令名称** 来介绍，遇到喜欢的快捷键，就在 *Command Palette* 中试一试吧。

## Editing Hack

### Common

在不同的软件，甚至不同的操作系统中，有一些快捷键是共用的。由于这些不是 VSCode 的命令，所以还是得罪一下你们，全部用快捷键组合。

![](/images/Be productive with Visual Studio Code/Arrow+Shift.gif)

方向键可以上下左右移动光标就不多说了，配合上 `Shift` 则可以在移动光标的同时选择字符.

![](/images/Be productive with Visual Studio Code/Ctrl+Arrow+Shift.gif)

方向键搭配上 `Ctrl` 可以按 **word** 来移动光标，也可以配合 `Shift` 来选则 word.

![](/images/Be productive with Visual Studio Code/Pg Arrow+Shift.gif)

`Home` 键可以将光标移到行首，`End` 键可以将光标移动行尾，`Page Up` 可以向上翻页，`Page Down` 可以向下翻页。它们都可以配合 `Shift` 来进行选中。

![](/images/Be productive with Visual Studio Code/Ctrl+Pg Arrow+Shift.gif)

配合 `Ctrl`，`Home` 键可以将光标移到文件头，`End` 键可以移到文件尾；`Page Up` 可以顺序切换到前一个 tab，`Page Down` 可以顺序切换到下一个 tab. `Ctrl`, `Home`, `End`, `Shift` 可以搭配起来用。

以上的快捷键在 Linux（桌面为 Gnome）和 Windows 上都是通用的，macOS 我想不起来了，应该差不多的。这些快捷键也可以在 Intellij Idea, Firefox, Chrome, Terminal. Firefox(自带) 和 Chrome（装插件）建议开启光标浏览。
`
![](/images/Be productive with Visual Studio Code/Firefox.gif)

### VSCode Favored

![](/images/Be productive with Visual Studio Code/Editing Selection.gif)

* *Add Selection To Next Find Match* 会选中下一个相同的字符串，在拼写错误等需要同时编辑一个字符串的时候很好用。

* *Add Cursor Above* 和 *Add Cursor Below* 适合同时编辑多行。`Esc` 可以退出多行编辑的状态，*Soft undo* 可以 undo 上一次选择。
* *Move Line Up* 和 *Move Line Down* 可以上下移动当前（选中）的代码行

![](/images/Be productive with Visual Studio Code/Editing.gif)

* *Toggle Line Comment* 切换行注释
* *Delete Line* 删除行
* *Fold* 和 *Unfold* 可以切换 Code Block 的折叠状态
* *Indent Line* 可以向内缩进代码，*Outdent* 可以向外缩进代码
* *Go to Bracket* 可以将光标移动到对应的括号

它们配合之前 Common 中的 `Ctrl`, `Shift`, 方向键等可以玩出花来。

![](/images/Be productive with Visual Studio Code/Ctrl+P.gif)

* `Ctrl P` 可以在最近的文件中切换, `Ctrl Tab` 也可以
* `Ctrl P` 然后再 `@` 可以查看当前代码文件的 Symbol 结构, 加个 `:` 可以将 Symbol 分组

![](/images/Be productive with Visual Studio Code/Refactor.gif)

* *Quick Fix* 可以快速重构你的代码，只是没有 Intellij Idea 的 `Alt Enter` 那么强而已
* *Rename Symbol* 可以重命名

以上是我编辑代码时常用的快捷键，剩下的其它快捷键可以尽情在 *Command Palette* 中探索。

## Mastering Keyboard Shortcuts

除了编辑代码的快捷键，VSCode 还有一些其它提高生产力的快捷键。

### Debugging

![](/images/Be productive with Visual Studio Code/Python Debugging.gif)

* *Toggle Breakpoint* 设置断点
* *Start Debugging* 开始调试
* *Step Into* 跳进
* *Step Out* 跳出
* *Debug: Restart* 重新开始调试
* *Debug: Continue* 恢复

### Sidebar

![](/images/Be productive with Visual Studio Code/Sidebar.gif)

* *View: Show Explorer* 打开文件资源管理器 Sidebar，可以通过方向键浏览文件树
* *Search: Find in Files* 打开搜索 Sidebar，速度超快。多次按 `Tab` 键可以聚焦到搜索结果，**Sidebar 里的内容都可以通过 `Tab` 来切换聚焦**
* *View: Show SCM* 打开源代码管理器 Sidebar
* *View: Show Debug* 打开调试 Sidebar
* *View: Show Extensions* 打开扩展 Sidebar
* *View: Focus First Editor Group* 重新回到编辑文件的状态
* *View: Toggle Sidebar Visibility* 是否显示 Sidebar

### Others

* *View: Toggle Integrated Terminal* 在终端和编辑窗口切换
* *File: Open Recent..* 打开最近的项目，`Enter` 在当前目录打开，`Ctrl Enter` 在新窗口打开
* *Gitlens: Compare Commit With Previous* 对比修改的代码（需安装 Gitlens 插件）
* *Preferences: Open User Settings* 打开用户设置 settings.json
* *Preferences: Open Keyboard Shortcuts* 打开键盘快捷键设置
* *Notifications: Clear All Notifications* 清楚消息通知

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

### Custom Settings

建议配置以下选项

```json
// 自动保存
"files.autoSave": "afterDelay"
// 自动格式化
"editor.formatOnSave": true,
"editor.formatOnPaste": true
// 用空格不用 Tab 
"editor.insertSpaces": true
// 渲染空格
"editor.renderWhitespace": "all"
```

## Extensions Make You Coding Life Easy and Cool

推荐3个扩展

* Gitlens
* Settings Sync
* Wakatime

Gitlens 可以大幅增强 VSCode 上的 Git 使用体验。直接安装试试吧

Wakatime 可以统计你的编码时间分布(我是名副其实的 Markdown 程序员)

![](/images/Be productive with Visual Studio Code/Wakatime.png)

Settings Sync 可以同步你的扩展、设置、主题、快捷键、代码片段，可以说是日常编码必备扩展了，显著提升多平台的使用体验。

## Tricks Make You Proud of VSCode

### Zen Mode

### File Diff

### Markdown Editor

### Snippets

### Add Files to VSCode Project(Maybe not Proud of It)

## Comparing With Jetbrains Family Products


