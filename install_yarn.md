
[TOC]

# 安装 Yarn
## 什么是 Yarn ？

>  Yarn 对你的代码来说是一个包管理器， 你可以通过它使用全世界开发者的代码，或者分享自己的代码。 Yarn 做这些快捷、安全、可靠，所以你不用担心什么。
> 通过 Yarn 你可以使用其他开发者针对不同问题的解决方案，使自己的开发过程更简单。 使用过程中遇到问题，你可以将其上报或者贡献解决方案。一旦问题被修复，Yarn 会更新保持同步。
> 代码通过包（package) 或者 称为模块（module）的方式来共享。 一个包里包含所有需要共享的代码，以及描述包信息的文件，称为package.json。
>  ----- `yarnpkg.com`

## 通过 包管理器 安装
### Arch Linux
由于 Arch Linux 的系统源里已经存在 Yarn 了，所以你只需执行:
```
$ sudo pacman -Syu yarn
```
### Ubuntu/Debian
在 Ubuntu 16.04 及其 更早版本 和 Debian 稳定版 环境时，你需要配置 NodeSource 仓库(有关如何配置，可参考: **安装 - 通过操作系统 - Ubuntu/Debian + Redis/Mongodb**)以获取足够新的 node.js 版本。
* 配置 APT 源 
```
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```
* 安装 Yarn
```
$ sudo apt-get update
$ sudo apt-get install yarn
```
### CentOS / Fedora / RHEL
在 CentOS、Fedora、RHEL 上，你可以通过 RPM 包仓库安装 Yarn。
```
$ sudo wget https://dl.yarnpkg.com/rpm/yarn.repo -O /etc/yum.repos.d/yarn.repo
```
如果你尚未从 RPM 仓库中安装 Node.js , 需要一起配置 NodeSource:
```
$ curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
```
>[info] 有关中国镜像的使用，我们推荐 **科大源** : `https://mirrors.ustc.edu.cn/nodesource/rpm/pub_v{版本号}.x`

安装 Yarn
```
$ sudo yum install yarn
```
### openSUSE
在 openSUSE 上，您可以通过 Yarn 的 RPM 包仓库安装 Yarn。
```
$ sudo zypper ar -f https://dl.yarnpkg.com/rpm/ Yarn
$ sudo zypper in yarn
```
### Solus
在 Solus 上，您可以通过 Solus 仓库安装 Yarn。
```
$ sudo eopkg install yarn
```
### Alpine
在 Alpine Linux (3.6+) 上，你可以用 apk 安装 Yarn。
```
$ sudo apk add yarn
```
### Windows
在之前的教程中，我们安装了 `Chocolatey`，现在我们只需如此简单的安装：
```
$ choco install yarn
```
### Mac OS
你可以通过 Homebrew 包管理器安装 Yarn，如果没有安装 Node.js 它也可以安装。
```
$ sudo brew install yarn
```
如果您使用 nvm 或类似的东西，您应该排除安装 Node.js 以便使用 nvm 的 Node.js 版本。
```
$ sudo brew install yarn --without-node
```
## 通过 安装脚本 安装
>[info] 该方法通常适用于 通过编译安装 或者 nvm 管理的 Node.js

在 macOS 和通用 Unix 环境里安装 Yarn 的最容易方法之一是通过 shell 脚本。你可以在你的终端里运行下列代码来安装 Yarn：
```
curl -o- -L https://yarnpkg.com/install.sh | bash
```
## 通过 NPM 安装
>[warning] **当然，我们并不推荐使用 `npm` 进行安装 `yarn`，理由如下:**
>  在用基于 Node 的包管理器安装 Yarn 时，该包未被签名， 并且只通过基本的 SHA1 散列进行唯一完整性检查。 这在安装系统级应用时有安全风险。
>因为这些原因，高度推荐用你的操作系统最适合的方式来安装 Yarn。

方法很简单，您只需如此执行：
```
$ sudo npm i yarn -g
```

## 使 Yarn 成为 NodeBB 的默认包管理器
在安装 NodeBB 完成后， 删除 `./node_modules` 来确保环境干净。
对于切换的方法， 其实很简单。 我们只需要编辑 `config.json` ， 添加以下字段:
```json
{
    "package_manager": "yarn"
}
```
保存之后， NodeBB 以后的插件更新， 依赖更新都会通过 yarn 安装了。
由于之前我们删除了 `./node_modules`, 所以这时候我们手动执行 `yarn` 安装依赖。
等待指令执行完毕后， 我们即可通过 `./nodebb start` 启动 NodeBB。

>[info] 编写: a632079 & Yarn Docs
维护: PA Team
审核: PA Team
最后更新: 2018.05.06