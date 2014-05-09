---
layout: post
title:  "crontab 执行 ruby 脚本"
date:   2013-03-23 01:35:00
---

最近在折腾使用服务器发微博的功能，这个功能实在是太折腾人了，开始的时候使用最通俗的方式，用 OAuth 去认证，然后再去发微博，不知道怎么的，OAuth已经不被支持，只支持 OAuth 2.

需要在服务器端发微博，每个小时去更新一次微博，还要读取数据库，加上一些逻辑判断。

这些需求可以用 rails去实现，可是不能够定时去执行，需要使用 linux的 crontab功能。

记得很久以前用过一次，是用任务备份数据库的资料，现在已经忘记了。

凭着印象去使用 crontab，开始的时候总是不能够执行命令。开始时怀疑是自己用错了。

{% highlight bash %}
*  *  * * * sudo  echo 'hello world!' >> /home/deploy//text.txt
taill -f /home/deploy/text.txt  
{% endhighlight %}

用这两条命令去调试，发现没错。

后来才知道，很多细节的地方不知道。

首先 crontab任务列表里面路径要写全，好像 ~/text.txt 是不支持的。

命令行里直接 ruby ~/xxx.rb 就可以了 可是在 crontab列表里面就不行，要写出 ruby 的完整路径。

使用 which ruby 查出 ruby 的完整路径。

推荐给大家的方法就是写一个脚本文件，然后用 crontab定时执行这个脚本文件。

写一个脚本文件可以方便的去执行这些任务。

这是 crontab任务列表的样式，然后写一个 sh脚本文件。

    *  *  * * * root  /bin/bash /home/deploy/weibo.sh

这是脚本文件的样式

{% highlight bash %}
#!/usr/bin/
/home/deploy/.rvm/rubies/ruby-1.9.3-p385/bin/ruby 
/home/deploy/weibo.rb
{% endhighlight %}

脚本文件中不要忘了写上 #!/usr/bin/ 这行来加载环境变量

有人说还要 source ~/.bashrc 去重写加载 bash脚本，不够我测试的时候没有这一行也没有问题，不过可能是 ubuntu的系统没问题，其他的系统就不知道了

weibo.rb 文件就是普通的 rb脚本文件，ruquire 一些库，然后执行一些程序。

crontab 还是有很多坑的，希望大家注意。
