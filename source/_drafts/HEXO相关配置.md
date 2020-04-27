---
title: HEXO相关配置
tags:
---

## 编辑文章

创建草稿文章

## 自动部署

目标：HEXO 博客内容完成以后，将内容上传到指定分支以后，通过某种方式实现监听分支上传事件，该事件触发时，执行 hexo d 命令，该命令执行成功以后，将结果上传至目标服务器。

通常监听分支上传事件并不是在github上执行，而是在一些第三方的自动构建平台，比如 TravisCI，分支上传事件触发后，自动构建平台将分支内容拉取到虚拟环境中（通常是一个docker虚拟容器），在这个虚拟容器中执行一些命令。

使用HEXO框架写作文章，每次写完之后都要使用 hexo clean 和 hexo generate ，然后使用ftp或sftp工具上传到服务器上，有的时候需要一个一个文件地上传，时间效率太低了。

```yaml
env:
  global:
  - 'SFTP_USER=[user]'
  - 'SFTP_PASSWORD=[password]'
  - 'SFTP_KEY=[base64-encoded-rsa-key]'
after_success:
- echo "${SFTP_KEY}" | base64 --decode >/tmp/sftp_rsa
- curl --ftp-create-dirs
       -T filename
       --key /tmp/sftp_rsa
       sftp://${SFTP_USER}:${SFTP_PASSWORD}@example.com/directory/filename
```

需要先加密部分文件
安装ruby http://www.ruby-lang.org/zh_cn/documentation/installation/#yum

ruby 更换源 gem sources --add https://gems.ruby-china.com/
查看软件源列表 gem sources -l
删除源 gem sources --remove https://rubygems.org/

安装软件出错
```
/usr/bin/ruby extconf.rb
mkmf.rb can't find header files for ruby at /usr/share/include/ruby.h
```
yum install ruby-devel

https://docs.travis-ci.com/user/encryption-keys/

https://docs.travis-ci.com/user/deployment/custom/