---
title: hooks hack
date: 2019-08-1 16:00:22
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

> 对于当某个值变化后去获取数据，如何保障接口数据按顺序返回（请求发出的顺序）,这方面 hooks 还不是很成熟。

类似的，我遇到了很多 hooks 的问题，包括目前的 react devtools 无法查看引用类型 hooks 的值等，有些找到了解决方案，有些，只能采用一些 hack 的方式去实现。

算是尝过鲜了，但全面拥抱 hooks，还是再缓一缓吧。虽然 hooks 的心智模型确实很顺滑。
