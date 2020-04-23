---
title: 替换node版本
date: 2019-12-05 13:28:58
tags:
- node
description: node
---

## 替换node版本
科普文：使用 nvs 管理本地 Node.js 版本 <https://zhuanlan.zhihu.com/p/63403762>

nvs 命令执行报错：

```
nvs : 无法加载文件 C:\Users\Admin\AppData\Local\nvs\nvs.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go
.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1
+ nvs -v
+ ~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

解决方法： 以管理员身份打开 windows powershell ,执行 `get-ExecutionPolicy` ，如果命令返回的不是 **RemoteSigned**，执行命令 `set-ExecutionPolicy RemoteSigned`。

`npm get prefix` 命令可以获取 npm 安装的全局模块的路径。

nvs 默认不会管理未通过nvs下载的node版本。