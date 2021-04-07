异步处理：
- 将异步同步化，借助suspense函数
- error: 可以使用`<ErrorBoundary>`捕获

一些API：
atom:
数据：atom({key:,default:})
获取数据的值(读操作)：useRecoilValue(atom);
像state一样获取数据并且获得setter方法：useRecoilState(atom)
写atom的方法：useSetRecoilState(atom)

selector:
- pure function acccepts atoms or seletors, keep tarcks, those update, it re-evaluted
- avoid redundant state, while effciently computed as function
selector({key:, get: ({get})=>{}})  返回值可以认为是

selectorFamily: 可以在get函数中做副作用请求

组合起来可以起到一个Data-Flow Graph 的样子

concurrent Requests:
use a concurrency helper such as waitForAll to run them in parallel.
  - Promise.all
use waitForNone to handle incremental updates to the UI with partial data. 
  - Promise.race

Pre-Fetching
对react新特性：kick off fetching before rendering的支持
useRecoilCallback(({snapshot, set})
前置知识：
阻塞 vs 可中断渲染
阻塞渲染：一旦React开始渲染一次更新，它们不能中断包括创建新的 DOM 节点和运行组件中代码在内的工作
在 Concurrent 模式中，渲染不是阻塞的。它是可中断的。

在 Concurrent 模式中，React 可以 同时 更新多个状态
- 对于 CPU-bound 的更新 (例如创建新的 DOM 节点和运行组件中的代码)，并发意味着一个更急迫的更新可以“中断”已经开始的渲染。
- 对于 IO-bound 的更新 (例如从网络加载代码或数据)，并发意味着 React 甚至可以在全部数据到达之前就在内存中开始渲染，然后跳过令人不愉快的空白加载状态。？？？

Suspense：协助编排加载状态在UI中的显示
- 是数据获取库与react紧密整合
- 它能让喔有针对性的加载状态的展示
- 能消除race conditions

Fetch-on-render（渲染之后获取数据，不使用 Suspense，useEffect）
- 不可避免的导致瀑布问题：本该并行发出的请求无意中被串行发送出去

Fetch-then-render：接收到全部数据之后渲染，不使用 Suspense）
- 需要接受全部数据之后才能渲染
- 组件树层级高了之后很难维护 hoisting的代码

Render-as-you-fetch（获取数据之后渲染，使用 Suspense）
有了 Suspense，我们不必等到数据全部返回才开始渲染：获取数据->开始渲染->结束获取数据
- 可以在渲染之前开始获取数据
- 在点击事件时，并行获取代码和数据
- 解决竞态，在获取数据之后立马设置state
  - 将resource以同步的方式setState，这样就可以换到下一帧的state和async请求，就不会出现静态
- 没有catch，使用错误边界组件来捕抓层级下的所有报错信息 

concurrent UI模式
useTransition({timeout}): 延迟多久setState才生效
- startTransition 类型为函数。我们用它来告诉 React 我们希望的延迟的是哪个 state 的更新。
- isPending 类型为 boolean。此变量在 React 中用于告知我们该转换是否正在进行。

避免意外隐藏用户交互得内容
现在的交互方式变成了：pending->skeleton->Complete
suspense组件中的state的异步结果的更新要加上transistion

suspenseList：控制异步的返回结果的顺序
- 协调节点的展开顺序：`<Suspense>` 只会根据在树中的显示顺序来“展开”它们的内容 — 即使它们的数据在不同的顺序到达
