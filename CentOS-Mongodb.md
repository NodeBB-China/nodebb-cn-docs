> 服务器选用 64 位 CentOS，MongoDB 现在只有64位版本
# CentOS & MongoDB
[TOC]

## 一、准备
1. 关闭 SELinux

>[info] 有些主机商给你的 CentOS 可能不存在下面那个文件，那你可以直接跳过这步。
```
$ vim /etc/sysconfig/selinux
$ yum install vim #如果 vim 指令未安装，那就按照它
```
找到 `SELINUX` 然后修改成 `disabled`.
```
SELINUX = disabled
```
2. 更新系统并安装基础工具
```
$ yum update
$ yum -y groupinstall "Development Tools"
$ yum -y install git ImageMagick
```
## 二、安装
### §1. Node.js
>[info] `nvm`是一款优秀的Node.js版本管理软件，这里我们就使用它来安装 Node.js.
1. 首先，我们现在服务器上安装 `NVM` 
```
#截止2017.08.07,NVM最新版本为 v0.33.2
$ sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash 
```
***可以在 [Github](https://github.com/creationix/nvm/) 中获取 NVM 最新的安装指令*** 

2. 添加 NVM 到环境变量
```
$ export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```
3. 使环境变量的修改生效
```
$ source ~/.bashrc
```
4. 检查是否成功安装
```
$ command -v nvm
```
如果没有抛出任何错误信息，那么我们继续下面的步骤吧。（如果有，请到Github中寻找解决方案。）

-------------------------------

>[info] 如果你使用的机器是国内的话，建议使用以下代码让`NVM`使用淘宝镜像来下载源码(腾讯云的下载速度只有可怜的1x.kb/s...)

```
# 让NVM使用淘宝源
$ sudo export NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node
$ sudo export NVM_IOJS_ORG_MIRROR=http://npm.taobao.org/mirrors/iojs
```
5. 好，我们使用下面的代码来安装LTS版本
```
#安装Node.js LTS
$ sudo nvm install --lts
```  

6. 等待执行完成，如果使用`node -v` ，`npm -v`都能正确输出版本号的话，说明nodejs已经成功安装。

>[info] 在国内，NPM源的速度比较慢，可以使用`sudo npm config set registry http://registry.npm.taobao.org/`来将npm更换到国内的淘宝源
### §2. MongoDB

*  配置 MongoDB yum 源

1. 创建文件并编辑它

```
$ vim /etc/yum.repos.d/mongodb-org-3.4.repo
```
2. 把下面的内容复制进去
```
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```
3. 更新文件，安装
```
$ yum update
$ yum -y install mongodb-org
```
>[success] 如果您是国内服务器，那么这样安装 `MongoDB` 会很慢，可以将 repo 里面的链接换成阿里云源，
> `baseurl=http://mirrors.aliyun.com/mongodb/yum/redhat/6/mongodb-org/stable/x86_64/`

4. 关闭巨型页 

安装完成后，为了消除 MongoDB 的性能警告，需要关闭 Linux 巨型页：
```shell
$ echo never > /sys/kernel/mm/transparent_hugepage/enabled
$ echo never > /sys/kernel/mm/transparent_hugepage/defrag
```

5. 启动 MongoDB 服务
```
$ service mongod start
```
### §3. NodeBB
>[info] ***在开始之前:***
>**① 提前切换成root用户，免得麻烦。**
> **② 不要直接下载 Github 上发布的 release 压缩包**，那里面没有 `.git/config` 配置文件，而在安装过程中需要读取配置文件的值。正确的做法是使用 Git 从 GitHub 上克隆源代码。

1. 下载 NodeBB

切换到	`/home`目录(可替换为你想要的位置)，克隆源代码：
```
$ cd /home #切换到你想要的位置
$ git clone -b v1.6.x https://github.com/NodeBB/NodeBB.git nodebb
```
>[warning] 注意： `v1.5.x` 是最新的稳定版分支的名字，**不建议** 使用其他分支作为 **生产环境**！
> **2017.09.23** 更换 `v1.5.x` 分支 为新稳定分支 `v1.6.x`
2. 安装 NodeBB 的运行依赖
```
$ cd /home/nodebb # /home替换成你上面选取的位置
$ npm i --producation
```

3. 新建数据库(详细可查看 配置MongoDB 篇)
* 使用以下命令进入 MongoDB 的命令行管理界面：

```
$ mongo
```
* 新建数据库：

```
> use nodebb # 新建数据库，名叫 nodebb
```

* 添加用户角色：

```
> db.createUser( { user: "nodebb", pwd: "替换成你的密码", roles: [ "readWrite" ] } )
```
>[danger] NodeBB 需要 MongoDB 版本至少为 2.6

* 打开 MongoDB 权限认证
 执行 `vim /etc/mongod.conf`，找到 `security:` 这一行，取消注释并改为：
```
security:
   authorization: enabled
```
* 重启 MongodB

```
$ service mongod restart
```
4. 初始化 NodeBB
使用如下命令开始初始化：

```
$ ./nodebb setup
```
**注意事项：**

* `URL used to access this NodeBB` 这个问题填写域名http://www.xxx.com（别漏下 `http` 前缀，域名末尾不加 `/`）
* 另外，NodeBB会默认监听本地端口 `4567`，我们后面还要使用 `nginx` 转发 `80` 端口的请求到 `4567` 端口。
* 中间还有两次需要回答问题 `Which database to use`，
第一次是回答数据库程序名 `mongo`，第二次回答所创建的数据库名 `nodebb`
* 这些回答都将被写入 NodeBB 的配置文件config.json，如果答错了可以随后手动修改

**极度重要！**

* 注意第一个问题所回答的 URL 将会作为之后邀请链接的网址，我们设成了 `http://www.xxx.com`，那么邀请连接就是 `http://www.xxx.com/register`
* 同时注意末尾别加斜杠，否则邀请链接里多一个斜杠成了 `http://xxx//register`
* 也不要加端口，不然邀请链接里包含 `4567` 端口（`http://www.xxx.com:4567/register`），而4567端口是不对外开放的！

### §4. Nginx(反代)
上一步我们安装好了nodebb，但是需要通过`4567`端口才能访问。现在我们设置一下反代服务，支持通过域名访问网站。

>[info] 反代服务器 有很多种，但这里我们选取的是最流行也是性能最优秀的反代服务: Nginx

1. 执行`vim /etc/yum.repos.d/nginx.repo` 并写入以下信息：

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/6/x86_64/
gpgcheck=0
enabled=1
```

2. 开始安装：

```
$ yum install nginx.x86_64
```
3. 配置 Nginx

执行 `vim /etc/nginx/nginx.conf`，在 `http` 语句块内追加上：

```nginx
##########################################
server {
    listen 80;

    server_name www.xxx.com; # 你的域名

   location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://127.0.0.1:4567;
        proxy_redirect off;

        # Socket.IO Support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
    
    # 配置 502 页? 参考: 高级 - 配置 Nginx
}
##########################################
```
4. 启动 Nginx
```
$ service nginx start
```
>更详细的Nginx配置，可参考 **高级 - 配置Nginx** 篇。

## 三、完成

### §1. 启动

运行以下命令启动 NodeBB：

```
$ ./nodebb start
```
>[warning] 请检测是否能通过url访问你的社区，若不行，则看看哪一步出错了。

至此，你的论坛已经完成。
### §2. 高级管理
为了更好的管理 NodeBB 的运行，推荐使用 `forever` 这个工具自动后台运行 NodeBB。
1. 安装 forever

```
$ npm install forever -g # 这里必须加-g参数，不然可能装不上
```
>[info] 装完后 `forever` 后，其可执行程序位于 Node.js 安装目录的 `bin` 子目录里，我们此前已经把整个 `bin` 目录加入 `PATH` 变量了。

2. 管理 NodeBB 进程

* 后台启动 NodeBB：

```
$ forever start app.js
```

* 停止 NodeBB：

```
$ forever stop app.js
```

* 查看后台的 NodeBB：

```
$ forever list
```
> 参考:
> 1. https://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat/#install-mongodb-community-edition
> 2. https://docs.nodebb.org/en/latest/configuring/databases/mongo.html
> 3. https://docs.mongodb.org/manual/administration/configuration/#security-considerations
> 4. https://docs.nodebb.org/en/latest/configuring/proxies/nginx.html

-----------------------------
>[info] 编写: hao-lee
维护: a632079
审核: PA Team
最后更新: 2017.09.23