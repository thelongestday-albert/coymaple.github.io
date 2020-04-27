---
title: MongoDB
tags:
- MongoDB
---
<!-- # MongoDB -->

若没有特殊说明，本文涉及到的 MongoDB 安装在64位 win10 操作系统上，版本为 v4.0.2。

<!-- more -->

## 安装

安装 MongoDB 的时候，除了下面两个地方，基本上一路点击 next 就好了。

在 `Service Configuration` 这一步可以勾选 `Install MongoD as a Service` （默认勾选）。勾选后会在系统服务中添加一个名为 MongoDB 的服务。如果不勾选就只安装二进制文件，不作为windows服务。然后选择 `Run Service as Network Service user`（以网络服务用户的身份运行）（默认）。

在 `Install MongoDB Compass` 这一步 ，要把左下角的勾选去掉，MongoDB Compass 是一个官方图形化操作界面，勾选后基本下不动。

## MongoDB 服务

默认情况下，MongoDB 安装完成后会在系统服务中添加一个 MongoDB 服务。

如果在安装的时候没有勾选 `Install MongoD as a Service` ，也可以用 `mongod --install`命令将 MongoD 设置为系统服务。 这个命令需要指定一些其他参数，参数可以指定从配置文件中获取，也可以执行命令的时候直接指定，如下所示：
- `mongod --config D:\MongoDB\mongo.config --install`
- `mongod --logpath "logpath" --logappend --dbpath "dbpath" --serviceName MongoDB  --install` 

> 配置文件的位置发生改动时也要通过上述命令重新生成 MongoDB 服务。

开启 MongoDB 服务后就可以通过 mongo 命令链接 MongoDB 数据库了。

<!-- MongoDB服务和 mongod 的关系？ -->

如果 MongoDB 服务没有设置为开机自启动，每次开机后都要手动启动该服务，通过系统的计算据管理工具去开启服务较为麻烦，在已经知道服务名的情况下，可以使用如下所示的命令快速启动或停止 MongoDB 服务。

- 启动服务 `net start MongoDB`
- 停止服务 `net stop MongoDB`

## 配置

MongoDB 默认的配置文件为 **mongod.cfg** ，相对于安装目录的路径为 `.\Server\4.0\bin`。

> 注：配置文件所有的修改都需要重启 MongoDB 服务才能生效。

### 日志配置

```yaml
systemlog
  destination: file
  path: ""
  logAppend: true
```

path 应为日志文件的全路径，当路径中有空格时，为了避免解析错误，可以用双引号将路径包裹起来。

logAppend 控制日志文件的追加方式。其值为true时，**重启后将日志追加到日志文件的末尾**；值为false时，**每次重启后都会备份之前的日志文件然后生成一个新的日志文件**。

> 注意 windows 下路径的写法。

### 数据存储配置

```yaml
storage:
  dbPath: "c:\MongoDB\db"
```

dbPath 指定数据文件存储路径

### 网络配置

```yaml
port:
  port: 27017
  bindIp: 127.0.0.1
```
MongoDB 默认监听的端口为 **27017**。更改 MongoDB **监听的端口**和**绑定的IP地址**后，链接到数据库需要指定端口号和域名，`mongo 127.0.0.1:3000`。

绑定多个IP需要用逗号分隔多个ip地址，就像这样 `bindIp: 127.0.0.1,61.61.61.61` 。


## 常用命令

使用 mongo 命令连接到数据库以后，就会进入到 MongoDB 的 shell 环境，在这里面可以对数据库进行操作。

`show dbs` 可以获得所有的数据库

`show collections` 可以i获得当前数据库内的所有集合（表）

`use dbname` 可以切换到指定的数据库下面，如果没有会创建该数据库。

为了方便说明，下面的CRUD操作都将会操作具体的集合。

https://docs.MongoDB.com/manual/crud/

insert() 函数可以在集合中插入一个文档

insert() 的参数是一个类似于JS对象，这个对象的属性不用与已有的数据保持一致，多一个少一个都可以。

```
db.student.insert({"name":"xiaoming","age":17})
db.student.insert({name:"laowang","age":17,"gender":"male"})
```

find() 函数可以用来查询数据，当该函数没有参数或仅有一个空对象参数时，默认查询集合所有内容。还可以指定查询条件查询。

```
db.student.find()
db.student.find({})
db.student.find({name: "xiaoming"})
```

在查询结果中可以看到多了一个字段 _id ，MongoDB 默认在使用inset() 函数插入数据时都会插入一个 _id 字段，该字段可以当作关系型数据库中的主键使用。

当文档中的字段数据内容过多，在终端中一行放不下时，可以在 find() 函数后接 pretty() 函数，该函数可以美化find查询结果在终端中的显示形式，使看起来更舒服一些。

删除数据可以使用 remove() deleteOne() deleteMany() 函数。

```
// 删除用户名为 laowang 的数据
db.student.remove({name: "laowang"})
// or
db.student.deleteOne({name: "laowang"})
```

更新数据可以使用 update() updateOne() updateMany() 等函数，

```
// 修改 xiaoming 的年龄为 18
db.student.update({name:"xiaoming"},{$set: {age: 18}})
// or
db.student.updateOne({name:"xiaoming"},{$set: {age: 18}})
```

高级查询相关内容见官方文档 https://docs.MongoDB.com/manual/tutorial/query-documents/

函数 sort() 可以将 find() 的查询结果安按照顺序（1 正序 -1 倒序）排序。

```
// 按照年龄从小到大排序
db.student.find().sort({age: 1})
```

<!-- 索引的建立与查询 getIndexes() ensureIndexes() -->

## 用户权限管理

有些时候为了安全着想，会为数据库创建用户，当操作者需要执行某些操作时，就要登录用户进行验证。

### 创建用户

```js
db.createUser({
  user: "accountUser",
  pwd: "password",
  roles: ["readWrite","dbAdmin"]
})
```

创建的用户会自动关联到当前正在操作的数据库。不能在 local 数据库下面创建用户。

### 开启用户认证

```yaml
security:
  authorization: enabled
```

默认 disabled ，不需要权限认证。设置为 enanled 后，访问指定的数据库需要验证

### 验证登录

```js
db.auth("sa","123456")
```

## 备份与恢复 

备份 `mongodump -h dbhost -d dbname -o dbdirectory`

恢复 `mongorestore -h  dbhost -d dbname --dir dbdirectory`

## 参考文章
- MongoDB安装及设置服务启动<https://blog.csdn.net/dandanfengyun/article/details/95497728>

