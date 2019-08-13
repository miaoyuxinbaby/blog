---
title: safari下的那些坑
date: 2019-07-07 19:08:40
categories: 孤独的观测者
---

最近遇到的一些 safari 下的坑，多为样式问题

<!--more-->

- safari 下，input 圆角时会出现内阴影

解决方法:

```css
input {
  -webkit-appearance: none;
}
```

- safari 下，input 框光标和文字不垂直居中

解决方法

```txt
去掉height和line-height
使用font-size和padding撑起高度
```

- safari, 微信支付回调时，会新开一个 tab，支付宝好像也是

```txt
 避免使用sessionStorage来存储一些信息
```
