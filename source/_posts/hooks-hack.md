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

并且，上述问题的可以用 useReducer 来解决.React 会保证 dispatch 在组件的声明周期内保持不变。也可以用 useRef 去解决,这类似于 class 组件的 this

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

> useRef 还可以存储，prevProps。在 effect 中做更新赋值

> useReducer 是 Hooks 的作弊模式!!

如果我们需要依赖 props 去计算下一个状态，一般来说会把 props 放到 effect 的[]中

但是，也可以用 reducer 去读取，这样得把 reducer 放到函数里

```js
function Counter({ step }) {
  const [count, dispatch] = useReducer(reducer, 0)

  function reducer(state, action) {
    if (action.type === 'tick') {
      return state + step
    } else {
      throw new Error()
    }
  }

  useEffect(() => {
    const id = setInterval(() => {
      dispatch({ type: 'tick' })
    }, 1000)
    return () => clearInterval(id)
  }, [dispatch])

  return <h1>{count}</h1>
}
```

原因：dispatch 的时候，React 只是记住了 action - 它会在下一次渲染中再次调用 reducer。在那个时候，新的 props 就可以被访问到，而且 reducer 调用也不是在 effect 里。

这可以帮助我移除不必需的依赖，避免不必要的 effect 调用。

但，尽量不要这么做。

> 关于请求的竞态

简单的处理可以通过一个布尔值做权宜之计，因为 effect 每次都是新的，闭包缓存布尔值

```js
function Article({ id }) {
  const [article, setArticle] = useState(null)

  useEffect(() => {
    let didCancel = false

    async function fetchData() {
      const article = await API.fetchArticle(id)
      if (!didCancel) {
        setArticle(article)
      }
    }

    fetchData()

    return () => {
      didCancel = true
    }
  }, [id])

  // ...
}
```

> 可以学习的仓库

[蚂蚁金服 的 hooks 开源 https://github.com/umijs/hooks](https://github.com/umijs/hooks)
