---
title: Windows 和 Linux 交叉编译

tags: KDE,KDevelop

---

# 前言
通过CMake设置好项目后，设置编译工具链。

在Windows上生成Linux项目的可执行文件或库，需要使用交叉编译工具链。以下是几种常见的方法和工具，可以帮助你在Windows上进行Linux项目的编译：
1. 使用MSYS2进行交叉编译


[MSYS2](https://www.msys2.org)是一个基于MinGW-w64的工具，提供了一个类Unix环境和完整的交叉编译工具链，支持在Windows上编译Linux项目。
步骤
安装MSYS2：
从MSYS2官网下载安装包并安装。
安装完成后，启动MSYS2 Shell。
更新MSYS2：
bash
复制
pacman -Syu
安装交叉编译工具链：
安装MinGW-w64工具链：
bash
复制
pacman -S mingw-w64-x86_64-toolchain
配置交叉编译环境：
创建一个工具链文件（如toolchain-linux.cmake），指定交叉编译工具链：
cmake
复制
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_C_COMPILER x86_64-w64-mingw32-gcc)
set(CMAKE_CXX_COMPILER x86_64-w64-mingw32-g++)
使用CMake进行编译：
在CMake中指定工具链文件：
cmake
复制
cmake -DCMAKE_TOOLCHAIN_FILE=toolchain-linux.cmake ..
make
1. 使用MinGW-w64
MinGW-w64是一个跨平台的C/C++编译器，支持在Windows上编译Linux项目。
步骤
安装MinGW-w64：
在Windows上安装MinGW-w64。可以从MinGW-w64官网下载安装包。
配置环境变量：
将MinGW-w64的bin目录添加到系统环境变量PATH中。
创建工具链文件：
创建一个工具链文件（如toolchain-linux.cmake）：
cmake
复制
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_C_COMPILER x86_64-w64-mingw32-gcc)
set(CMAKE_CXX_COMPILER x86_64-w64-mingw32-g++)
使用CMake进行编译：
在CMake中指定工具链文件：
cmake
复制
cmake -DCMAKE_TOOLCHAIN_FILE=toolchain-linux.cmake ..
make
1. 使用WSL（Windows Subsystem for Linux）
WSL允许你在Windows上运行Linux发行版，从而使用Linux的工具链进行编译。
步骤
安装WSL：
在Windows中安装WSL。可以通过Microsoft Store安装Ubuntu或其他Linux发行版。
安装交叉编译工具链：
在WSL中安装交叉编译工具链：
bash
复制
sudo apt update
sudo apt install gcc-x86-64-linux-gnu g++-x86-64-linux-gnu
配置工具链文件：
创建一个工具链文件（如toolchain-linux.cmake）：
cmake
复制
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_C_COMPILER x86_64-linux-gnu-gcc)
set(CMAKE_CXX_COMPILER x86_64-linux-gnu-g++)
使用CMake进行编译：
在CMake中指定工具链文件：
cmake
复制
cmake -DCMAKE_TOOLCHAIN_FILE=toolchain-linux.cmake ..
make
1. 使用Cygwin
Cygwin是一个在Windows上提供类Unix环境的工具，支持交叉编译。
步骤
安装Cygwin：
从Cygwin官网下载安装包并安装。
安装交叉编译工具链：
在Cygwin中安装交叉编译工具链：
bash
复制
setup-x86_64.exe -q -P gcc-g++-x86_64-linux-gnu
配置工具链文件：
创建一个工具链文件（如toolchain-linux.cmake）：
cmake
复制
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_C_COMPILER x86_64-linux-gnu-gcc)
set(CMAKE_CXX_COMPILER x86_64-linux-gnu-g++)
使用CMake进行编译：
在CMake中指定工具链文件：
cmake
复制
cmake -DCMAKE_TOOLCHAIN_FILE=toolchain-linux.cmake ..
make
总结
在Windows上生成Linux项目的可执行文件或库，可以通过以下几种方法实现：
使用MSYS2和MinGW-w64进行交叉编译。
使用WSL（Windows Subsystem for Linux）。
使用Cygwin。
根据你的需求选择合适的工具和方法。



# 安装
太简单了，省略。

# CMake说明

## 简介
在项目文件夹内部，`CMake`的配置是通过`CMakeLists.txt`进行配置的。
子项目的配置通常是通过文件夹层级来维护的，这就意味着子文件夹中也需要有自己的`CMakeLists.txt`文件。

为了方便描述，下文件假设当前的软件叫`RX`，拥有如下3个项目:
1. `System` 静态链接库，产生`lib`。
2. `Interface` 动态链接库，依赖`System`，产生`.dll`和`.lib`。
3. `Main` 主程序，依赖`System`和`Interface`。

## 目录组织如下
```
RX
|- CMakeLists.txt
|- Project
    |- System
        |- CMakeLists.txt
        |- stdafx.h
        |- system.cpp
    |- Interface
        CMakeLists.txt
        |- stdafx.h
        |- system.cpp
    |- Main--
        |- CMakeLists.txt
        |- stdafx.h
        |- main.cpp


```

## 根目录下的`CMake`配置

``` cmake
# CMake程序的版本要求，如果不满足，运行时会提示错误
cmake_minimum_required(VERSION 3.0)

####################################
# 项目名称
####################################
project(RX)



```