# Config.json 配置
![](images/TIM图片20170817021351.png)

>好吧，就当我买个萌吧，下面继续。

NodeBB的大多数配置由管理员控制面板（ACP）处理。但在位于NodeBB根文件夹的配置文件（`config.json`）中还定义了少数与服务器相关的选项。

## 这些值是默认 通过安装脚本 设置的：

* `url` 是指向您的 NodeBB 的 完整Web地址。如果您没有域名，那么IP地址也可以正常工作（例如：`http://127.0.0.1:4567`）。路由安装也在这里定义他们的路由（例如 ： `http://127.0.0.1:4567/forum`）
* `secret` 是用于 Cookie会话 的 散列文本字符串。如果改变密钥，那么所有现有的 sessons 将不再有效，用户需要重新登录。
* `database` 定义NodeBB使用的主数据库。（例如： `redis` 或 `mongo` )
* `redis` 和 `mongodb` 是包含数据库链接信息的对象，它们包含下面的部分或全部的内容：
  - `host`
  - `port`
  - `uri` (Mongodb 填连接地址)
  - `username` (仅 Mongodb 填写)
  -  `password`
  - `database`

## 以下值是可选的，并覆盖由NodeBB设置的默认值：

* `port`（默认值：`4567`）指定 NodeBB 绑定的端口号。您可以指定端口组，NodeBB 将会生成等同端口数量的进程。如果使用多个端口，则需要配置一个 负载均衡器 来处理对不同端口的请求。
* `bcrypt_rounds`（默认值：12）指定密码的加密回合数。较慢的机器可以选择减少轮次以加快登录过程，但是如果计算机处理能力过快，以至于默认值已经不足以满足计算的时间，您 可以选择 提高回合数以提升撞库的难度。
* `upload_path`（默认值：`/public/uploads`）指定相对于 NodeBB 根目录 的 上传文件的保存路径。
* `jobsDisabled` 这可以禁用在一定间隔内运行的作业。
  - 例如:“jobsDisabled”：true 将禁用日常摘要邮件和通知。此选项对于运行多个NodeBB 进程 的安装很有用。在这样的设置中，只有一个进程可以处理`jobs`，而其他进程可以将`jobs`设置为true 。

> 其实 我也没看懂上面那个该怎么配置....

* `socket.io` Socket.io 的配置项
  - `transports` (默认值：`["polling", "websocket"]`) 配置 Socket.IO 使用的传输协议
  - `address` ( 默认值: `""`) Socket.IO 的服务器地址。可以为空。
  -  `origins` (默认值为在 config.json 中 url 所在的所有端口) 可为 Socket.IO 定义不同的 URL，格式为: `domain.tid:port`(例如：example.org:*)

*  `bind_address`（默认值：`0.0.0.0` 或 全部接口）指定 NodeBB 应绑定的本地地址。
   - 默认情况下，NodeBB 将侦听所有接口上的请求，但设置后，NodeBB 将只接受该接口的连接。
* `sessionKey`（默认值：`express.sid`）指定使用的会话密钥。

>[info] 编写: NodeBB Development Team
维护: a632079
审核: PA Team
最后更新: 2017.08.17