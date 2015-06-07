---
layout: post
title: "使用 Rake::Mime 库中的方法查询文件的 mime-type"
---

最近写的一个网站功能需要根据文件的 mime-type 来判断是否允许上传。
mime-tpye 全称为“多用途互联网邮件扩展”，是一个互联网标准，使 Email 可以支持更多非文本格式文件。

可以根据文件的后缀名查找对应的 mime-type，网上有人搜集了常见的后缀名。比如，http://www.sitepoint.com/web-foundations/mime-types-complete-list/

通过这个网页没有找到常见的后缀名，可以使用 Rake 类库提供的方法来查找。(需要注意一点，这里的命令需要在 rails console 里面执行)

```ruby
Rack::Mime.mime_type('.foo') # => "application/octet-stream"
Rack::Mime.mime_type('.doc') # => "application/msword"
```

#####参考页面:
 - http://rack.rubyforge.org/doc/Rack/Mime.html
 - http://www.sitepoint.com/web-foundations/mime-types-complete-list/
