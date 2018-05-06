# 常用方法 & 变量
[TOC]

## 导言
![](images/screenshot_1523274418452.png)
NodeBB 自身携带了一系列方法， 我们将其称为 `NodeBB 系统函数库`。 NodeBB 系统函数库包含数据库操作， 权限管理， 用户操作， 路由管理， socket.io操作等快捷的方法。 出于快速开发的考虑， NodeBB 团队开放了系统函数库给开发者们调用。作为开始开发的第一步， 建议初步了解 NodeBB 系统函数库的使用方法。
函数库位置： https://github.com/NodeBB/NodeBB/tree/master/src

## 常用方法
在本节中， 我们会初步介绍 NodeBB 函数库的基本使用方法， 以及常用的方法。

### 申明函数库
```
  // 系统函数库
  const user = module.parent.require('./user')
  const db = module.parent.require('../src/database')
  const meta = module.parent.require('./meta')
  const utils = module.parent.require('../public/src/utils')
  
  // 常用模块
  const async = module.parent.require('async')
  const nconf = module.parent.require('nconf')
  const winston = module.parent.require('winston')
  const path = module.parent.require('path')
  
  // 加载依赖
  const pify = require('pify')
```
>[info] 以上是相对常用的 NodeBB 函数库 以及 NodeBB依赖模块 的申明

一次申明所有常用的函数库是一个十分有助于提高开发效率的手段， 可以避免在开发过程中多次回到文件头添加申明。
### 用户表操作
本节将介绍 NodeBB 函数库中 `user` 的常见使用方法。

>[danger] 注意: 如果需要读写自定义的用户表字段， 需要添加白名单。
#### 添加白名单
在 `plugin.json` 中添加 hook :
```json
{
  "hooks": [
    {
      "hook": "filter:user.whitelistFields",
      "method": "addUserWhitelistFields"
    }
  ]
}
```
在 `core.js` 中添加: 
```javascript
class Core {
  // 在类中添加
  async addUserWhitelistFields: (data) => {
    data.whitelist.push('field_name') // 添加白名单字段名
    return data
  }
}
```
#### 保存数据至用户表
* 方法: `user.setUserField`, `user.setUserFields`
* 参数:
  * 前者: `uid`, `field`, `data`
  * 后者: `uid`, `data` (对象)

##### 使用例子
```javascript
async () => {
  // 如果只是保存一些非重要信息(无需确保写安全)， 可以移除下方的 `await` 来提高速度
  await pify(user.setUserField)(1, 'access_token', '**********')
  await pify(user.setUserFields)(1, {
    access_token: '******',
    access_secret: '******'
  })
}
```
#### 获取用户表数据
* 方法: `user.getUsersFields` , `user.getUserFields` , `user.getUserField`
* 参数: `uids`, `fields`

> **注:**  后两者其实是前者的语法糖。

该方法能够便捷得获取某用户的特有信息。

##### 使用例子
```javascript
async () => {
  const username = await pify(user.getUserField)(1, 'username') // 返回 uid 为 1 的用户名, 返回类型就是数据类型
  const data = await pify(user.getUserFields)(1, ['username', 'email']) // 返回 uid 为 1 的用户的用户名和邮箱， 返回类型是数组
  const muti = await pify(user.getUsersFields)([1, 2], ['username', 'email']) // 返回 uid  1, 2 的用户名和邮箱， 返回类型是数组 (多元数组)
}
```
#### 获取 UID
NodeBB 中可以通过 Username, Userslug, Email 来获取 UID.
方法: `user.getUidByEmail ` `user.getUidByUsersulg` `user.getUidByUsername`
参数: `input`

> **注：** NodeBB 还提供类似获取用户表数据中的获取多数的方法，也提供了获取 username , userslug, email 的方法， 有需要可以查看 NodeBB 系统函数库。

