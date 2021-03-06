>[info] **该文章是 PA Team 安装NodeBB中文社区时使用的方法**
# Ubuntu/Debian
[TOC]

## 一、 配置环境
### 1.1 Node.js
Node.js 是由 Chrome JavaScript V8 引擎衍生出的一种高性能，高并发，易扩展的框架。
NodeBB也正是因为它，才具有了快捷，高效的特性。

* 截止目前，NodeBB 要求 Node.js 的最低版本为 v8.x 

>[success] 目前，推荐使用 Ubuntu/Debian 包管理器 来快速安装nodejs。
#### 1.1.1 使用 Ubuntu/Debian 包管理器来安装 （推荐）
>[info] 不同于 nvm ，我们推荐您在非 **`ROOT`** 用户下进行下列操作（直到教程结束）。

###### **使用 Nodejs 的快速配置脚本**
```
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

---------
如果是位于大陆的服务器，我们强烈推荐您执行下面的操作 :
* 编辑 Node.js 源
```
$ sudo vim /etc/apt/sources.list.d/nodesource.list  
```
使用下方的源来代替源内的 URL

>  `https://mirrors.ustc.edu.cn/nodesource/deb/node_{版本号，例如: 8}.x`
* 更新 APT 源
```
$ sudo apt-get update
```

---------

通过 APT 安装
```
$ sudo apt-get install -y nodejs
```
检查 Node.js 版本
```
$ node -v
```
###### **补充: 安装 `yarn`**
>[info] 本步骤仅为有需要的童鞋使用，不是必要步骤。

若你不是通过 包管理器 安装 nodejs 的话，建议您使用下面的指令来安装 `yarn`
```
$ curl -o- -L https://yarnpkg.com/install.sh | sudo bash
```
* 通过包管理器 安装 `yarn`

