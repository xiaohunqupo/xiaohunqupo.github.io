---
title: 玩转kdevelop

tags: KDE,KDevelop

---

# 前言


# 安装

## Windows版本
能安装，但是缺少文件`Qt6Core5Compat.dll`，复制官方较新的Qt6.8.1中的对应文件到程序目录中，可以正常启动。

但是在创建项目及打开项目时会死机。

## Linux版本

### 使用AppStore安装
呃安装失败

### 使用命令行安装
执行以下命令:
```
sudo apt-get install kdevelop
```
安装后，直接执行`kdevelop`即可启动。

### 注意1
如果启动后出现如下提示信息：
```
kdevelop error:Could not load project management plugin KDevCMakeManager
```

说明未安装CMake，执行以下命令安装:
```
sudo apt-get install cmake
```

### 注意2
如果编译`Qt5`项目时，出现以下错误信息:
```
package configuration file "Qt5Qml" not found
```

这是由于当前`KDevelop`编译依赖的`Qt5`环境不完整，执行以下命令安装`Qt5`：
```
sudo  apt-get install qtdeclarative5-dev
```

### 注意3
如果编译`Qt6`项目时，出现以下错误信息:
```
Could not find a configuration file for package "ECM" that is compatible with requested version "6.0.0".
```

说明当前的`ECM(extra-cmake-modules)`版本不够，执行以下命令进行安装最新版本:
```
git clone https://invent.kde.org/frameworks/extra-cmake-modules.git 
cd extra-cmake-modules
mkdir build && cd build    
cmake ..
make && sudo make install
```

## 注意4
如果编译`Qt6`项目时，出现以下错误信息:
```
Could not find a configuration file for package "ECM" that is compatible with requested version "6.0.0".
```

```
sudo apt-get install qt6-base-dev
```