# 更新 MongoDB
>[info] 本章节包含着自本文档创建以来所涉及的所有 MongoDB 发行版更新

[TOC]

## 3.4 更新至 3.6
>[warning] MongoDB 3.6 尚未在 MacOS 新推出的文件系统 APFS 中进行测试，可能会导致错误。

本教程将指导如何将 `mongod` 包 正确从 3.4 升级至 3.6。
如果您需要更新其他的包，请参阅: https://docs.mongodb.com/manual/release-notes/3.6/

>[danger] 在尝试升级之前，请先熟悉本文的内容。
>
### 升级建议和检测清单
升级时，请检测以下几点：
* **升级版本路径**
您必须运行 3.4.x 版本，才能将现有的 MongoDB 升级至 3.6
如果您运行早于 3.4.x 的版本，您必须将现有版本升级至 3.4.x 才能升级至 3.6
* **准备**
在开始升级之前，请参阅 MongoDB 3.6文档 中的 [兼容性更改](https://docs.mongodb.com/manual/release-notes/3.6-compatibility/) ，以确保您的应用程序与 MongoDB 3.6 兼容。 
在开始升级之前，请 **解决** 应用程序部署中的不兼容问题。
将升级部署到生产环境之前，请在暂存环境中 **测试** 您的应用程序部署。
* **降级考虑**
如果在此之前已经升级至 3.6 的测试版本，我们建议您降级至 3.4 的最新补丁版本。
* **默认绑定到本地主机**
从 MongoDB 3.6 开始，`mongod` 和 `mongo` 实例默认绑定 `localhost`。 远程客户端无法连接到仅绑定到本地主机的实例。 要覆盖和绑定到其他IP地址，请使用配置文件(`/etc/mongodb.conf`)中的 `net.bindIp` 设置 或 使用 `--bind_ip` 命令行选项 指定一个IP地址列表。
如果希望远程客户端连接到您的实例，升级过程将要求您指定 `net.bindIp` 设置。

>[danger] 绑定到其他IP地址之前，请考虑启用访问控制或其他安全措施，以防止未经授权的访问。
### 准备
3.4 实例必须已把 `featureCompatibilityVersion` 设置为 `3.4`。要检测设置，请输入：
```
> db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )
```
返回的结果应该包含 `"featureCompatibilityVersion": "3.4".`
如果没有，那么请 更新 或 设置 `featureCompatibilityVersion`：
```
> db.adminCommand( { setFeatureCompatibilityVersion: "3.4" } )
```
### 更新 Mongodb 程序
将 Mongodb Repos 的地址中的 3.4 更换为 3.6，执行：
```
$ sudo apt update && sudo apt upgrade #Debian/Ubuntu
$ sudo yum update #REHL/CentOS
```
### 更新步骤
1. **将 3.4 二进制文件替换为 3.6**
由于我们是从包管理器中更新的 MongoDB，我们只需执行：`sudo systemctl restart mongod`，即可切换至 3.6.
2. **启用向后不兼容的 3.6 特性**
在这步，你可以运行 3.6 而不启用其版本特性以兼容 3.4
要启用这些3.6功能，请将功能兼容性版本设置为 3.6。
```
> use admin
> db.adminCommand( { setFeatureCompatibilityVersion: "3.6" } )
```
完成！

>[info] 编写: a632079 & MongoDB Docs
维护: PA Team
审核: PA Team
最后更新: 2018.01.20