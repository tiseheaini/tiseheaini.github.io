---
layout: default
title:  "在 rails 中使用 MySQL"
date:   2012-11-11 01:07:00
---

大多数新人都会使用 MySQL ，好多人都是从 MySQL 开始使用和学习数据库的，本人就是这样的。由于这样的原因，在不管是使用 php 还是 rails 我都是会用 MySQL 来当项目的数据库。

第一，我用 MySQL 用的比 Mongodb 多，比其他的都熟练，遇到问题也容易去解决。

第二，MySQL 确实很简单，安装简单，配置简单，资料也比其他的数据多。

我第一次在 rails 中使用 MySQL 时遇到了很多问题，在这里写出来等新人遇到相同问题时可以用我写的方法去解决。

很多新人都是看 《敏捷开发》开始入门的，rails 默认是使用 SQLite 3 当数据库的，但真正部署时继续用就不合适了。

config/database.yml 文件中是这样写的

    production:
      adapter: sqlite3
      database: db/production.sqlite3
      pool: 5
      timeout: 5000

《敏捷开发》中要改的地方很多

    production:
      adapter: mysql2
      encoding: utf8
      reconnect: false
      database: depot_production
      pool: 5
      username: username
      password: password
      host: localhost

但当我按照书上改的时候，会提示错误。怎么会有错误呢？？

书上给的模板是有问题的，而且并没有告诉读者在修改时要注意什么。

    production:                   
      adapter: mysql2             
      encoding: utf8              
      reconnect: false            
      database: databases_production
      pool: 5                     
      username: root              
      password: '123456'          
      socket: /var/run/mysqld/mysqld.sock

这个模板是比较好的。

可以在这里查到更详细的解释，http://guides.ruby-china.org/configuring.html#3-12

host 那一行感觉可有可无，当然如果你的数据库在另外一台主机上就要去修改它。

username 和 password 这两行是一定要改的。首先 密码可以为空，只要空着就可以了。如果密码为纯数字，那么一定要用 ‘ ’ 括起来，不然以 yaml 的读取方式会出错，但你给它一个字符串，它就不会出错了。

socket 这一行一定要写上，开始时我没有写，就会报错，rails 找不到 MySQL 的 引用文件也就无法使用 MySQL 去工作了。

这样修改以后应该就可以正常工作了，记得 使用 rake db:create 去创建数据库，不然会提示你找不到数据库的错误。

如果有问题可以给我发邮件。
