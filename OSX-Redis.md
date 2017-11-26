>[info] 手头没有  MacBook/iMac ，所以我们就直接照搬官方的文档了。 欢迎 pullrequest 更新本教程！
# OSX Mavericks
## 需要的软件
首先，安装以下程序：
* http://nodejs.org/
* http://brew.sh/
## 安装 NodeBB
用 `homebrew` 安装 Redis
```
$ brew install redis
```
在你的终端中输入，启动 Redis:
```
$ redis-server
```
克隆 NodeBB 仓库:
```
$ git clone -b v1.7.x https://github.com/NodeBB/NodeBB.git
```
>[info] 现在，你可以使用国内的镜像仓库(`https://gitee.com/NodeBB-CN/NodeBB.git`)来提高连接速度。

---------------

>[success] **2017.11.26** 更换 `v1.6.x` 分支 为新稳定分支 `v1.7.x`

进入目录
```
$ cd NodeBB
```
安装依赖
```
$ npm install --producation
```
运行交互式安装脚本
```
$ ./nodebb setup
```
您可以将所有默认选项作为你的最终选择。除了“要使用哪个数据库（mongo）”，您应该使用“redis”
在你安装完毕后，运行:
```
$ ./nodebb start
```
你可以通过 `http://localhost:4567/`访问你的论坛

>[info] 编写: NodeBB Development Team
维护: a632079
审核: PA Team
最后更新: 2017.11.26