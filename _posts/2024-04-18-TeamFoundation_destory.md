---
title: TeamFoundation 销毁或永久删除文件

tags: C/C++, TeamFoundation

---

# 命令
使用`tf destroy`命令销毁或永久删除 Team Foundation 版本控制 (TFVC) 中的版本控制的文件。

``` bat
tf destroy [/keephistory] <itemspec1>[;<versionspec>][<itemspec2>...<itemspecN>] [/stopat:<versionspec>] [/preview] [/startcleanup] [/noprompt] [/silent] [/login:username,[password]] [/collection:TeamProjectCollectionUrl]
```

# 示例

``` bat
tf destroy $/Thirdparty/assimp/5.0.x /login:test,password /collection:http://ados/DefaultCollection /startcleanup
```

# 参考文献
[MSDN - Destroy 命令（Team Foundation 版本控制）](https://learn.microsoft.com/zh-cn/azure/devops/repos/tfvc/destroy-command-team-foundation-version-control?view=azure-devops&source=recommendations)
