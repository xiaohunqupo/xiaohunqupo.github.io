---
title: 小心临时字符串std::string

tags: C/C++

---

# 前言
实际开发时发现有个地方函数执行的比较奇怪，例如：
``` c++
int main()
{
    const char* str1 = std::string("A").c_str();
    for(int i = 0 ;i < 100; ++i)
    {
        const char* str2 = std::string("B").c_str();
        
        // do something
        // str1 指向的值会变为 "B"

    }
}
```

# 原因
发生这个问题的原因是表达式中的`std::string("A")`在当前行执行结束时就已经释放了。

变量str1虽然看起来有值，但实际上是指向了已经被释放的内存。

所以当再次有内存申请，这个地方的数据将会被覆盖。

# 参考资料
这篇文章写的更细致些，我就不再复制了。[如何在c语言引用string,C++ 为什么在返回字符串的函数上调用std :: string.c_str()不起作用？](https://blog.csdn.net/weixin_32101377/article/details/117145804)
