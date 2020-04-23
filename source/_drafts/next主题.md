---
title: Next主题
tags:
---

Next 是 HEXO 的一款主题，其简洁。

<!-- more -->

## 解决菜单不准问题

在 Next 的官方文档中一开始就介绍了如何设置语言，但是经过实际操作后发现，虽然按照文档中设置的那样做了，但是设置没有生效，菜单栏显示的是看不懂的语言。

这是因为Next主题的 **language** 目录下面并没有一个文件与 `language: zh-Hans` 这一配置相呼应，解决方法也很简单，将 **zh-CN.yml** 文件重命名为 **zh-Hans.yml** 。

## 设置引用区块边框颜色

Next 默认的引用区块的颜色是灰色，而我习惯于看到绿色的引用区块边框，于是我就设法将之改成了绿色。

引用区块相关的样式代码定义在 `source/scaffolding/base.styl` 文件中，并未直接指定引用区块边框的颜色，而是使用了一个变量。这个变量定义在 `source/_variables/base.styl` 文件中，因为在该文件中定义的很多颜色没有一个觉得可以作为左边框的颜色，故在该文件中新增了一个变量 `$color-success = #5cb85c`。同时将指定边框颜色的变量替换为 $color-success 。

## 首页显示部分文章内容

官方提供了三种方式来达成这个目的，个人推荐使用前两种方式。

http://theme-next.iissnan.com/faqs.html#read-more

## 添加备案信息

