---
title: react hooks 问题汇总
date: 2019-10-1 16:00:22
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

> 即使观测值没有发生，我也想去执行 effect 怎么办？

hack 方式时，额外观测一个递增的数字，用来做无改动时的执行导火索。**希望社区早点给出最佳实践**

救救菜鸡我

```js
useEffect(() => {
  // 要用id1,id2去 list1,2中取数据 name1,name2
  // 问题1：如何控制 list1 和 list2变化的时候，不执行effect
  // 问题2：如何控制，id1和id2都更新后再执行effect
}, [id1, id2, list1, list2])
```

等等，我为什么一定要在 effect 中获取数据，我就不能再 effect 外面获取吗？？卧槽。我是不是傻了。

并且，上述问题的可以用 useReducer 来解决.React 会保证 dispatch 在组件的声明周期内保持不变。

```js
function reducer(state, action) {
  const { list1, list2 } = state

  if (action.type === 'getname1')
    return { name1: list1.filter(_ => _.id === action.value), name2: '' }
  // ...
}

const [state, dispatch] = useReducer(reducer, initialState)
const { name1, name2 } = state

useEffect(() => {
  // 获取name1
  dispatch({ type: 'getname1', value: id1 }) // Instead of setCount(c => c + step);
}, [id1, id2, dispatch])
```

相比于直接在 effect 里面读取状态，它 dispatch 了一个 action 来描述发生了什么。这使得我们的 effect 和 list1,list2 状态解耦。我们的 effect 不再关心怎么更新状态，它只负责告诉我们发生了什么。更新的逻辑全都交由 reducer 去统一处理.

> 关于连续多次改动 state，要取最新的值，可以通过 useRef 解决，它像一个盒子，永远装着最新的数据
