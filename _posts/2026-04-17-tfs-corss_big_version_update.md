---
title: tfs跨大版本升级

tags: TFS

---

# 前言
TFS在跨大版本升级时，会出现SqlServer版本更换要求。

如果直接进行升级，最后一定会卡在无法对低版本SqlServer访问，导致无法从旧项目迁移得问题。

其实要解决这个问题还是有办法得。

# 下文提到的工具
1. `SQL Server Management Studio` SQL Server管理工具，简称 `SSMS`。

# 卸载新版本TFS
卸载新版本TFS，再通过 `SMSS` 工具，检查新的数据库中是否有`Azure`开头的项目。如果有，说明之前升级后进行了额外的配置，需要手动删除这些库。

# 备份数据库
如果有备份得数据库这一步直接跳过。

## 旧版本TFS未卸载
直接进行在`TFS`管理工具中立即备份即可。

## 旧版本TFS已卸载
可以直接在 `SMSS` 中备份以下几个数据库：

1. `AzureDevOps_Configuration`数据库，这里面存储了TFS的系统信息。
2. `AZure_xxxxxx`数据库，这里面是按`Collection`存储的数据库。

# 还原数据库
这一步需要使用到备份生成的 `.mdf` 和 `.trn` 两个文件，这两个文件的文件名。

在 `SMSS` 中，新建一个SQL执行以下脚本，便可以还原一个数据库。

``` sql
-- 第1步：还原完整备份，必须 NORECOVERY（保持"正在还原"状态，才能继续追加）
RESTORE DATABASE [AzureDevOps_Configuration] 
FROM DISK = 'd:\a\AzureDevOps_Configuration_9862489827534757288F.bak'
WITH 
    MOVE 'AzureDevOps_Configuration' 
        TO 'C:\Program Files\Microsoft SQL Server\MSSQL17.MSSQLSERVER_2025\MSSQL\DATA\AzureDevOps_Configuration.mdf',
    MOVE 'AzureDevOps_Configuration_log' 
        TO 'C:\Program Files\Microsoft SQL Server\MSSQL17.MSSQLSERVER_2025\MSSQL\DATA\AzureDevOps_Configuration_log.ldf',
    NORECOVERY;  -- ← 关键：改成 NORECOVERY，数据库将处于"正在还原"状态

GO

-- 还原事务日志（按时间顺序，如果有多个则中间都用 NORECOVERY，最后一个用 RECOVERY）
RESTORE LOG [AzureDevOps_Configuration] 
FROM DISK = 'd:\a\AzureDevOps_Configuration_9862489844524537788L.trn'
WITH RECOVERY;  -- 最后一个日志必须用 RECOVERY，完成还原

GO
```

