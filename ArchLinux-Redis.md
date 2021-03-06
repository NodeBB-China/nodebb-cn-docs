>[info] 手头没有 Arch Linux的机器，所以我们就直接照搬官方的文档了。 欢迎 pullrequest 更新本教程！
# Arch Linux
首先，我们安装基础软件。 确保 `pacman -Syu` 已与存储库同步，并且所有其他软件包都是最新的。

目前 Node.js 的 LTS 分支为: `carbon` (v8.x)
```
$ sudo pacman -S git nodejs-lts-carbon npm redis imagemagick icu yarn
```
如果要使用MongoDB，LevelDB或其他数据库而不是Redis，请查看“配置数据库”部分。同时，请将上方的 redis 替换为 `mongodb`
下一步，克隆这个仓库:
```shell
$ git clone -b v1.9.x https://github.com/NodeBB/NodeBB.git nodebb
```
>[info] 现在，你可以使用国内的镜像仓库(`https://gitee.com/NodeBB-CN/NodeBB.git`)来提高连接速度。

进入 NodeBB 目录：
```
$ cd nodebb
```
通过运行带有`setup`标志的脚本来启动安装程序：
```
$ ./nodebb setup
```
默认设置是在默认端口上运行的本地服务器 和 同一机器/端口上的Mongodb数据库。
最后，我们运行论坛。
```
$ ./nodebb start
```
NodeBB也可以用辅助程序启动，如`supervisor`和`forever`。 你可以点击这里，来了解他们。

>[info] 编写: NodeBB Development Team
维护: a632079
审核: PA Team
最后更新: 2018.05.05