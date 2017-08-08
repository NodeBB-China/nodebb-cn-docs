# 配置 NginX 作为代理
NodeBB默认运行在端口`4567`上,这意味着通常需要 `URL:Port` 才能访问 NodeBB（例如: `http://example.org:4567`）
为了允许NodeBB在没有端口的情况下可以被访问，可以设置 NginX 来让所有对特定主机名（或子域）的请求代理到在任何端口上运行的 NodeBB 。
## 要求
* NginX 版本 1.10.0 或 更高
   - ~~软件包管理器可能不提供足够新的版本。 要获取最新版本，请自行编译，或者在Ubuntu上使用NGINX Stable或NGINX Development PPA 后再进行安装。如果您使用的是Debian，请使用DotDeb存储库获取最新版本的Nginx。~~ 
   现在,包管理器安装的 NginX 基本已满足需求。
   - 您可以用 `nginx -V` 查看 NginX 版本
## 修改配置
编辑文件 `/path/to/nginx/conf/nginx.conf`,在 http 块加入内容。如:

>[info] 如果您查看过 NodeBB 官方文档，你会发现它的 配置文件位置 已经过时。

![](images/21.png)
下面是基本的 nginx 配置，NodeBB 运行在 4567 端口：
```
server {
    listen 80;

    server_name forum.example.org;

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
}
```
下面的 nginx 配置，NodeBB 运行在 `["4567","4568"]` 端口：
```
server {
    listen 80;

    server_name forum.example.org;

    location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://io_nodes;
        proxy_redirect off;

        # Socket.IO Support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

upstream io_nodes {
    ip_hash;
    server 127.0.0.1:4567;
    server 127.0.0.1:4568;
}
```
下面是使用SSL的例子:
```
### redirects http requests to https
server {
    listen 80;
    server_name forum.example.org;

    return 302 https://$server_name$request_uri;
}

### the https server
server {
    # listen on ssl, deliver with speedy if possible
    listen 443 ssl spdy;

    server_name forum.example.org;

    # change these paths!
    ssl_certificate /path/to/cert/bundle.crt;
    ssl_certificate_key /path/to/cert/forum.example.org.key;

    # enables all versions of TLS, but not SSLv2 or 3 which are weak and now deprecated.
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # disables all weak ciphers
    ssl_ciphers 'AES128+EECDH:AES128+EDH';

    ssl_prefer_server_ciphers on;

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
}
```
### 注意：
 * ~~nginx 必须大于1.4.x版本才能支持websockets。Debian / Ubuntu 使用1.2，NodeBB 一样能够运行，因为有降级机制。~~ 好吧，我们现在要求的版本至少为 `1.10.0` ,并不会存在上面的问题。
 * 如果你的 NodeBB 在和 nginx 是用一台物理机运行，那么 `proxy_pass` 的 IP 应该是 `127.0.0.1`。如果存在于不同的环境，请修改为 NodeBB 所在服务器的 IP.
 * 这个配置能让您的 nginx 服务器监听 `forum.example.org` 的请求。但它不能把互联网路由到这里，所以，你还需要更新你的域名的 DNS 设置，把 `forum.example.org` 解析到 nginx 对应的机器！
 * 在CentOS 7上，您可能会遇到“Bad Gateway”错误。您可以通过运行这条指令 `setsebool -P httpd_can_network_connect on` 来解决此问题。
 
 ## 配置 Nginx 以使用自定义的错误页面
 此示例将演示如何在您的论坛未运行时将 Nginx 配置为使用自定义的502错误页面。
 ### 创建您的自定义错误页面
创建 `502.html`并将其放在 `/path/to/nginx/html` 目录中（这是Nginx默认文档根目录的位置）然后添加内容到您的 `502.html`文件。以下是可以复制和粘贴的示例：
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>Insert your page title here</title>
    </head>
    <body>
        <p>Insert your content here.</p>
    </body>
</html>
```
### 配置Nginx以使用您的自定义错误页面
编辑您的 Nginx 配置 文件(上面我们所修改的`/path/to/nginx/conf/nginx.conf`)，找到你刚才添加的`Server` 块，然后在里面加入下面的内容：
```
server {
    #在这里开始添加内容
    error_page 502 /502.html;

    location = /502.html {
        root /path/to/nginx/html;
        internal;
    }
}
```
使用`error_page`指令，以便在发生502错误时提供您创建的自定义页面。位置块确保根与我们的文件系统位置匹配，并且文件只能通过内部Nginx重定向访问。

重新启动Nginx: `sudo service nginx restart`，使配置生效。这样，下次用户访问您的论坛时，他们会看到您的自定义页面。

>[info] 编写: NodeBB Development & a632079
维护: a632079
审核: PA Team
最后更新: 2017.08.08