##### 使用例子
```javascript
async () => {
  const uid = await pify(user.getUidByEmail)('admin@admin.com') // 返回 uid 或者 null
  const also_uid = await pify(user.getUidByUsername)('a632079') // 返回 uid 或者 null
  const uid_too = await pify(user.getUidByUserslug)('a632079') // 返回 uid 或者 null
}
````

#### 创建用户
方法: `user.create`
参数: `data`(对象)

> **注:** data 中 email, username 必须存在. password 可以缺省

##### 使用例子
```js
async () => {
  await pify(user.create)({
    username: 'username',
    email: 'user@mail.com',
    password: 'input_Password' // 密码可以缺省， 通常只在通过 SSO 注册的时候缺省
  })
}
```

### 数据库操作
本节将会介绍 NodeBB 函数库中 `db` 的常用方法。
#### 保存数据
方法: `db.setObjectField`
参数: `key`, `field`, `data`

##### 使用例子
```js
async () => {
  // 无需确保写安全， 可以移除 await 提高速度
  await pify(db.setObjectField)('wxid:uid', 'wechat_openid', 'uid') // SSO WeChat 中的例子， 通过 wechat openid 获得 用户 uid 以便实现登录
}
```
#### 判断数据是否存在
方法: `db.isObjectField`
参数: `key` `field`

##### 使用例子
```js
async () => {
  const juage = await pify(db.isObjectField)('qqid:uid', 'qq_openid') // 返回布尔值. true 或者 false
}
```

#### 获取数据
方法: `db.getObjectField`
参数: `key`, `field`

##### 使用例子
```js
async () => {
  const juage = await pify(db.getObjectField)('qqid:uid', 'qq_openid') // 返回 null 或者 数据
}
```

### 元信息操作
本节将讲述 NodeBB 系统库 `meta` 中最常用的方法
#### 获取设置
* 方法: `meta.setting.get`, `meta.setting.getOne`
* 参数: 
  * 前者: `hash`
  * 后者: `hash`, `field`
##### 使用例子
```js
async () => {
 const settings = await pify(meta.setting.get)('aliverify') // 摘自 AliVerify 返回 null 或者设置
 const appkey = await pify(meta.setting.getOne)('aliverify', 'appkey') // 摘自 AliVerify 返回 null 或者设置
}
```

#### 保存设置
* 方法: `meta.setting.set`,`meta.setting.setOne`
* 参数: 
  * 前者: `hash`, `values`
  * 后者: `hash`, `field`, `value`
  
>[info] 我们并不推荐使用此方法， 设置更应该交给插件后台设置控制。

##### 使用例子
```js
async () => {
  await pify(meta.setting.set)('qq_config', {
    appId: '******',
    appToken: '*******'
  })
  await pify(meta.setting.setOne)('qq_config', 'appId', '******')
}
```

### 工具类
本节中， 我们将介绍 NodeBB 公共库中 `/public/src/utils` 的用法
#### 生产 UUID
方法: `utils.generateUUID`

##### 使用例子
```js
async () => {
  const uuid = utils.generateUUID()
}
```

#### 盐化
可以将 username 转换成 userslug
方法: `utils.slugify`
参数: `username`

##### 使用例子
```
async () => {
  const userslug = utils.slugify('MyUserName')
}
```

#### 有效性检查
方法: `utils.isEmailValid`, `utils.isUserNameValid`, `utils.isPasswordValid`, `utils.isNumber`
参数: `input`
##### 使用例子
```
async () => {
   utils.isEmailValid('mail.com') // 返回 false
   utils.isUserNameValid('a632079') // 返回 true
   utils.isPasswordValid('') // 返回 false
   utils.isNumber('123') // 返回 false
}
```

## 常用变量
### Session 中的 uid
如果有 express 的 `req` 参数传入， 我们就可以很方便得获取 uid
#### 使用例子
```js
async (req) => {
  if (req.user && req.user.uid && req.user.uid > 0) {
    const uid = req.user.uid
  }
}
```

>[info] 编写: a632079
维护: PA Team
审核: PA Team
最后更新: 2018.05.06