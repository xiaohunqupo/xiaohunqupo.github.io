---
title: C++模板嵌套问题

tags: C/C++

---

# 前言
在编译一个项目的代码时，发生了编译错误`error C3878`，简化的代码如下：
``` c++
template<typename T>
void Test()
{
	std::list<T*>::iterator t;
}
```

# 问题分析

上面的简化代码，看起来是没有问题的，即使用AI分析也不会有错误。

实际上上面这段代码在`VC140`及更早版本中是没问题的。

但是后续版本中，编译要求更严格了。

在`VC141`、`VC142`VC下编译，会得到两条错误，如下：
```
error C2760: 语法错误: 意外的令牌“标识符”，预期的令牌为“;”
error C7510: “iterator”: 类型 从属名称的使用必须以“typename”为前缀
```

在`VC143`下编译，错误信息如下：
```
error C3878: syntax error: unexpected token 't' following 'expression'
```

从上面的信息差异可以看出，导致高版本编译错误的主要原因是`C++`标准的变动。

具体细则我暂时还没去查找，但大致可以推断是因为对模板的顶要求更严格了。

# 解决方法
已经知道问题的原因了，参考`VC141`的错误信息，明确模板对象的嵌套关系，在嵌套的对象声明处加上typename即可。

修改后代码如下：
``` c++
template<typename T>
void Test()
{
	typename std::list<T*>::iterator t;
}
```