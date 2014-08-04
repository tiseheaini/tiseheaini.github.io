---
layout: post
title: "在 mint xfce 上安装 fcitx"
---

在 mint xfce 上安装 fcitx 多数是因为 依赖没有装好，安装其他 linux 不成功也是同样的原因。
这是之前的内容，重新整理一下，记录下来。

首先加上软件源，有兴趣的同学可以看一下 add-apt-repository 这是私有源安装软件，对于 linux 来说简化了安装过程。

执行下面的代码来添加私有软件源

```
sudo add-apt-repository ppa:fcitx-team/nightly
```

更新一下，接着进入真正的安装


```
sudo apt-get update

sudo apt-get install fcitx fcitx-config-gtk fcitx-sogoupinyin fcitx-table-wubi

sudo apt-get install fcitx-ui*
```

上面命令依次运行后重启

```
sudo reboot
```

重启完成后就可以用 linux 版本的 搜狗输入法了。

EOF