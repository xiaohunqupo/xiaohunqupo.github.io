---
title: 在 Windows 和 tcmalloc 愉快的玩耍吧

tags: C/C++, tcmalloc

---

# 起始
都说使用`tcmalloc`可以有效的提高 `new` 和 `delete`的效率，所以我要尝试一下~~

了解前因后果，按顺序看。

简单有效直接看 **`gperftools`项目编译`tcmalloc`**。

# `tcmalloc` 的编译

## 获取`tcmalloc`代码
首先，我们找到`tcmalloc`的代码库，然后获取代码~~
``` bat
https://github.com/google/tcmalloc.git
```

## 尝试编译
看文档中的说明并未提到Windows怎么编译，估计会出意外。

按照说明，需要使用`Basel`编译，这是[官网]()，这是[bazel-7.1.2-windows-x86_64的下载连接](https://objects.githubusercontent.com/github-production-release-asset-2e65be/20773773/dce6a91b-cf12-4a08-9c87-3c49bf45af70?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20240509%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240509T035501Z&X-Amz-Expires=300&X-Amz-Signature=cecab7c4050793281b1b67e9feda2d0ca468a8262a2361e20519675cd8a80cdb&X-Amz-SignedHeaders=host&actor_id=9416734&key_id=0&repo_id=20773773&response-content-disposition=attachment%3B%20filename%3Dbazel-7.1.2-windows-x86_64.exe&response-content-type=application%2Foctet-stream)。

安装后按照说明编译吧~~

大概率不出意外的会出意外。。。

如果跟我一样出问题了看后面，没出问题应该就能得到lib吧，我猜的。

# 借助`gperftools`项目编译`tcmalloc`
gperftools这个工具包包含了很多项目，且默认提供`Visual Studio`解决方案，其中就包括了`tcmalloc`项目。

## 获取`gperftools`
在`gpreftools`项目[网站](https://github.com/gperftools/gperftools)中找到发布包，解压到本地即可。
 
## 编译项目
打开项目可以看到`libtcmalloc_minimal`项目，直接编译即可。

该项目Relase的配置，默认为静态链接库形式。

如果需要动态链接库的可以自己调整项目。

# 使用`tcmalloc`
调整项目属性
1. 添加链接库。在`项目`->`属性`->`连接器`->`输入`->`附加依赖项`中添加`libtcmalloc_minimal.lib`。
2. 添加强制符号引用。在`项目`->`属性`->连接器->输入->`强制符号引用`中添加`__tcmalloc`。

# 已知问题
在使用过程中，或了解过程中发现可能以下问题
1. 跨越dll的`new`和`delete`会崩溃。大概原因是使用静态链接库，每个dll内部都单独进行了内存管理，可以通过更换为动态连接库的tcmalloc解决，见[参考资料](https://stackoverflow.com/questions/14243186/has-anyone-been-able-to-integrate-tcmalloc-on-a-windows-64-bit-application-that)。
2. 尽量将`tcmalloc`应用在主程序上。经过同行传授经验:主程序用`tcmalloc`的情况下启动时，会第一时间加载tcmalloc的dll，并接管内存分配释放。以下也是同行传授经验，我这里并未实际验证。
   1. 主程序用`tcmalloc`，dll不用。主程序中`new`，dll中`delete`，是OK的。
   2. 主程序用`tcmalloc`，dll不用。dll中`new`，主程序中`delete`，是OK的。
   3. 主程序不用，dll用`tcmalloc`。主程序中`new`,dll中`delete`，是不OK的。
   4. 主程序不用，dll用`tcmalloc`。dll中`new`,主程序中`delete`，是不OK的。
3. 启用`tcmalloc`后，会增加3MB左右的内存占用。
4. 某些情况下，启用`tcmalloc`会比不启用时占用更多的内存，该状况原因暂时不明确。已经向作者提出问题。
   下面是我在一个项目中进行的测试：
   1. 