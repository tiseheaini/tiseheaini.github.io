---
layout: post
title: "can't convert Symbol into String 解决方法"
date:   2013-11-03 17:05:00
---

在设置 i18n 时 上一秒还没有问题，下一秒就出现问题了

错误信息 

    ActionView::Template::Error (can't convert Symbol into String):
                                                   
    27:       <div>生日</div><br />
    28:         <%= f.date_select :birth %>

提示我 28行代码出问题了，这不挺好吗？ 我也没改这段代码。

为什么会这样呢？？因为语言环境变了，原来 *date_select* 会自动生成时间的下拉菜单，菜单里面会有 January February 一直到 12月，但你改变了语言环境，rails 就不知道怎么回事了。所以就出问题了。

解决问题的方法很简单

在你原来的 yml 文件中添加，比如我的 yml 文件是 zhCN.yml

    zhCN:
      date:
        order:
        - :year
        - :month
        - :day
然后把表单也改过来，不让 rails 去用中文显示月份

<%= f.date_select :use_month_numbers => true %>
上面的代码中 *:use_month_numbers => true* 就是告诉 rails 月份使用数字，这样 *date_select* 就可以正常生成下拉菜单了

如果有问题 可以给我发邮件
