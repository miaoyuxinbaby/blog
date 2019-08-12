---
title: hooks hack
date: 2019-07-22 22:08:40
categories: 孤独的观测者
---

最近使用 react 的过程中，遇到一些问题

<!--more-->

```js
// state -> list1,list2,list3
// 根据hooks的语法，需要这么写，但是，比如v1变化，会重置v2,v3,list2时，这时effect会执行4次。明细是不合理的。
// hack的方式是，所有数据都用ref或者写到函数外，state里就放一些，用来控制强制刷新页面的变量，需要时就修改以达到刷新页面的目的

useEffect(() => {
  let i = list1.filter(_ => _.value === v1)
  同理，i2,i3


},[v1,v2,v3,list1,list2,list3]);
```
