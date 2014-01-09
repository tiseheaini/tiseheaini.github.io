---
layout: default
title:  "让微博可以在本地调试之rails篇"
---
在rails 上折腾微博登录，会遇到一个小问题，那就是如何在本地调试，首先在用户输入完用户名和密码后会跳转到设置好的一个 callback 地址，例如 www.gongzuozheng.com/user/auth/callback 
在rails 上用 localhost:3000 来调试，这样是没办法进行调试的。网上很多教程是根据 php 环境来说明的，php 天生就是在 80端口上进行调试和更改，rails 当然也可以在 80 端口上启动 **rvmsudo rails server -p 80**  就可以，使用 **rbenv** 的用户也有相应的方法，不过这么恶心的方法对于有点强迫症的我是不能接受的。
找到一种很完美的解决方法解决方法。
思路： 
1 改动 /etc/hosts 文件，将域名请求转到本地
2 利用 nginx 的反向代理将 80 端口的请求转向 3000 端口上

虽说麻烦一点，不过这样去改，不会更改改变启动 rails 的方式，不用特意强调使用哪个端口。

打开 **/etc/hosts** 文件添加一行代码

    127.0.0.1  www.gongzuozheng.com

然后打开 nginx 配置文件 /etc/nginx/site-enabled/default 添加代码

    upstream www.gongzuozheng.com {
    server 127.0.0.1:3000;
    }
    server {
      ## 监听 80 端口
      listen 80;
      ## 项目目录
      root /home/xxx/xxx/xxx/public;
      index index.html index.htm;

      server_name www.gongzuozheng.com;
      ## 很重要，就是这行代码将用户请求转向 3000 端口上。
      location / {
        proxy_pass http://www.gongzuozheng.com;
      }
    }

这些写好后，重启 nginx 。
直接访问 www.gongzuozheng.com 就可以进行微博登录的测试了。
