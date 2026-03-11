---
title: 玩转CMake

tags: KDE,KDevelop

---

# 前言
今日需要将项目迁移到Linux上，那么首先要做的就是创建Linux上的项目啦~

这就要清楚大名鼎鼎的CMake。

由于网上的教程都是简单的小型项目示例，所以参考起来总是缺点东西。

我这里就用实际大型项目简化出来的项目作为，示例吧~



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