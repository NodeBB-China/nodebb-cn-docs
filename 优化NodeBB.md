# 优化 NodeBB
当要维护许多并发连接或高请求速率时，可以遵循某些程序来简化NodeBB。
本文试图概述那些发现 NodeBB 不能在当前配置中以全速运行时能使用的各种策略。

## 利用集群
默认情况下，NodeBB将在一个进程上运行，某些调用可能比其他进程更长，从而导致相同资源的延迟或队列等待。要解决这个问题，可以通过添加多个端口到`config.json`中来指示NodeBB在多个进程上运行。
```
{
    "port": ["4567", "4568", "4569"]  //这将启动3个进程，分别监听这3个端口
}
```
请记住，使用 `node loader.js` 或 `./nodebb start` 来启动程序，这样才会产生 3 个进程。

>[danger] 若使用 `node app.js` 将只会启动一个监听端口组第一个成员的进程！

需要像Nginx这样的代理服务器，以便在所有服务器之间进行负载均衡请求。 请将将下面的块添加到您的配置中(请根据实际修改)：
```
upstream io_nodes {
    ip_hash;
    server 127.0.0.1:4567;
    server 127.0.0.1:4568;
    server 127.0.0.1:4569;
}
```
...并将 `proxy_pass` 值改为：`proxy_pass http://io_nodes;`
## 使用代理服务器来提供静态资源
Nginx在提供静态资源（例如javascript，css，图像等）方面非常好。 通过 Nginx 接管为最终用户提供服务的任务。这样，NodeBB进程将仅仅处理API调用，这将大大降低NodeBB的工作量（并增加了吞吐量）。

您只需要修改您的在 Nginx 配置文件，并在其中加入下方的 `location` 块 :
```
location @nodebb {
    proxy_pass http://127.0.0.1:4567;
}

location ~ ^/assets/(.*) {
    root /path/to/nodebb/;
    try_files /build/public/$1 /public/$1 @nodebb;
}

location /plugins/ {
    root /path/to/nodebb/build/public/;
    try_files $uri @nodebb;
}

location / {
    proxy_pass http://io_nodes;
}
```

>[warning] 此配置仅适用于NodeBB v1.4.3 及 以上版本。

此外，你可以指示Nginx为这些资源提供压缩服务:
```
gzip            on;
gzip_min_length 1000;
gzip_proxied    off;
gzip_types      text/plain application/xml text/javascript application/javascript application/x-javascript text/css application/json;
```
## 应用上述所有配置的 Nginx 配置示例
```
upstream io_nodes {
    ip_hash;
    server 127.0.0.1:4567;
    server 127.0.0.1:4568;
    server 127.0.0.1:4569;
}

server {
    listen 80;

    server_name community.nodebb.org;

    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_redirect off;

    # Socket.io Support
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";

    gzip            on;
    gzip_min_length 1000;
    gzip_proxied    off;
    gzip_types      text/plain application/xml text/javascript application/javascript application/x-javascript text/css application/json;

    location @nodebb {
        proxy_pass http://io_nodes;
    }

    location ~ ^/assets/(.*) {
        root /path/to/nodebb/;
        try_files /build/public/$1 /public/$1 @nodebb;
    }

    location /plugins/ {
        root /path/to/nodebb/build/public/;
        try_files $uri @nodebb;
    }

    location / {
        proxy_pass http://io_nodes;
    }
}
```

>[warning] 此配置仅适用于NodeBB v1.4.3 及 以上版本。

## 配置 Redis
当您设置NodeBB使用多个流程时，最好配置Redis。每个NodeBB进程可以通过Redis pub- sub与其他进程通信。在服务器上安装Redis，并在 `config.json`中添加一个Redis块。下面就是一个使用 mongodb 作为数据库储存 和 Redis pubsub 的例子。

>[success] 当这样配置的时候，Redis 也将被用作会话 (sessions) 存储。
```
{
    "url": "http://example.org",
    "secret": "your-secret-goes-here",
    "database": "mongo",
    "port": [4568,4569],
    "mongo": {
        "host": "127.0.0.1",
        "port": "27017",
        "database": "0"
    },
    "redis": {
        "host":"127.0.0.1",
        "port":"6379",
        "database": "0"
    }
}
```

>[info] 编写: NodeBB Development Team
维护: a632079
审核: PA Team
最后更新: 2017.08.17