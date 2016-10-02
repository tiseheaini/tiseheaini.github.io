---
layout: post
title: "回帖频率限制 -- redis 实现"
tags: ['ruby', 'rails', 'redis', '频率限制']
---

服务器后台会经常用到一些手段来防止用户恶意灌水刷帖，前段时间新上线的网站遇到了这样的问题，参考了一些资料之后决定使用 redis 来实现对操作频率的限制。

实现对用户的操作频率限制，需要实现对用户在周期时间内操作的统计。

具体实现需要解决这些问题：

>如何计算周期时间。<br />
>如何使清除过期数据。

使用类似 redis key-value 数据库可以很方便的设计出符合上面条件的程序。

对用户回帖频率限制的实现原理

每次回帖前检查 “用户:id 在 帖子:id 中 回帖 特定时间段” 的操作次数，如果大于规定的操作次数，则给出警告提示，不允许操作。

我们将使用使用公式来计算时间段：

```ruby
## (时间戳 % 3600) / 60 以1分钟为一次统计
## 2015/08/01 12:01:00 => 0
## 2015/08/01 12:01:21 => 1
## 2015/08/01 12:01:41 => 2

(时间戳 % 周期) / 时间切片
```

在 redis 里面可以根据 key 来设置过期时间，来防止 key 无限增加的问题。

伪码如下:<br />
假设当前 user id 为2，在 id 为 5 的 topic 中添加回复，且每分钟的回复操作不能超过 3 次。

```ruby
## 检查当前 "user:2:topic:5:comment:create:1" 是否存在
key = "user:2:topic:5:comment:create:1"
if redis.exists(key) {
  ## 如果操作次数 >= 3 次就给出警告，不允许操作，否则在 redis 键中 +1
  if redis.get(key) >= 3 {
    printf "您的操作频率过高，请等一等"
  } else {
    redis.incr(key)
  }
} else {
  ## 如果 "user:2:topic:5:comment:create:1" 不存在，那么就创建 redis 键
  ## 并设置过期时间(时间最好设置之前设置的时间切片的秒数)
  redis.incr(key)
  redis.expire(key, 60)
}
```

看一下 redis 中 key <br />
假设当前时间为 2015年8月2日 12点 1分 00秒，一分钟内不超过 3次操作。<br />
redis 里面只会有三种情况,且 key 会在下一分钟自动过期

```ruby
## 2015/08/01 12:01:00 -- 2015/08/01 12:01:20
"user:2:topic:5:comment:create:0"
## 2015/08/01 12:01:21 --  2015/08/01 12:01:40
"user:2:topic:5:comment:create:1"
## 2015/08/01 12:01:41 --  2015/08/01 12:02:00
"user:2:topic:5:comment:create:2"
```

key 会自动过期不会产生冗余信息。这种方法也可以用在限制登录次数的功能上。