---
title: "Set Up Development Environment on Windows"
date: 2018-03-04T15:43:15+08:00
categories: ["DevOps", "Tools"]
tags: ["Windows", "Package Manager", "Linux", "WSL", "Terminal Emulator", "Editor", "Font"]
---

> 闲言碎语：长久以来，Windows 一直被诟病在开源领域上开发体验不好，但最近几年，微软做出了巨大的改变，全力拥抱了开源，Github 上随处可见微软开发者的 Contribution；同时，笔记本电脑领域上几乎所有的创新都来自于 Windows 阵营，Windows 电脑变得越来越酷。这两点促使我专门为 Windows 开发社区做出自己的一份贡献。希望大家保持开放与沟通，避免无意义的争吵，塑造良好的社区氛围。

这篇博客会介绍一些加快开发效率的工具，会安利以下(类别的)产品:

* Package Manager(包管理器)
* Linux Toolkit(Linux 工具包)
* Terminal Emulator(终端模拟器)
* Windows Subsystem for Linux(Windows 上的 Linux 子系统)
* Oh-My-Zsh
* Editor&IDE(编辑器和集成开发环境)
* Font(字体)

## Package Manager
[scoop](https://github.com/lukesampson/scoop) 是 Windows 上一个很友好的包管理器，类似于 Ubuntu 上的 apt，macos 上的 homebrew。

#### Install
打开 Powershell
```Powershell
set-executionpolicy remotesigned -s currentuser
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```
#### Usage
scoop 可以简化 Windows 上的软件安装流程。
```shell
scoop install nodejs
```
scoop 的软件库很全，几乎你想要的都能找到，再也不用打开浏览器->看文档->下载->配置环境变量了。

Windows 平台上还有另外一个知名的包管理器 [chocolatey](https://chocolatey.org/)，scoop 的优势在于不需管理员权限，因为所有的软件安装在用户本地，只对该用户有效，也减少了环境变量污染等问题，提高了使用体验。
## Linux Toolkit
Linux 爱好者的你一定很希望在 Windows 上也可以使用 grep, sed, awk 等等工具。
```shell
scoop install busybox
```
一条命令就能帮你安装 130 多个工具，你熟悉的 Linux 工作体验又回来了！
## Terminal Emulator
Windows 上的终端很难用，有不少替代品可供选用。
```shell
scoop bucket add extras # conemu 在 extras 库中，关于 scoop 中的 bucket 概念，参考: https://github.com/lukesampson/scoop/wiki/Buckets
scoop install conemu
```
[ConEmu](https://github.com/Maximus5/ConEmu)是 Windows 上一个很优秀的 Terminal Emulator，tabs, splits 等等都提供给你。
![ConEmu](images/set-up-development-environment-on-windows/ConEmu.png)

如果你是 **Electron-Fan** 的话，[Hyper](https://github.com/zeit/hyper) 也是一个不错的选择。

如果你是 **UWP-Fan** 的话，[FluentTerminal](https://github.com/felixse/FluentTerminal) 提供给你
## Windows Subsystem for Linux
有没有想象过将 Linux 原生应用跑在 Windows 上? 由于 Windows 优秀的内核架构，这个不成问题，Windows Subsystem for Linux(WSL) 就是这么一种实现，你可以在 Windows 上安装 Ubuntu, Opensuse, Centos 等等，并且随意地在它们之间切换。

#### Install
管理员身份打开 Powershell
```Powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

* 如果是最新的 Win10 系统，到应用商店里就能直接安装 Ubuntu, Opensuse 等
* 如果是老版本的 Win10 系统，建议升级到最新的 Win10，没理由停留在老版本的 Win10。如果暂时不想升级:
设置-> 更新和安装 -> 开发者选项 -> 开发者模式。命令行里运行 **bash**

详细的安装过程参考这篇文档 [Install the Windows Subsystem for Linux](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)


#### Usage

WSL 相比于 Cygwin 的优势在于它强大的 interoperability

> 
```shell
wsl ls -la | findstr "foo"
```
ls 是 linux 命令, findstr 是 windows 命令, wsl 允许两者结合起来用，超酷的！

> 
WSL 还允许你在 Linux 下访问 Windows 上的文件，调用 Windows 上的程序。
```bash
$ ipconfig.exe | grep IPv4 | cut -d: -f2
172.21.240.1
10.159.21.24
```
bash 里直接调用 Windows 上的 ipconfig.exe

如果你想 hack wsl，玩一些更有意思的事情，可以看看这个项目 [WSL-Distribution-Switcher](https://github.com/RoliSoft/WSL-Distribution-Switcher)

如果你想了解 WSL 背后有趣的实现原理，可以参考这篇博客 [Windows Subsystem for Linux Overview](https://blogs.msdn.microsoft.com/wsl/2016/04/22/windows-subsystem-for-linux-overview/)。一句话来说，WSL 将 Linux System Call 翻译并转发至 Windows Kernel。

## Oh-My-Zsh
有了 WSL，你已经可以在 Windows 上跑 Linux 了。这时候，你的欲望膨胀了，你想要用 oh-my-zsh 那一套，加快你的开发效率。没问题!

以 Windows 上的 Ubuntu 为例:

* 安装 zsh

  ```shell
  sudo apt install zsh
  vim ~/.bashrc
  ```
* vim 打开 .bashrc 后，在最后一行加上以下代码

```shell
exec zsh
```

* 进行 zsh 的初始化，选 2 吧。

* 安装 oh-my-zsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

你熟悉的 zsh 主题和快捷命令又回来了！

![oh-my-zsh](images/set-up-development-environment-on-windows/oh-my-zsh.png)
## Editor&IDE
在此推荐两款编辑器，[VScode](https://code.visualstudio.com/) 和 [Atom](https://atom.io/)。

它们都是用 Electron 打造。VScode 适合喜欢开箱即用的人，非常方便；Atom 适合喜欢对编辑器进行 Hack 的人。你可以在官网上安装，也可以用 scoop 安装。
```shell
scoop bucket add extras # vscode 和 atom 都在 extras 库中，关于 scoop 中的 bucket 概念，参考: https://github.com/lukesampson/scoop/wiki/Buckets
scoop install vscode
scoop install atom
```

IDE 的话，也就 Visual Studio 和 Jetbrains 家族了吧，看你开发的语言平台了。讲真，有了 VScode，我可以很方便地获得类 IDE 的 js, ts, go, c#, python, rust 开发体验。IDE 都很少打开了。
## Font
最新的 Win10 系统对高分辨率的屏幕支持已经很好了。但一些第三方软件由于历史原因，还没有很好地适配。在使用这些第三方软件的时候，如果字体让你感到不舒服，请带上护目镜——使用额外的字体

我对字体没有额外的研究，在此就分享给大家一款对开发者友好的字体——[Meslo-Font](https://github.com/andreberg/Meslo-Font)

下载安装之后，第三方软件里配置一下字体即可。

最近发现 scoop 也可以安装字体！

```shell
scoop bucket add nerd-fonts # 字体在 nerd-fonts 库中，关于 scoop 中的 bucket 概念，参考: https://github.com/lukesampson/scoop/wiki/Buckets
scoop install sudo # 字体安装需要管理员权限
sudo scoop install hack-nf
```

重启你的电脑💻

## 最后
希望这篇博客可以帮助到 Windows 上的开发者，希望大家喜欢。所有的荣誉归于以上对 Windows 开发者社区做出巨大贡献的开发者们，对他们报以崇高的敬意。并以此为动力，为 Windows 社区做出更多的贡献。

这篇博客会保持更新，分享我在 Windows 上开发时使用的工具，力在提升 Windows 上的开发体验。谢谢大家。