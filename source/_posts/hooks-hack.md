---
title: react hooks 问题汇总
date: 2019-10-1 16:00:22
categories: 孤独的观测者
---

最近使用 react 的过程中，遇到一些问题

<!--more-->

我遇到了很多 hooks 的问题，包括目前的 react devtools 无法查看引用类型 hooks 的值等，有些找到了解决方案，有些，只能采用一些 hack 的方式去实现。

> 即使观测值没有发生，我也想去执行 effect 怎么办？

hack 方式时，额外观测一个递增的数字，用来做无改动时的执行导火索。

```js
useEffect(() => {
  // 要用id1,id2去 list1,2中取数据 name1,name2
  // 问题1：如何控制 list1 和 list2变化的时候，不执行effect
  // 问题2：如何控制，id1和id2都更新后再执行effect
}, [id1, id2, list1, list2])
```

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