配置 Yarn 源
```
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```
更新 APT 源 并 安装 Yarn
```
$ sudo apt-get update
$ sudo apt-get install yarn
```
检查 Yarn 版本
```
$ yarn -v
```
>[info] 有关 Yarn 安装的更多玩法，可以通过 **高级 - 安装 Yarn** 了解。
#### 1.1.2 使用 NVM 来安装 Node.js LTS  （适用于多版本 Node.js 切换，共存）
>[warning] 我们建议您使用 **`Root`** 用户来进行下面的安装操作。 这样会减少很多不必要的麻烦!
>在腾讯云亲测:**使用默认的ubuntu用户来安装比使用Root麻烦十倍 <- 主要是`sudo`并不能载入你的环境配置,so...**)
* 首先，我们现在服务器上安装 `NVM` 
```
#截止2018.01.20,NVM最新版本为 v0.33.8
$ sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash 
```
***可以在 [Github](https://github.com/creationix/nvm/) 中获取 NVM 最新的安装指令*** 
* 添加 NVM 到环境变量
```
$ export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```
* 使环境变量的修改生效
```
$ source ~/.bashrc
```
* 检查是否成功安装
```
$ command -v nvm
```
如果没有抛出任何错误信息，那么我们继续下面的步骤吧。（如果有，请到Github中寻找解决方案。）
* 使用 NVM 安装 Node.js 的 LTS 版本  

***如果你使用的机器是国内的话，建议使用以下代码让NVM使用淘宝镜像来下载源码(腾讯云的下载速度只有可怜的1x.kb/s...)***
```
$ sudo vim ~/.bashrc
```
将下方的代码放置到最下方
```
#让NVM使用淘宝源
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
export NVM_IOJS_ORG_MIRROR=https://npm.taobao.org/mirrors/iojs
```
使其生效
```
$ source ~/.bashrc
```
好，我们使用下面的代码来安装LTS版本
```
#安装Node.js LTS
$ sudo nvm install --lts
```  

等待执行完成，如果使用`node -v` ，`npm -v`都能正确输出版本号的话，说明nodejs已经成功安装。

>[info] 在国内，NPM源的速度比较慢，可以使用`sudo npm config set registry https://registry.npm.taobao.org/`来将npm更换到国内的淘宝源

----
#### 1.1.3 使用源码编译安装Node.js (已不再推荐)
首先，我们要去Node.js官网获得源代码文件`https://nodejs.org`。
我们推荐使用LTS作为NodeBB的驱动环境。截止`2018.01.20`，目前Node.js的LTS最新版本为`8.9.4`。  
你可以通过下面的Linux指令将Node.js下载到你想要的目录中。
```
$ sudo wget https://nodejs.org/dist/v8.9.4/node-v8.9.4.tar.gz 
#国内速度比较慢，可以使用这个链接代替：
# https://npm.taobao.org/mirrors/node/v{版本号}/node-v{版本号}.tar.gz
```
然后，我们需要解压这个压缩包，并且开始编译它。
```
$ sudo tar -xzf node*.tar.gz #解压
$ cd node* #进入解压后的文件夹
$ sudo ./configure #进行环境配置（检测），如果出现warning 建议使用 Oneinstack 安装Nginx 以解决环境依赖问题 （Nginx稍后会使用到）
$ sudo make #编译 （可能需要数十分钟）
$ sudo make install #安装 
#先别急，我们看看安装是否成功
$ node -v #检查是否输出的是你要安装的版本
$ npm -v #同上
```
>[info] 在国内，NPM源的速度比较慢，可以使用`sudo npm config set registry http://registry.npm.taobao.org/`来将npm更换到国内的淘宝源.

### 1.2 数据库

#### Mongodb （推荐）
Mongodb是目前流行的NoSQL数据库。它具有高性能，高可用，易扩展，富查询的特性。相比Redis ，它更能节省内存开支。  
  
   - **Ubuntu**
Mongodb 目前只给LTS版本提供安装包。例如，12.04 LTS (precise), 14.04 LTS (trusty), 16.04 LTS (xenial)
使 APT 支持 https
```
$ sudo apt-get install apt-transport-https
```
导入公钥
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
为Mongodb建立一个.list文件

>[info] 由于国内可能源不稳定，我们可以使用`https://mirrors.tuna.tsinghua.edu.cn/mongodb/apt/{ubuntu或者debian}` 来代替下面的官方源
---------------
>[success] 17.07.17 新增: **阿里源** `https://mirrors.aliyun.com/mongodb/apt/{ubuntu或者debian}` 
```
#（目前最新版本是3.6，安装其他版本可以将 3.6 替换为其他版本。这个方法可能向上兼容到 3.0。）

#如果你是Ubuntu 12.04 LTS 执行下面的代码
$ sudo echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list

#如果你是Ubuntu 14.04 LTS ，请执行下面的代码
$ sudo echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list

#如果你是Ubuntu 16.04 LTS 请执行下面的代码
$ sudo echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list

```
  更新apt源
```
$ sudo apt-get update
```
  安装Mongodb
```
$ sudo apt-get install -y mongodb-org #安装Mongodb社区版
```
```
$ mongod --version # 检测版本
```
>[danger] 请检查：**您的 MongoDB 版本是否大于或等于3.4.10**，如果不是请立即升级（以前的版本存在严重的安全风险）！ 

-----
  
   - Debian
  导入公钥
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
 新建一个.list文件

>[info] 由于国内可能源不稳定，我们可以使用`http://mirrors.tuna.tsinghua.edu.cn/apt/{ubuntu或者debian}` 来代替下面的官方源
---------------
>[success] 17.07.17 新增: **阿里源** `http://mirrors.aliyun.com/mongodb/apt/{ubuntu或者debian}` 

```
#（目前最新版本是3.6，安装其他版本可以将下面的3.4替换为其他版本。这个方法可能向上兼容到3.0。）

#如果你是 Debian 7 Wheezy，请使用执行下面的命令
$ sudo echo "deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/3.6 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list

#如果你是Debian 8 Jessie ,请执行下面的命令
$ sudo echo "deb http://repo.mongodb.org/apt/debian jessie/mongodb-org/3.6 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
```
  更新apt源
```
$ sudo apt-get update
```
  安装Mongodb
```
$ sudo apt-get install -y mongodb-org
```

```
$ mongod --version # 检测版本
```
>[danger] 请检查：**您的 MongoDB 版本是否大于或等于3.4.10**，如果不是请立即升级（以前的版本存在严重的安全风险）！ 


   
**有关如何配置Mongodb数据库，[请点击这里](https://www.kancloud.cn/a632079/nodebb-cn/372104)**
  
  

#### redis （不推荐大型社区使用）

>[danger] Q: (NodeBB Development)为什么我们不建议使用 Redis ? 
>A: https://docs.nodebb.org/configuring/databases/redis/
1. 安装 Redis
```shell
$ sudo add-apt-repository ppa:chris-lea/redis-server
$ sudo apt-get update
$ sudo apt-get install -y redis-server #安装redis
```
2. 为 Redis 加入安全性保障:

>[success]**密码支持，仅限本地使用，删除FLUSHALL**

编辑 `/etc/redis/redis.conf`:
```
$ vim /etc/redis/redis.conf
```
在里面加入下面的内容:
```
requirepass yourpassword
bind 127.0.0.1
rename-command FLUSHALL ""
```
3. 重启服务
```
service redis-server restart
```

## 二、安装
### 2.1 获取 NodeBB 程序
截止 2017.11.26，NodeBB 目前最新稳定版本为1.7.0。
首先，我们先得安装好Git（如果有，可跳过）
```
$ sudo apt-get install -y git build-essential
```
找到一个你喜欢的位置，然后使用`git`指令获取程序。
```
$ cd /home #修改为你喜欢的位置
$ sudo git clone -b v1.9.x https://github.com/NodeBB/NodeBB.git nodebb #这将在 /home 下面 新建一个名叫nodebb的文件夹并且会把程序从Github克隆到里面
```
>[info] 现在，你可以使用国内的镜像仓库(`https://gitee.com/NodeBB-CN/NodeBB.git`)来提高连接速度。

然后，我们先把依赖安装完毕。  
```
$ cd nodebb
```
> `1.7.x` 起，无需执行`npm i --production` 或 `yarn install --production` 

现在，我们就可以对开始程序的配置了。
```
$ sudo ./nodebb setup
```
>[info] NodeBB v1.7.1 支持使用 `./nodebb install` 来使用 可视化的 Web 界面进行安装（配置）

*可以根据括号中的提示完成信息的填写，nodebb监听的默认端口是`4567`，Mongodb 监听的端口是 `27017`*  
使用`sudo ./nodebb start` 启动，享受NodeBB给你带来的快乐吧！
### 让NodeBB开机自启
方法可以参考 Ghost 的安装，不过我们还是建议你把NodeBB注册为服务。

首先，先新建一个叫做nodebb的用户和用户组并且把nodebb目录下所有文件的所有权转移给这个用户（组）。
```
$ sudo adduser --system --group nodebb
$ sudo chown -R nodebb:nodebb /{你之前填写的目录}/nodebb
```
现代化的Linux系统都使用 `systemd` 作为默认的初始化系统。让我们给 NodeBB 新建一个 systemd 单元吧。使用`vim /lib/systemd/system/nodebb.service`编辑，请将下面的内容复制进去。
```
[Unit]
Description=NodeBB forum for Node.js.
Documentation=http://nodebb.readthedocs.io/en/latest/
After=system.slice multi-user.target

[Service]
Type=simple
User=nodebb

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=nodebb

Environment=NODE_ENV=production
WorkingDirectory=/home/nodebb
#请把上面的目录替换为你的nodebb目录
ExecStart=/usr/bin/node loader.js --no-daemon --no-silent
#请修改/usr/bin/node 为which node所打印的位置
Restart=always

[Install]
WantedBy=multi-user.target
```
好了，我们启用并且运行nodebb服务吧！
***如果前面使用`./nodebb start` 运行了nodebb的话，请先使用 `./nodebb stop`停止。***
```
$ sudo systemctl enable nodebb
$ sudo service nodebb start
$ sudo service nodebb status
```

## 三、使用Web服务器反代NodeBB
### Nginx 

#### 通过 系统源 安装 Nginx
1. 先去官网下载 apt 源的签名，并添加
```
$ wget http://nginx.org/keys/nginx_signing.key
$ sudo apt-key add nginx_signing.key
```
2. 在 /etc/apt/sources.list 添加源 
```
$ vim /etc/apt/sources.list
# Ubuntu 16.04
$ deb http://nginx.org/packages/ubuntu/ xenial nginx
$ deb-src http://nginx.org/packages/ubuntu/ xenial nginx
# Ubuntu 14.04
$ deb http://nginx.org/packages/ubuntu/ trusty nginx
$ deb-src http://nginx.org/packages/ubuntu/ trustyl nginx

# Debian 7 Wheezy
$ deb http://nginx.org/packages/debian/ wheezy nginx
$ deb-src http://nginx.org/packages/debian/ wheezy nginx
# Debian 8 Jessie 
$ deb http://nginx.org/packages/debian/ jessie nginx
$ deb-src http://nginx.org/packages/debian/ jessie nginx
```
3. 安装
```
$ sudo apt-get update
$ sudo apt-get install nginx
```
4. 新增配置

>[success] 有关 详细的Nginx 反代配置 您可以参考**高级 - 配置Nginx**篇。

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
5. 启动 Nginx
```
$ service nginx start
```
####  通过 编译 安装 Nginx 
>[info] 由于编译安装 比较繁琐，所以我们推荐使用 `Oneinstack` 来进行安装 Nginx.
```
$ sudo apt-get -y install wget screen curl python
$ wget http://mirrors.linuxeye.com/oneinstack-full.tar.gz
$ tar -xzf oneinstack-full.tar.gz
$ cd oneinstack
$ screen -S os
# 在 Screen 内的操作
$ ./install.sh
```
根据需要选择你需要安装的部分。

>[success] 下面的内容是基于你使用这个安装包安装了 `Nginx` 后的。

```
$ cd ~/oneinstack #跳转到你解压oneinstack的目录
$ sudo ./vhost.sh add #配置你需要使用的域名（可以开启ssl）
$ sudo vim /usr/local/nginx/conf/vhost/domains.conf 
```

>[success] 有关 详细的Nginx 反代配置 您可以参考**高级 - 配置Nginx**篇。

* 编辑你刚才填写的域名的配置文件。

* 在文件中，我们删除所有的 location块。并且我们需要把下面的 location 复制进你的conf文件中。

* 如果你启用了ssl，请稍后将正确的证书编辑进相应的文件（通常目录为/usr/local/nginx/conf/ssl/）
```
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

```
最后，我们需要使用下面的指令重载Nginx（如果报错，修改指定位置即可。）
```
$ sudo service nginx reload
```
---
### 其他 Web 服务器
下面是Apache的配置方法，如果您需要，可以点击查看。
* [Apache v2.4.x+](http://nodebb.readthedocs.io/en/latest/configuring/proxies/apache.html)
* [Apache v2.x](http://nodebb.readthedocs.io/en/latest/configuring/proxies/apache2.2.html)

>参考：  
>[NodeBB-Docs](http://nodebb.readthedocs.io/en/latest/index.html)  
>[Mongodb-Docs](http://docs.mongodb.com)  
>[Oneinstack](http://oneinstack.com)
--------------------------
>[info] 编写: a632079
维护: a632079
审核: PA Team
最后更新: 2018.05.05