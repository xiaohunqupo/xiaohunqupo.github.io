---
title: _USRDLL _AFXDLL _WINDLL 三种dll编译宏的具体含义

tags: C/C++, MFC

---

转载自 [_USRDLL _AFXDLL _WINDLL 三种dll编译宏的具体含义](https://www.cnblogs.com/laohaozi/p/12537582.html)

# 

常见的编译参数VC建立项目时总会定义`Win32`。控制台程序会定义`_CONSOLE`，否则会定义`_WINDOWS`。
`Debug`版定义`_DEBUG`，`Release`版定义`NDEBUG`.
与MFC DLL有关的编译常数包括：
`_WINDLL` 表示要做一个用到MFC的DLL
`_USRDLL` 表示做一个用户DLL（相对MFC扩展DLL而言，静态）
`_AFXDLL` 表示使用MFC动态链接库的 regular DLL(动态)
`_AFXEXT` 表示要做一个MFC扩展DLL

所以：
Regular, statically linked to MFC
```
_WINDLL,_USRDLL
```

Regular, using the shared MFC DLL
```
_WINDLL,_USRDLL,_AFXDLL
```

Extension DLL
```
_WINDLL,_AFXDLL,_AFXEXT
```

