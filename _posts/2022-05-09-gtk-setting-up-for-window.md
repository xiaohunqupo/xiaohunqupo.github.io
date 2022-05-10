---
title: windows 下安装 gtk

tags: C/C++,gtk
---

按照[官方网站]([The GTK Project - A free and open-source cross-platform widget toolkit](https://www.gtk.org/docs/installations/windows/))说明，有两种方法再windows安装GTK。

1. [MSYS2]([The GTK Project - A free and open-source cross-platform widget toolkit](https://www.gtk.org/docs/installations/windows/#using-gtk-from-msys2-packages))。`MSYS2`为windows提供了类似UNIX的开发环境。

2. [gvsbuild]([GitHub - wingtk/gvsbuild: GTK stack for Windows](https://github.com/wingtk/gvsbuild))。此方法基于gvsbuild库，该库提供了生成GTK技术栈的的脚本，可输出由`Visio Studio`和`Meson`项目使用的库和工具。

本文主要介绍`gvsbuild`方法。

# 安装说明

由于国内网络问题，按照`gvsbuild`中提供的安装说明需要依赖`Choco`包管理器，但在实际操作中将遇到各种网络问题。所以我才用纯粹手动安装所需库。

## 安装`Git`

访问[Git官网](https://git-scm.com/)，下载最新版本并安装即可。

## 安装`MSYS2`

访问[MSYS2官网]([MSYS2](https://www.msys2.org/))，下载最新版本并安装即可。

## 安装Visual Studio 2022

直接去官网下载安装包，安装即可。安装时必须在`工作负荷`中，勾选`使用C++的桌面开发`选项。

## 安装python

访问python官网，下载最新版本并安装即可。安装时，请务必选择添加到`Add to PATH`及`Disable path length limit`选项，否则需要额外的手动处理。

## Clone gvsbuild项目

clone项目到本地，示例如下:

```shell
mkdir C:\gtk-build\github
cd C:\gtk-build\github
git clone https://github.com/wingtk/gvsbuild.git
```

## 编译项目

进入项目目录并运行生成脚本`build.py`即可，示例如下:

```shell
cd C:\gtk-build\github\gvsbuild
python .\build.py build gtk4
```

等待下载及编译即可。 

因为网络波动，此步骤可能在下载文件时出现下载失败等状况，如果失败请重新尝试。

如果重新尝试时，提示某文件`hash`数据不正确，执行错误、主机无应答，请删除指定文件后，重新尝试。如果仍然失败，可依据提示信息中的地址，手动下载后放到`项目目录\src\`文件夹内。

## 生成目录

编译的默认生成目录为:

```shell
c:\gtk-build\gtk\x64\release
```

此步骤不是必须的，按照自己情况配置即可。

如果需要的话，可以在命令行中，执行如下语句:

```shell
$env:Path = "C:\gtk-build\gtk\x64\release\bin;" + $env:Path
```

# 结语

起始整个过程如果网络通常的话，按照项目内的说明，使用Choco安装，应该是很顺利的。不过现阶段环境就是这样，大家手动处理一下也是可以接受的。

祖国万岁。
