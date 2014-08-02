---
layout: post
title: "服务器Mcrypt.so加密库支持 未安装"
---

shopex 的模板开发需要在本地搭建好环境，然后将下载的模板在本地修改好后上传服务器。

shopex 的本地环境搭建一点都不容易，开始时想在 mac 上运行，不过因为 ZendOptimizer 没办法安装而不得不使用 windows。

window 上安装也没有想象中那么容易，ZendOptimizer 的问题在 windows 下顺利解决掉了，不过新的问题出现了 **服务器Mcrypt.so加密库支持 未安装**

查找了一下 php 安装扩展的方法，主流的方法就是将 php 主目录下包含的 **libmcrypt.dll** 和 **libmhash.dll** 文件复制到 `C:\windows\system32` 目录下
且在 php.ini 配置文件里找到 `extension=php_mcrypt.dll` 和 `extension=php_mhash.dll` 这两个语句，去掉注释，并重启 apache 。

按照操作进行了不下数十遍，还是不行，肯定是我打开的方式不对，这里注明一下，我用的搜索引擎是某度公司的，后来我用 mac 把自己的 ip 跳到另一个国度，使用某歌的搜索了一下，哇，答案就乖乖的躺在第一行。原来 **libmcrypt.dll** 文件还要复制到 `apache/bin` 目录下才能正常使用。

操作完毕后，果然问题解决了 shopex 的本地环境总算是搭建成功了。

放出网址 http://bbs.shopex.cn/read.php?tid-302286.html

EOF
