# 运行 NodeBB

> 本文章基于 NodeBB 官方文档  进行细节优化，更符合文档的排版。

在之前的章节中，我们已经掌握了 NodeBB 可执行文件 的基本指令。也知道我们该使用：
* `./nodebb start` 启动 NodeBB
* `./nodebb stop` 停止 NodeBB
* 你也可以使用 `npm start` 和 `npm stop` 来开关 NodeBB，他们和上面的指令是完全等效的。

----------------------------
>[warning] 特殊的启动方式:
> `node app.js` 启动单一进程的 NodeBB (使用该指令时只启动一个 NodeBB 进程)
> `node loader.js` 启动 进程池 的 NodeBB （只有使用该指令时，NodeBB 的端口池配置才会生效）
> **虽然上面的方法也能正常启动 NodeBB ，但我们依旧推荐使用 可执行文件 来操作NodeBB**

通过之前的学习，我们也知道上面的方法不能使 NodeBB 在开机时便自行启动。这时候，通过下面的方法，便能使你的 NodeBB 达到开机自启动的目的。

![](images/9E1870C29D14BFEB4A476C21D6A62D7D.jpg)

>[info] 首先，请确认你的 Mongodb/Redis 服务是开机自启动的。
```
# Mongodb / redis 开机自启
systemctl enable mongod
systemctl enable redis
```

## pm2
PM2 是一个带有负载均衡功能的 Node 应用的进程管理器。
当你要把你的独立代码利用全部的服务器上的所有 CPU，并保证进程永远都活着，0 秒的重载， PM2 是十分完美的。

>[info] 使用 pm2 时，我们无需再为 NodeBB 配置端口组

```
$ sudo pm2 start /path/to/nodebb/app.js --name NodeBB
```

## systemd
>[info] 记得把 `/path/to/nodebb` 和 `myuser` 替换成自己的 路径 和 用户名 哦！
> ***我们能通过 systemd 使 NodeBB 开机自启***

Ubuntu 的新版本 使用 systemd 来管理服务。下面是一个NodeBB 的 systemd 服务例子：
```
[Unit]
Description=NodeBB
Documentation=https://docs.nodebb.org
After=system.slice multi-user.target mongod.service

[Service]
Type=simple
User=nodebb

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=nodebb

WorkingDirectory=/path/to/nodebb
ExecStart=/usr/bin/env node loader.js --no-silent --no-daemon
Restart=always

[Install]
WantedBy=multi-user.target
```
>[warning] 记得让 NodeBB 目录的所有者和你填写的用户名是同一个。
> 附指令: `sudo chown -R username.usergroup /path/to/nodebb`

把例子修改并保存到 `/etc/systemd/system/nodebb.service`。然后，你就能通过以下指令控制它了:
```
systemctl start nodebb
systemctl stop nodebb
```
>[danger] 我们在 `ExecStart` 的后面加了两个标志，他们的作用是:
> *  `--no-silent`：确保将日志发送到stdout（在这种情况下，您可以通过运行 `journalctl -u nodebb` 查看日志输出)
> * `--no-daemon` ：确保在主线程中运行，不产生任何分支。

> 加入开机自启动
```
systemctl enable nodebb
```

想要了解更多有关配置 systemd 的信息？请参考：[systemd 帮助页](https://www.freedesktop.org/software/systemd/man/systemd.service.html)

## Upstart
在 Ubuntu 的较早版本中，在启动时它会利用 `Upstart` 来管理进程。值得一提的是： 当它们崩溃时，`Upstart` 还会重新启动脚本。
您可以使用 `Upstart` 启动/停止/重新启动 NodeBB。

>[danger] 目前 CentOS7 / Ubuntu 16.04 都使用 systemd 管理服务，所以该节仅适合低于该版本的系统。 
-----------
>[warning]注意：在 NodeBB v0.7.0 之前，Upstart 进程不能获得正确的 pid，这意味着没有办法停止 NodeBB 的进程。NodeBB v0.7.0 包含一些允许 Upstart 更有效地控制 NodeBB 的更新。

您可以对下方的 `Upstart` 配置进行简单修改，然后就可以投入使用。
```
start on startup
stop on runlevel [016]
respawn
setuid someuser
setgid someuser
script
    cd /path/to/nodebb
    node loader.js --no-silent --no-daemon
end script
```
若你是 Root 用户 并且 假定`nodebb.conf` 是 upstart 的配置文件的名字的话，可以通过: `start nodebb`，`stop nodebb`，`restart nodebb`  启动/停止/重启 NodeBB 。

下面的两个都是能够帮助 NodeBB 在出现异常后能帮助它自动重启的程序，十分实用。
## Supervisor
使用 [supervisor 包](https://github.com/isaacs/node-supervisor)，你能在 NodeBB 异常时，自动重启 NodeBB：
```
$ npm install -g supervisor
$ supervisor app
```
缺省情况下，`supervisor` 持续将输出通过管道发给 `stdout`，它最适合开发版本。

## Forever
另一个保持 NodeBB 运行的方法，是使用 [forever 包](https://github.com/nodejitsu/forever) 。通过命令行接口，forever 可以监视 NodeBB，在需要的时候重启 NodeBB：
```
$ npm install -g forever
$ forever start app.js
```

## Grunt (适用于开发)
我们可以利用 Grunt 来启动 NodeBB 并在 有文件改动时重新编译资源。因为不重新编译未改动的文件，所以启动速度会快很多。
```
$ npm install -g grunt-cli
```
运行 Grunt 以便 启动 NodebB 并监听文件变动
```
$ grunt
```

>[info] 编写: NodeBB Development Team
维护: a632079
审核: PA Team
最后更新: 2018.01.20