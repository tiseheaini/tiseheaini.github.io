---
layout: post
title: "Rails 下实时生成多尺寸图"
tags: []
---

在开发网站时，同一张图片时常需要生成多张不同尺寸的缩略图片。

一般有两种策略生成缩略图，一种在上传图片时，生成所有尺寸的缩略图，另一种是请求指定尺寸时再生成缩略图。

前一种方式会有一些限制，当我们需要一种新的图片尺寸时，需要使用脚本重新生成。而后一种方式就比较灵活。（淘宝也是采用这个方案，只是淘宝使用ImageMagick。）

网上有一种是借助 lua_nginx module 调用 GraphicsMagick 命令生成缩略图片，不过 nginx 默认发布版本是不包含 lua_nginx 模块，需要重新编译安装，这样太麻烦，实现方式并不灵活。我们需要一种更灵活的方式来实现。

使用 Nginx + API 的方式适用于各种 Web 框架。

在 Nginx location 中将图片格式的请求全部拦截下来处理，检查请求的图片是否存在，存在则设置 expires http 缓存。否则就转向 API 去生成缩略图，并返回新生成的缩略图。

```nginx
server {
    ## 监听 80 端口
    listen 80;
    ## 项目目录
    root xxx;

    server_name example.com;

    location ~* ^(/pictures/).*\.(jpg|jpeg|png|gif|bmp)$ {
        #如果文件存在,则设置过期时间,关闭访问日志
        if ( -f $request_filename ) {
            expires max;
            access_log off;
        }

        #如果文件不存在,则rewrite到产生图片的脚本文件autoimg.php
        if (!-f $request_filename) {
            rewrite \/(\w*_\w*\.(jpg|jpeg|png|gif|bmp)) /pictures/resize?file=$1;
            expires max;
        }
    }

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_redirect off;
      proxy_pass http://xxxx;
    }
}
```

nginx 中最重要的代码就是找到某些目录下的所有需要处理的图片，查看图片是否存在，不存在则 rewrite 到指定的 API 下处理。

```nginx
    #匹配 /pictures 路径下的所有常见图片格式，并处理。
    location ~* ^(/pictures/).*\.(jpg|jpeg|png|gif|bmp)$ {
        #如果文件存在,则设置过期时间,关闭访问日志
        if ( -f $request_filename ) {
            expires max;
            access_log off;
        }

        #如果文件不存在,则rewrite到生成缩略图的路由 /pictures/resize
        if (!-f $request_filename) {
            rewrite \/(\w*_\w*\.(jpg|jpeg|png|gif|bmp)) /pictures/resize?file=$1;
            expires max;
        }
    }
```

ruby 部分的代码只是一个示例，调用 API 处理图片，并将处理完的图片返回。

```ruby
  def resize
    #找到图片，使用 MiniMagick 改变图片尺寸和图片质量
    #pic = Picture.find(name)
    #image = MiniMagick::Image.open()
    #image.resize
    #image.quality
    #send_file image_path, type: image.mime_type, disposition: "inline"
  end
```
