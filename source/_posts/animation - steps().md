---
title: animation 属性中的 steps 功能符
date: 2019-03-31 18:34:15
categories: 少女日记
---

近日因夕芷宝宝连续 4 个月未投稿，遂潜入其 QQ 群，潜水 N 天后，手贱点入了直播间，看到礼物那边有动画，好奇心一上来，就打开了 F12.

让我又回忆起了这种实现动画的方式，没有补间动画的**帧动画**。

<!--more-->

本以为是 gif 动画，因为还是比较，emmm，亮瞎狗眼的。
后来发现，图片是一张 png，然后很长，研究了下发现是用的 css3 的 animation 属性做的动画

```css
animation: 1.76s steps(22) infinite frame-gift-30088-72;
```

steps(number, position)

number: 表示把我们的动画分成了多少段。

position
关键字。表示动画是从时间段的开头连续还是末尾连续。支持 start 和 end 两个关键字，含义分别如下：

start：表示直接开始。
end：表示戛然而止。是默认值。
