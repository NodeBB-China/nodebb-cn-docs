## 一、准备

>[warning] 本教程测试环境为 `CentOS 7`

1. 首先更新 `centos`
```
yum -y update
yum -y install epel-release #虽然 NodeBB Docs 并不需要 Centos6 执行这条命令，但为了安装比较新的 Redis 还是建议执行
yum update
```
2. 安装基础环境
```
yum -y groupinstall "Development Tools"
yum -y install git mageMagick
```
## 二、安装
### §1. Node.js
>[info] 我们现在推荐使用 `nvm` 来安装Node.js.
------------------------
>[warning] 截止目前，NodeBB 要求 Node.js 的**最低版本**为 **v4.x**

#### 1. 使用 nvm 安装Node.js LTS(推荐)
>[success] **LTS** ( Long-term Support ) 是 Node.js 的长期支持版本，使用该系列能够让你的程序拥有可靠安全的环境保障。

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

----
#### 1.2 使用源码编译安装Node.js (目前已不再推荐)
首先，我们要去Node.js官网获得源代码文件`https://nodejs.org`。
我们推荐使用LTS作为NodeBB的驱动环境。截止`2017.08.07`，目前Node.js的LTS最新版本为6.11.2。  
你可以通过下面的Linux指令将Node.js下载到你想要的目录中。
```
$ sudo wget https://nodejs.org/dist/v6.11.2/node-v6.11.2.tar.gz 
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

### §2. Redis
~~1. 启用EPEL仓库 & 更新仓库~~ (之前执行过了，此处无需进行这步操作)
2. 通过 `yum` 安装
```
$ yum install redis
```
>[warning] 此时检查一下你将下载的Redis是否是最新版本，如果是，跳到第5步，否则就继续。（[点击这里查看最新 Stable 版本](https://redis.io/)）

3. 安装 Remi 源
```
yum install -y http://rpms.famillecollet.com/enterprise/remi-release/7.rpm   #数字为centos版本
```
4. 通过 Remi 安装最新的 Redis
```
yum --enablerepo=remi install redis
```
-------------------------------------
5. 让 Redis 开机自启
```
systemctl start redis
systemctl enable redis
```
6. 我们来给 Redis 加一些安全设置:
* 加入密码，仅限本地登入，删除 FLUSHALL

```
$ vim /etc/redis/redis.conf
#如果上面没有，配置文件可能会在 /etc/redis.conf
```
编辑 Redis 的配置文件，然后把下面的内容粘贴进去（建议放置到最后）
```
requirepass yourpassword
bind 127.0.0.1
rename-command FLUSHALL ""
```
7. 重启 Redis 服务
```
$ service redis-server restart
```
### §3. NodeBB

1. 从 Github 上克隆 NodeBB 的发布版本分支
```
cd /home #把目录替换为你想安装到的地方
git clone -b v1.5.x https://github.com/NodeBB/NodeBB nodebb
```
2. 安装 NodeBB 依赖环境
```
cd /home/nodebb #替换为上面你修改的目录 + /nodebb
npm install --producation
```
>[info] 由于机器性能和网络情况的差异，该操作可能需要您花费 数分钟 甚至 数十分钟 的时间！
3. 进行初始化设置
```
./nodebb setup
```
>[info] 需要注意的是: 由于默认选择的数据库是 MongoDB 所以在 `Which databases to use` 那步 请填写 `redis`!
--------------
>[success] 这时候您可以尝试通过 `http://你的IP:4567` 访问 NodeBB。如果成功访问，那么我们就大功告成了！(可能需要您使 `iptables`/`firewalld` 允许外部连接对于 4567 端口的访问)

## 三、反代
上一步我们安装好了nodebb，但是需要通过`4567`端口才能访问。现在我们设置一下反代服务，支持通过域名访问网站。

>[info] 反代服务器 有很多种，但这里我们选取的是最流行也是性能最优秀的反代服务: Nginx
1. 首先，安装nginx
```
yum install nginx
```
2. 设置nginx反代规则

> 更详细的 Nginx 规则 请参考 **Nginx 配置** 篇
```
cd /etc/nginx/conf.d
nano example.conf
```
规则如下（www重定向至no-www，如果不需要，可以自己修改）
```
server {
    listen       80;
    server_name  www.example.com;
    return       301 http://example.com$request_uri;
}

server {
    listen 80;

    server_name example.com;

    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://127.0.0.1:4567/;
        proxy_redirect off;

        # Socket.IO Support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```
3. 让 Nginx 配置生效
```
service nginx reload
```
---------------
**如果出现如下错误:**
```
nginx: [emerg] could not build server_names_hash, you should increase server_names_hash_bucket_size: 32
nginx: configuration file /etc/nginx/nginx.conf test failed
```
修改 `nginx.conf`，在 `http{}` 添加:
```
server_names_hash_bucket_size  64;
```
保存，退出，启动nginx

> 教程改编自官方文档，官方文档地址：https://docs.nodebb.org/en/latest/installing/os/centos.html
--------------------------
>[info] 编写: gaokaigithub
维护: a632079
审核: PA Team
最后更新: 2017.08.07