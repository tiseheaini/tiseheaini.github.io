---
layout: post
title:  "关于coolxueyuan小项目该注意的地方"
date: 2012-10-26 13:23:00
---

先说一声抱歉，现在才写上这些问题。

首先，项目中用 gem  carrierwave 用于图片的上传， carrierwave 中用到了rmagick 去处理图片的缩放。

当大家使用 bundle install 命令时就会提示错误，错误的意思就是没有安装 rmagick ，可能大家会疑惑，难道运行 bundle install 时不是安装 rmagick 吗？？不是那个样子的。

所以解决的办法就是先去安装 rmagick 。

在 linux 上安装 rmagick 时需要两个库，imagemagick 和 libmagickwand-dev 。

需要运行这条命令 sudo apt-get install libmagickwand-dev imagemagick 去安装依赖库。

这是你再去执行 bundle install 时就不会有刚才的错误信息了。
