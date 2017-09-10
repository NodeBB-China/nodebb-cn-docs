# NodeBB 搜索
默认 NodeBB 不带搜索引擎的，所以需要使用 搜索功能 的话需要用户自行开启。
需要注意的是，我们在本节中仅介绍本地搜索引擎(区别于公共搜索引擎：谷歌，百度)
## DBSearch
DBSearch 是 NodeBB 默认的搜索引擎（被依赖于`package.json`）。它支持 Mongodb，Redis，同时无需安装第三方程序（例如：Java，MySQL），是一个不错的搜索引擎软件。但是，它并不支持 **非拉丁字符**（例如：中文） 的搜索，所以目前我们**不推荐**使用该搜索插件。

>[danger]这里说下为啥 `dbsearch` 不支持中文：
>* 未集成中文分词
>* 数据库权重排列问题。（若支持中文搜索的话，基于Redis 的 NodeBB 是肯定不能用的，因为 Redis 缺乏这种排列的方法）

### 使用
启用 `nodebb-plugin-dbsearch` 并根据自己的需要配置即可。
## Solr（推荐）
Solr 是一个比较优秀的开源搜索引擎，目前有很多企业使用它作为搜索服务。它基于 Lucene ，但对于此有过很多优化。有趣的是，和 Lucene 一样，它隶属于 Apache 基金会。
### 安装
#### 在操作系统中
>[info] 本部分只支持 Ubuntu/Debian 使用，如您是其他系统，请参考官方教程: http://lucene.apache.org/solr/quickstart.html 

* 要求: Java 8
```
$ sudo apt-get install openjdk-8-jre solr-tomcat
```
#### 在 NodeBB 中
1. 安装 `nodebb-plugin-solr`
2. 启用它并重启 NodeBB
3. 在 Solr 配置中修改端口为 `8080`
4. 重载 NodeBB
5. 在 Solr 插件配置中 开启自动引索并点击重构引索(Rebuild indexes)

-------------
上面的是最简单的 Solr 使用方法，需要扩展/安全啊什么的，请善用搜索引擎。

## ElasticSearch
ElasticSearch 也是一个十分优秀的开源搜索引擎，同时也是目前主流的企业级引擎（例如：百度云，看云，阿里云提供 ElasticSearch 搜索集群）。它基于 Apache Lucene，起初是个人开发者开发的项目，目前由整个开源社区共同维护。

>[danger] ElasticSearch 虽然确实十分优秀，但十分消耗资源，在 2G 2C 的服务器上 CPU 内存占用居高不下 :(

### 安装
* 要求 : Java 8

#### 在操作系统中
>[info] 本部分只支持在 Ubuntu/Debian 使用，如您是其他操作系统，请参考官方教程：https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html#install-elasticsearch

1. 添加 Elastic 公钥
```
$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
2. 安装 OpenJDK Java Runtime Environment(JRE)
```
$ sudo apt-get install openjdk-8-jre
```
3. 安装 ElasticSearch
```
$ sudo apt-get install elasticsearch
```
4. 开机自启并启动服务
```
$ sudo systemctl enable elasticsearch.service
$ sudo service elasticsearch start
```
5. 通过任何插件将 Mongodb 数据导入 ElasticSearch中

>这里，我们推荐使用 [mongo-connecter](https://github.com/mongodb-labs/mongo-connector)

#### 在 NodeBB 中
1. 安装 `nodebb-plugin-elasticsearch`
2. 激活插件 并 重启 NodeBB
3. 进入插件设置页面，检查与 ElasticSearch 的通信是否正常。如果没有，请根据需要调整。

## 公共搜索引擎

>[warning] 使用 公共搜索引擎 的前提是：您的站点已经被它收录 并且 您的站点的收录量/收录速度比较可观，否则就算安装了，也是然并卵系列。

这类插件就比较多啦，你只需要在 寻找插件 中 搜索 对应的搜索引擎名字（如：baidu,google,bing）即可。

>[info] 编写: a632079
维护: PA Team
审核: PA Team
最后更新: 2017.09.10