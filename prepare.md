# 准备
NodeBB 需要部署(注: `Rebuild` , 为了更符合习惯所以翻译为此) 资源文件才能使对页面的更改生效。部署过程可以通过调用: `./nodebb build` 来实现。 由于默认会部署所有的资源文件， 所以该过程可能会十分漫长 (一般 30s)。

## 按需部署资源
出于快速开发的目的， 我们通常只按需部署文件。
例如， 我们只对 ACP 页面做出了变更， 我们可能只需要部署管理面板相关的资源:
```
$ ./nodebb build adminjs admincss tpl
```
以上的指令只会部署 ACP 面板的浏览器端 JS ， 样式表 以及 所有的模板文件。
有关 build 指令的详细用法， 可以通过查看本文档 `安装 - 使用` 部分来充分了解。

## Grunt
为了优化开发过程， NodeBB 团队 （可能还有一部分插件作者）会在开发工作时使用 `Grunt`。尽管您可能需要安装 `grunt-cli` ， 但 `grunt` 会随着 `npm install` 直接被作为 NodeBB 的依赖安装。
安装完成后， 您可以简单得通过 `grunt` 来编译所有资源并启动 NodeBB， `Grunt` 还会监听资源变化， 编译变动的资源。这使得我们无需通过 ACP， 或者通过指令重启， 启动 NodeBB。

## ES2015 , ES2016, ES2017
我们更推荐使用 ES2015 或者更新的 JS 语法来书写 NodeBB 插件。 这是规避 JS 错误丢失的最有效手段。
* 使用 `const` , `let` 而不是 `var`
```javascript
const a = 1;
let b = 1;
b = 2; 
```
* 使用 `Promise`, 尽量规避回调
```javascript
Promise.resolve([1, 2, 3])
  .then(data => {
    // 做一些处理
  })
  .catch(err => {
    winston.err(err);
  });
```
* 善于利用 `Class`, `Generator`, `Symbol`, `Async Function` 等新特性
```
const privateData = Symbol('a');
class A {
  constructor() {
    this[privateData] = 'private'
  }
}

new A()[privateData] // is private
new A()['a'] // is null 
new A()[Symbol('a')] // is null

async function b() {
  return true;
}
b()
  .then(d =>{
    // d is true
  })
```

## 使用 QuickStart 来快速开发
NodeBB 官方的 QuickStart 地址为: https://github.com/NodeBB/nodebb-plugin-quickstart
**不过，在本章节中， 我们会使用我们自己编写的 QuickStart 作为讲解基础。**
地址: https://github.com/NodeBB-China/nodebb-plugin-quickstart

>[info] 编写: a632079
维护: PA Team
审核: PA Team
最后更新: 2018.05.06