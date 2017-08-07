>[info] 手头没有 FreeBSD 的机器，所以我们就直接照搬官方的文档了。 欢迎 pullrequest 更新本教程！
# FreeBSD
>[warning] 本指南适用于FreeBSD 10.1-release。 对于所有其他相对现代的版本，在局部可能会有变动。

运行下面的指令，确保系统是最新的：
```
$ freebsd-update fetch
$ freebsd-update install
```
安装 `Redis`
```
$ pkg install redis
```
按照常规步骤以运行级别运行Redis，并启动它。
安装 `gcc5`:
```
$ pkg install gcc5
```
安装 `Node.js`
```
$ pkg install node
```
克隆NodeBB repo（假设你已安装 `git`，否则请使用`pkg install git`来安装它）：
```
$ git clone -b v1.5.x https://github.com/NodeBB/NodeBB.git
```
进入 NodeBB 目录 并安装依赖
```
$ cd nodebb
$ npm install --producation
```
运行交互式安装脚本：
```
$ ./nodebb setup
```
您可以将所有选项作为默认值。

>[info] 目前数据库默认为: Mongodb,在安装时请选择 Redis. 如果您想使用 MongoDB，请参考 配置数据库部分 或 参考 Mongodb 官方文档

你完成了 安装后，运行：

```
$ ./nodebb start
```
访问您的论坛`http:// yourdomain:4567/`完成配置和设置。 yourdomain 是你设定的域名。

>[info] 编写: NodeBB Development Team
维护: a632079
审核: PA Team
最后更新: 2017.08.07