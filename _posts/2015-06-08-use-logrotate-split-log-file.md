---
layout: post
title: "使用 logrotate 分割日志文件"
tags: []
---

logrotate 是 linux 上的一个小工具，用来将日志按每日，每周或每月处理。

Rails 项目默认的日志文件放在了 log 目录下，有 development.log，production.log 和 test.log。
在服务器上 production.log 是最大的。不经意间，一个项目的 log 文件已经有 100M 了。需要注意一个这个问题了。

logrotate的配置在 `/etc/logrotate.conf`

用户使用的配置的放在了 `/etc/logrotate.d/`

```
compress 通过gzip 压缩转储以后的日志

nocompress 不需要压缩时,用这个参数

copytruncate 用于还在打开中的日志文件,把当前日志备份并截断

nocopytruncate 备份日志文件但是不截断

create mode owner group 转储文件,使用指定的文件模式创建新的日志文件

nocreate 不建立新的日志文件

delaycompress 和 compress 一起使用时,转储的日志文件到下一次转储时才压缩

nodelaycompress 覆盖 delaycompress 选项,转储同时压缩.

errors address 专储时的错误信息发送到指定的Email 地址

ifempty 即使是空文件也转储,这个是 logrotate 的缺省选项.

notifempty 如果是空文件的话,不转储

mail address 把转储的日志文件发送到指定的E-mail 地址

nomail 转储时不发送日志文件

olddir directory 转储后的日志文件放入指定的目录,和当前日志文件在同一个文件系统

noolddir 转储后的日志文件和当前日志文件放在同一个目录下
```

放一段一个 Rails 项目使用的配置
```
/home/deployer/rails_project/current/log/production.log {
  daily
  dateext
  rotate 7
  copytruncate
}
```
