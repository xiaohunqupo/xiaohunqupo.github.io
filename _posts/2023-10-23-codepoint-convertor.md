---
title: CodePoint 转换为 utf-8

tags: C/C++, boost

---

转载自 [c++ - 如何将代码点转换为 utf-8？](https://www.coder.work/article/1202723)

# 目的

我有一些代码读取 unicode 代码点(在字符串 0xF00 中转义)。

自从我使用 boost ，我推测以下是否是最佳(和正确)方法:
``` c++
unsigned int codepoint{0xF00};
boost::locale::conv::utf_to_utf<char>(&codepoint, &codepoint+1);
```

# 最佳答案

您可以使用 std::wstring_convert 对标准库执行此操作将 UTF-32(代码点)转换为 UTF-8:

``` c++
#include <locale>
#include <codecvt>

std::string codepoint_to_utf8(char32_t codepoint) {
    std::wstring_convert<std::codecvt_utf8<char32_t>, char32_t> convert;
    return convert.to_bytes(&codepoint, &codepoint + 1);
}
```

这将返回一个 std::string，其大小为 1、2、3 或 4，具体取决于 codepoint 的大小。如果代码点太大(> 0x10FFFF，最大 unicode 代码点)，它将抛出 std::range_error。

你的 boost 版本似乎在做同样的事情。 The documentation表示 utf_to_utf 函数将 UTF 编码转换为另一种编码，在本例中为 32 到 8。如果您使用 char32_t，这将是一种“正确”的方法，它将在 unsigned int 与 char32_t 大小不同的系统上工作。
``` c++
// The function also converts the unsigned int to char32_t
std::string codepoint_to_utf8(char32_t codepoint) {
    return boost::locale::conv::utf_to_utf<char>(&codepoint, &codepoint + 1);
}
```
# 其他

关于c++ - 如何将代码点转换为 utf-8？，我们在Stack Overflow上找到一个类似的问题： https://stackoverflow.com/questions/56341221/
