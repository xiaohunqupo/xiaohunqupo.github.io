---
title: pagefiles.sys所在盘缓慢导致系统卡顿
tags: Windows pagefiles.sys
---

`pagefiles.sys`为虚拟内存的分页文件。

由于内存中的空余空间可能不是连续的，所以为了保证有连续可用的足够大的内存空间，系统会通过该文件进行数据交换。此外由于部分应用程序会使用到虚拟内存，所以不建议直接关闭。

那么在`pagefiles.sys`文件所在盘的速度及工作状况将会影响系统在交换内存是的速度，也是磁盘缓慢导致系统卡顿的主要原因。

如果发现在非系统盘有繁重工作时，出现系统卡顿，那通常是由于`pagefiles.sys`文件在那个盘导致的。可以通过修改`虚拟内存` 设置，解决卡顿问题。

# 虚拟内存设置

![Untitled](\images\2022-03-25-windows-slow-pagefiles\1.png)

![Untitled](\images\2022-03-25-windows-slow-pagefiles\2.png)

![Untitled](\images\2022-03-25-windows-slow-pagefiles\3.png)

![Untitled](\images\2022-03-25-windows-slow-pagefiles\4.png)

# 磁盘队列长度

磁盘队列长度是指磁盘读写任务的工作状况，长度可参考下表：

| 平均长度    | 说明           |
| ------- | ------------ |
| 小于等于0.5 | 正常读写操作       |
| 小于等于1   | 磁盘频繁读写       |
| 小于等于2   | 磁盘基本         |
| 大于2     | 磁盘任务繁重或磁盘有坏块 |
