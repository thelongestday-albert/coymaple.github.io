---
title: npm 常用命令
date: 2019-12-05 11:04:06
tags:
- node 
- npm
---

> 作为一名前端开发人员，npm 可以说是最熟悉也是最陌生的一个工具了。几乎每天的开发都要用到 `npm install` 和 `npm run <command-name>` 命令，但很多前端开发者对它的了解也就止步于此了。本文旨在介绍npm中除上面介绍的之外的命令。

<!-- more -->

# npm 
npm init 

## 安装模块

在项目中已经有 package.json 文件的情况下，可以执行 `npm install` 命令，npm 包管理器会自动解析 package.json 文件，自动下载项目依赖。

npm install 命令后面可以跟以下几个参数：

- -g 下载全局模块，在所有路径下面都可以使用，安装到项目里面的模块使用命令除了写到package.json script 属性内的外，还可以使用 npx 命令。

- --save,-S  将模块依赖关系写入到package.json文件的dependencies参数中

- --save-dev,-D   将模块依赖关系写入到package.json文件的devDependencies参数中

> devDependencies  里面的模块只用于开发环境，不用于生产环境，而 dependencies  是需要发布到生产环境的。

## 卸载模块
`npm uninstall <name>`

卸载全局模块 `npm uninstall -g <name>`

`npm un` 是 `npm uninstall` 的缩写形式。

## 修改npm源

`npm config set registry <URL>`

> 将 npm 源更换为淘宝源 `npm config set registry https://registry.npm.taobao.org/`。

上面这个命令执行成功之后，可以执行 `npm config get registry` 命令查看上面这条命令是否执行成功。

## 修改全局模块安装路径

`npm config set prefix <PATH>`

与修改npm源比较类似，这个命令成功以后，可以执行 `npm config get prefix` 查看命令执行的结果。

## npm init 

使用参数 `--yes` 来跳过执行 `npm init` 命令时的回答提问阶段，快速生成一个 package.json 文件。

`npm init --yes`

## 查看全局或项目中模块安装的路径

`npm root` 或 `npm root -g` 可以查看查看全局或项目中模块安装的路径。

## 查看npm安装的模块

`npm list` 和 `npm ls` 以及 `npm ll` 都可以查看当前项目中安装的模块列表。

这几个命令都可以在后面加参数 --depth=0 来过滤模块的层级

> Node模块搜索是从代码执行的当前目录开始的，搜索结果取决于当前使用的目录中的node_modules下的内容。

当当前项目中依赖在package.json 文件中出现，但是实际并没有下载时，npm 也会标记出来。如下图所示：

![](http://cdn.coymaple.com/jWysHoQVYLu7h1g.png)
## windows 电脑全局安装的脚本不能找到的问题

以管理员身份打开 windows powershell ,执行 `get-ExecutionPolicy` ，如果命令返回的不是 **RemoteSigned**，执行命令 `set-ExecutionPolicy RemoteSigned`。


## 其他

1、`npm view moduleName engines` 可以查看当前安装的模块依赖的 nodejs 版本。

在终端中输入 `npm view mocha engines` ，其命令结果如下：

![](http://cdn.coymaple.com/YMEjZDte2XF517c.png)