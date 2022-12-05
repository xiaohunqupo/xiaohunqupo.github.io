---
title: 中望CAD开发指南

tags: C/C++,中望CAD
---
# 开发环境配置

## 系统

系统的选择可以有很多ubuntu、中标麒麟、UOS都可以，自行安装即可。

## 开发环境

### GCC

编译器采用gcc进行编译。

安装方法如下，因为都是基于ubuntu的系统，基本默认安装了apt包管理器。

执行以下命令安装`gcc`：

```shell
sudo apt install gcc
```

安装后，可以查看gcc版本：

```shell
gcc -v
```

## Visual Studio Code

IDE选择使用`VSCode`，可在商店里搜索安装或在官网下载安装。

安装后，可在扩展页面安装中文包，方便使用。

同时建议安装`C/C++ Extension Pack`。这个是工具集合，包含了代码提示、代码高亮、CMake语法高亮等`C++`开发常用工具。

# CMake

CMake是工程生成工具，可在商店里搜索安装或在官网下载安装。


