
---
title: Hexo搭建个人博客
tags:
- node
---

这篇文章是对知乎专栏文章[超详细Hexo+Github博客搭建小白教程](https://zhuanlan.zhihu.com/p/35668237)的一个补充，首先感谢作者，按照作者的提示，成功地搭建起了个人博客系统。

文章中第三步 安装Hexo，作者在安装完hexo-cli 模块以后继续安装 hexo 模块，其实 hexo init 命令在执行之后，生成的 package.json 文件中模块依赖第一项就是 hexo ，可以在执行完 hexo init 命令之后，执行 npm install 安装包括 hexo 在内地依赖模块。有一点需要注意，就是 hexo init命令执行要求当前的工作目录必须是空文件夹。

记一下 Hexo 的命令。

- hexo g 生成静态文件
- hexo s 在本地浏览器中浏览博客网页
- hexo new post "article title" 新建一篇文章，文章保存在 \source\_posts 下面，文件名是 article title.md
- hexo new post 执行后会在路径 \source\_posts 下面建立一个 post.md 文件。
- hexo d 部署至服务器，可能会是 github