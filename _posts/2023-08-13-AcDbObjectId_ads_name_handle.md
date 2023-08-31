---
title: AcDbObjectId、指针、句柄和ads_name的区别

tags: C/C++, ObjectARX

---

转载自:[https://my.oschina.net/u/2930533/blog/759617](https://my.oschina.net/u/2930533/blog/759617)

# 概述
编译时可能会遇到如下信息:
访问实体的特性必须通过对象指针，但是一旦你获得了实体的ID、句柄或者`ads_name`，都能通过ID作中个而获得对象的指针。其中ID是一个桥梁。句柄是Windows编程一人常用的概念，在`ObjectARX`编程中一般指`AcDbHandle`类 (也可指Windows编程的界面元素)，该类封装了一个64位整形标识符，随`dwg`文件一同保存。`ads_name`则是在ADS编程中出现的一个概念，其实际上是一个二维数组，数组元素类型为长整型，在与用户交互的函数中经常用到。

# 特点
ID、句柄和 ads name 具有各自的特点:
1. `AcDbObjectId`: 当`dwg`图形被打开后数据库中的实体对象都会在内存中对应一个唯一的id(`AcDbObjectId`)。`AcDbObjectId`也是与对象相关联的唯一标识符，`AcDbObjectId`仅存在于其所存在的数据库从内存中产生到数据库被删除之间。如果操作多个`dwg`,`AcDbObjectId`在多个数据库之间都是唯的
2. `AcDbHandle`: `dwg`文件中每一个实体都有一个唯一的标识符,用AcDbHandle表示,在一个`AutoCAD`中，不能保证每个实体的句柄都唯一。在`AutoCAD`的两个`dwg`中同一实体的句柄是相同的实体的`AcDbHandle`可以随`dwg`文件被保存,所以即使`dwg`未被`AutoCAD`打开也可以根据句柄搜索`dwg`文件获取对象信息。
3. `AcDbObject`对象指针:当一个对象被打开后,`AutoCAD`会返回指向`AcDbObject`或其派生类型的应该c++指针,然后就可以采用标准c++类对象的方式对实体进行操作.
4. `ads_name`: `AutoCad`定义的一个数据类型一般用来保存用户交互过程中成功选择实体的节点ads name是不稳定的，仅当你在一旦退出`AutoCAD`或者切换到另一个图形，`ads_name`就会丢失。

# 转换
ID、指针、句柄和 ads name 之间具有下面的转换关系:
1. 从`AcDbObjectId`到`AcDbObject`对象: acdbOpenAcDbObject()或者 acdbOpenObject。
2. 从`AcDbObject`对象到`AcDbObiectld`: 所有的数据库常驻对象都继承自`AcDbObjectId`函数能获得所指向对象的ID。
3. 从`AcDbHandle`到`AcDbObjectId`: AcDbDatabase::getAcDbObjectId()。
4. 从`AcDbObjectId`到`AcDbHandle`: AcDbObjectld::handle()。
5. 从`AcDbObject`到`AcDbHandle`: AcDbObject::getAcDbHandle()。
6. 从`ads_name`到`AcDbObjectId`: 使用全局函数 acdbGetObjectId()。
7. 从`AcDbObjectId`到`ads_name`: 使用全局函数 acdbGetAdsName()。