---
title: ObjectARX开发问题汇总
tags: ObjectARX
---

# ObjectARX开发问题汇总

# 奇怪问题

### `COPY`命令

`copy`命令的实现，看起来是依赖实体的`deepclone` 接口，但是与开发者直接`deepClone`又有所区别。在`copy`命令调用过程中，`deepclone`函数内向块表添加克隆后的的对象后，`deepclone`结束后无法子实体以及目标块表是无法正确触发反应器的(大概还有很多类型的反应器也都无法触发)。

可在命令反应器，命令结束时，重新处理子实体与父实体关系。
