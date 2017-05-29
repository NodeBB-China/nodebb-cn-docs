>[info] 说起来，很抱歉呢。
> 承诺 的 NodeBB-CN 社区 由于 续费原因导致数据丢失了:(
> 为此，我们决定将 原创教程 和 官方教程 放在这里，方便大家获得信息。
> **生来纯洁，不忘初心** *-- MyPCQQ Lib*

##序言
NodeBB 是 一种基于 Node.js 构建的电子公告牌系统(Bulletin Board System)。事实上，NodeBB已经不能说是一种单纯的 BBS 系统了。相对于传统的BBS,它的风格其实更偏向于 WeCenter 这种话题类的平台(Platform),也就是说它可能并不是十分适合有 **传统BBS** 需求的站长。
NodeBB的优势在于 即时通讯技术 和 瀑布流消息的呈现模式，这主要得益于 Socket.io 提供的 ajax 解决方案(WebSocket > Long polling)。所以说，NodeBB能够吸引人的其实就是它这种十分友好，流畅的用户之间的交互。
NodeBB 的插件结构十分自由，也十分的开放。 与此同时 NodeBB 的原生系统其实十分的简陋 ，例如:缺少完善的权限系统，缺少完善的积分系统，未自带完善的社会化登录(SSO)等。
所以，我们欢迎任何热爱它的人来加入我们。但是，我们也不会解答一些作为 Node.js 开发者应该明白的常识性问题。
>[warning] 以下是熟练使用 NodeBB 必须掌握的程序:
> **Node.js** --- NodeBB 是基于 ES5 标准开发的程序。但是鉴于 Node.js 强大的模块系统，所以你完全可以使用 ES6 或者 ES2016来开发任何新的插件。
> **MongoDB/Redis** 掌握数据库的基本操作，能够让你在初期避免发生一些奇葩的问题。

很多人一开始会拿 Discuz!/PHPWind 来和 NodeBB进行比较，在这里呢，笔者可以很负责的告诉你:想要熟练操控NodeBB的难度是这类PHP论坛的2-3倍。奉劝那些只是看着NodeBB的现代化(Modern)而入坑的童鞋量力而为。

由于老的社区团队已经不再维护中文社区了，所以我们创建了新的社区/团队:
* NodeBB中文社区 : [nodebb.cn)](http://nodebb.cn.org) **目前是Closed的状态，预计在6.23恢复**
* NodeBB China(QQ交流群) : 392448497

## 正在使用NodeBB的论坛
[萌梦社区](http://qtdream.com/ "萌梦社区")

[EFL Club 英语学习论坛](http://forum.eflclub.me/ "EFL Club")

[羊毛界](http://yangmaojie.com "羊毛界")

[Minecraft Lightweight](http://mc.soaryun.com/ "Minecraft Lightweight")

[Widora](http://sns.widora.io "Widora")

[intio.org](http://intio.org/ "intio.org")

[自由职业者社区](https://v2mm.tech/categories/ "v2mm")

**如果您也在使用NodeBB，可以在 NodeBB China 的 Github 仓库中直接提交Pull Requests来补充**

## 项目贡献
>[info] 目前我们的文档托管在看云。我们会定期从 Github 同步到看云的仓库(每周一次)
> 项目 Github 链接: https://github.com/NodeBB-China/nodebb-cn-docs

1. 遇到问题可以在Issues中/看云评论提出，同时也非常欢迎各位Fork此项目然后提交Pull Requests。
2. 如果对Git和GitHub的使用流程比较熟悉，也可以在Issues中申请加入我们Organization的Members，这样就可以无需Fork而可以直接向本项目Push内容。
3. 我们的文档分为原创和翻译。对于原创，您可以在文档中注明作者或链接。对于翻译，参与前请优先阅读[翻译流程约定](官方教程翻译/翻译流程约定.md)

>以上贡献规则摘自 NodeBB China 文档

## NodeBB官方链接索引

* [NodeBB GitHub](https://github.com/NodeBB/NodeBB)
* [官方论坛](https://community.nodebb.org/)
* [官方英文版文档](https://docs.nodebb.org/en/latest/)

>[info] 编写: a632079
润色: -
维护: a632079
审核: PA Team & NodeBB China
最后更新: 2017.05.30