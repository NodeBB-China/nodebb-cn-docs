>[success] 这里是 NodeBB 使用中 可能会碰到的 问题和解决方案 篇
> 其中部分来自 NodeBB中国 交流群 中成员所碰到的问题

# NodeBB 问题和解决方案 
[TOC]
## NodeBB 官方 Q&A
### 如何启动/停止/重启NodeBB？
您可以调用 `./nodebb` 来启动和停止NodeBB：
```
$ ./nodebb

Welcome to NodeBB

Usage: ./nodebb {start|stop|reload|restart|log|setup|reset|upgrade|dev}

        start   Start the NodeBB server
        stop    Stops the NodeBB server
        reload  Restarts NodeBB
        restart Restarts NodeBB
        log     Opens the logging interface (useful for debugging)
        setup   Runs the NodeBB setup script
        reset   Disables all plugins, restores the default theme.
        activate        Activate a plugin on start up.
        plugins List all plugins that have been installed.
        upgrade Run NodeBB upgrade scripts, ensure packages are up-to-date
        dev     Start NodeBB in interactive development mode
```
### 如何将NodeBB更新到较新的版本？
请参考 更新 NodeBB
### 我升级了NodeBB，现在 X 不能正常工作！
请参考 更新 NodeBB
### 我安装了一个不兼容的插件，现在我的论坛不能启动！
如果您知道哪个插件导致问题，请通过运行 `./nodebb reset -r nodebb-plugin-pluginName` 禁用它 。
否则，通过运行以下命令禁用所有插件：` ./nodebb reset -p`
### 是否可以通过FTP安装NodeBB？
可以使用 FTP 将文件传输到远程服务器，但是您需要对服务器进行 shell 访问才能在实际上“启动”NodeBB。
附：[在DigitalOcean上安装NodeBB的便捷指南](http://burnaftercompiling.com/nodebb/setting-up-a-nodebb-forum-for-dummies/)
### 我得到一个“npm ERR！” 错误
在大多数情况下，涉及npm的错误是由于Node.js过时。如果在运行时看到类似于此的错误 `npm install`：





>[info] 编写: NodeBB-China
维护: PA Team
审核: PA Team
最后更新: 2017.08.08