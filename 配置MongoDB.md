
[TOC]

# MogoDB 基础运维知识
## 3.0版本以前
在mongodb3.0版本以前中,有一个admin数据库, 牵涉到服务器配置层面的操作,需要先切换到admin数据库.即 use admin , 相当于进入超级用户管理模式,mongo的用户是以数据库为单位来建立的, 每个数据库有自己的管理员.我们在设置用户时,需要先在admin数据库下建立管理员---这个管理员登陆后,相当于超级管理员.
```
命令:db.addUser();
简单参数: db.addUser(用户名,密码,是否只读)
```
注意: 添加用户后,我们再次退出并登陆,发现依然可以直接读数据库?
原因: mongodb服务器启动时, 默认不是需要认证的.
要让用户生效, 需要启动服务器时,就指定 `--auth` 选项.这样, 操作时,就需要认证了.
```
# 添加用户
> use admin
> db.addUser('admin','admin',false); # 3.0版本更改为createUser();
# 删除用户
> use test
> db.removeUser(用户名); # 3.0版本更改为dropUser();
```
## 3.0版本以后
### 创建管理员
在3.0版本以后,mongodb默认是没有admin这个数据库的,并且创建管理员不再用 `addUser `,而用 `createUser`;
#### 语法说明
```
{ user: "<name>",  
  pwd: "<cleartext password>",
  customData: { <any information> }, # 任意的数据,一般是用于描述用户管理员的信息
  roles: [
    { role: "<role>", db: "<database>" } | "<role>", # 如果是role就是直接指定了角色,并作用于当前的数据库
    ...
  ] # roles是必传项,但是可以指定空数组,为空就是不指定任何权限
}
```
Built-In Roles（[内置角色](http://docs.mongodb.org/manual/reference/built-in-roles/#built-in-roles)）：

* 数据库用户角色：read、readWrite;
* 数据库管理角色：dbAdmin、dbOwner、userAdmin；
* 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
* 备份恢复角色：backup、restore；
* 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
* 超级用户角色：root 
* 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
* 内部角色：__system
  PS：关于每个角色所拥有的操作权限可以点击上面的内置角色链接查看详情。
##### 官方Example
```
> use products # mongoDB的权限设置是以库为单位的,必选要先选择库
> db.createUser( 
{ "user" : "accountAdmin01", 
 "pwd": "cleartext password",
 "customData" : { employeeId: 12345 },
 "roles" : [ { role: "clusterAdmin", db: "admin" }, 
             { role: "readAnyDatabase", db: "admin" },
             "readWrite" 
             ] },
{ w: "majority" , wtimeout: 5000 } ) # readWrite 适用于products库,clusterAdmin与readAnyDatabase角色适用于admin库
```
writeConcern文档（[官方说明](http://docs.mongodb.org/manual/reference/write-concern/)）

* w选项：允许的值分别是 1、0、大于1的值、"majority"、\<tag set>；
* j选项：确保mongod实例写数据到磁盘上的journal（日志），这可以确保mongd以外关闭不会丢失数据。设置true启用。
* wtimeout：指定一个时间限制,以毫秒为单位。wtimeout只适用于w值大于1。
```
> use shop;
> db.createUser({
    user:'admin',
    pwd:'zhouzhou123',
    roles:['dbOwner']
})
```
> 只要新加了一个用户,admin数据库就会重新存在;
```
$ mongo --host xxx -u admin -p zhouzhou123 --authenticationDatabase shop # 用新创建的用户登录
```
在 Mongo 中的操作:
```
# 查看当前用户在shop数据库的权限
> use shop;
> db.runCommand(
  {
    usersInfo:"shopzhouzhou",
    showPrivileges:true
  }
)

# 查看用户信息
> db.runCommand({usersInfo:"userName"})

# 创建一个不受访问限制的超级用户
> use admin
> db.createUser(
  {
    user:"superuser",
    pwd:"pwd",
    roles:["root"]
  }
)
```
### 认证用户
```
> use test
> db.auth(用户名,密码); #注意是以库为单位,必须先选择库;
```

### 删除用户
```
# 删除用户
> use test
> db.dropUser('用户名');
```
### 修改用户密码
```
> use test
> db.changeUserPassword(用户名, 新密码);

# 修改密码和用户信息
db.runCommand(
  {
    updateUser:"username",
    pwd:"xxx",
    customData:{title:"xxx"}
  }
)
```
## 权限规则
参考: http://blog.csdn.net/kk185800961/article/details/45619863

> 以上内容摘自: [【mongoDB运维篇①】用户管理](https://segmentfault.com/a/1190000004263255#articleHeader0)
# NodeBB 中所需要的 MongoDB 操作
## 对于现代版本的MongoDB (3.0之后)
进入 MongoDB
```
$ mongo
```

新建一个数据库管理员吧(相当于Root):
```
> use admin
> db.createUser( { "user" : "替换成你想要的管理员账户",  "pwd": "替换成你的管理员账户密码", "customData" : { employeeId: 12345 }, "roles" : [ { role: "clusterAdmin", db: "admin" },{ role: "readAnyDatabase", db: "admin" },"readWrite" ] },{ w: "majority" , wtimeout: 5000 } );
```
添加一个名为 nodebb 的数据库 并 新建一个用户赋予给它读写此库的权限:
```
> use nodebb
> db.createUser( { user: "nodebb", pwd: "替换成你的密码", roles: [ "readWrite" ] } )
```
如果要在NodeBB的管理控制面板（高级→数据库）中查看数据库统计信息，请键入以下命令：
```
>  db.grantRolesToUser("nodebb",[{ role: "clusterMonitor", db: "admin" }]);
```
如果不输入命令，则会在尝试查看数据库统计信息时收到此错误消息：
```
Internal Error.

Oops! Looks like something went wrong!

/api/admin/advanced/database

not authorized on nodebb to execute command { serverStatus: 1 }
```
## 对于早期版本的MongoDB (3.0之前) 
```
$ mongo
```
在 MongoDB 中输入:
```
> use admin
> db.addUser("替换成你想要的管理员账户","替换成你的管理员账户密码",false);
> use nodebb
> db.addUser( { user: "nodebb", pwd: "替换成你的密码", roles: [ "readWrite" ] } );
```
## 使 MongoDB 启用权限认证
修改 `/etc/mongodb.conf`
```
$ vim /etc/mongodb.conf
```
### 对于 2.6.x 
取消注释 : `auth = true`。
### 对于3.x
取消注释 `security: `并在其下方添加: `authorization: enabled`（不要忘记在第二行之前放置两个空格）。它应该是这样的：
```
security:
  authorization: enabled
```
### 重新启动 Mongodb
```
$ service mongod restart
```

>[info] 编写: PA Team & NodeBB Development
维护: a632079
审核: PA Team
最后更新: 2017.08.07