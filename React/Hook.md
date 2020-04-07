$$Hook$$
*可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。*
- 可选、向后兼容、可用
- 代码可读性更强
- 组件树层级变浅
  
- Hook简介：
  - Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数
  - 不能在 class 组件中使用
  
**useState**
- useState 覆盖式更新状态
- 允许你在 React 函数组件中添加 state 的 Hook
- 在函数组件中，我们没有 this，所以我们不能分配或读取 this.state。我们直接在组件中调用 useState Hook
- 函数api： 
  - 参数：初始 state，可以使用数字或字符串赋值
  - 返回值：当前 state 以及更新 state 的函数
  - *state 只在组件首次渲染的时候被创建。在下一次重新渲染时，useState 返回给我们当前的 state*
    - 返回的变量在函数的生命周期内，但是state会被 React 保留
- 需要多个state变量时，可以多次声明，可以将相关数据分为一组
- 函数式更新
  - 如果新的 state 需要通过使用先前的 state 计算得出，那么可以将函数传递给 setState
    - `setCount(prevCount => prevCount - 1)`
- 惰性初始 state
  - 如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state：initialState 参数只会在组件的初始渲染中起作用
- 跳过 state 更新
  - *调用 State Hook 的更新函数并传入当前的 state 时，React 将跳过子组件的渲染及 effect 的执行。*
  - ？？？


**useEffect**：
> - 数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用
> - 可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。
> - 解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题
- 一个 Effect Hook，给函数组件增加了操作副作用的能力
- 使用useEffect时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数。由于副作用函数是在组件内声明的，所以它们可以访问到组件的 props 和 state
  - 默认：每次渲染后调用副作用函数 —— 包括第一次渲染的时候。
  - 可以通过返回一个函数来指定如何“清除”副作用
  ```js
    useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  ```
  - 在这个示例中，React 会在组件销毁时取消对 ChatAPI 的订阅，然后在后续渲染时重新执行副作用函数。
  - 跟 useState 一样，你可以在组件中多次使用 useEffect ：
- useEffect 做了什么
  - Hook可以告诉 React 组件需要在渲染后执行某些操作。React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它
- 为什么在组件内部调用 useEffect？
  - 将 useEffect 放在组件内部让我们可以在 effect 中直接访问 count state 变量（或其他 props）(closure)
- useEffect 会在每次渲染后都执行吗？
  - 是的，默认情况下，它在第一次渲染之后和每次更新之后都会执行。
- 因为在渲染后更新，useEffect中可以得到最新的state值，并在更新完 DOM 后执行effect
  
- react 将在组件更新前刷新上一轮渲染的 effect。

> 提示：  
> 与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 useLayoutEffect Hook 供你使用，其 API 与 useEffect 相同。

- 取消订阅：
  - 如果你的 effect 返回一个函数，React 将会在执行清除操作时调用它
    - 每个 effect 都可以返回一个清除函数。如此可以将添加和移除订阅的逻辑放在一起。
  - React 会在组件卸载的时候执行清除操作

为什么 effect 的清除阶段在每次重新渲染时都会执行，而不是只在卸载组件的时候执行一次？
- 为了保证mount、update、unmount时的一致性
- update中可以通过prevProps和prevState来比较逻辑，然后跳过更新
  - 要传递数组作为 useEffect 的第二个可选参数，当不更新时，即告诉React 跳过对 effect
  - 遍历数组的值做一个浅比较
**确保数组中包含了所有外部作用域中会随时间变化并且在 effect 中使用的变量**
**想执行只运行一次的 effect，传一个空数组**  更接近我们DidMount和unMount模式

**Hook使用规则**
- 只在React 函数的最顶层使用 Hook
  - 不能在if或者for中调用hook，因为他们破坏了hook被调用的一致性(多次渲染要保持调用顺序不变)
- 只在 React 函数中调用 Hook
  - 在 React 的函数组件中调用 Hook
  - 在自定义 Hook 中调用其他 Hook

**自定义 Hook**
自定义 Hook 是一种自然遵循 Hook 设计的约定，而并不是 React 的特性。
自定义 Hook 必须以 “use” 开头
在两个组件中使用相同的 Hook 不会共享 state ；是一种重用状态逻辑的机制(例如设置为订阅并存储当前值)

- 可以在多个 Hook 之间传递信息，因为hook之间获取/传递的值都是最新值
- 不必过早的拆分hook

**使用useReducer**
- useReducer API
> 有个复杂的组件，其中包含了大量以特殊的方式来管理的内部状态。useState 并不会使得集中更新逻辑变得容易，因此你可能更愿意使用 redux 中的 reducer 来编写。
- api: `const [state, dispatch] = useReducer(reducer, initialArg, init);`
> React 会确保 dispatch 函数的标识是稳定的，并且不会在组件重新渲染时改变。这就是为什么可以安全地从 useEffect 或 useCallback 的依赖列表中省略 dispatch。
- 惰性初始化
  - 将 init 函数作为 useReducer 的第三个参数传入，这样初始 state 将被设置为 init(initialArg)
- 跳过 dispatch
  - 如果 Reducer Hook 的返回值与当前 state 相同，React 将跳过子组件的渲染及副作用的执行。Object.is

**useCallback**
- const memoizedCallback = useCallback(fn, arr); 返回一个memoized的回调函数
  - 依赖项数组不会作为参数传给回调函数。所有回调函数中引用的值都应该出现在依赖项数组中
- 把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。
- *当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用*
> useCallback(fn, deps) 相当于 useMemo(() => fn, deps)。
- 可以避免函数组件中，相同变量的箭头函数导致的多次不必要渲染

**useMemo**
> 传入 useMemo 的函数会在渲染期间执行。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 useEffect 的适用范畴，而不是 useMemo
- `const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);`
- 它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算
- 可以把 useMemo 作为性能优化的手段，**但不要把它当成语义上的保证**

两种记忆函数的区别：
  ```js 先看两段代码
  function App() {
    const memoizedHandleClick = useCallback(() => {
      console.log('Click happened')
    }, []); // 空数组代表无论什么情况下该函数都不会发生改变
    return <SomeComponent onClick={memoizedHandleClick}>Click Me</SomeComponent>;
  }

  function App() {
    const memoizedHandleClick = useMemo(() => () => {
      console.log('Click happened')
    }, []); // 空数组代表无论什么情况下该函数都不会发生改变
    return <SomeComponent onClick={memoizedHandleClick}>Click Me</SomeComponent>;
  }
  ``` 
- useCallback 不会执行第一个参数函数，而是将它返回，而 useMemo 会执行第一个函数并且将函数执行结果返回
-  useCallback 常用记忆事件函数，生成记忆后的事件函数并传递给子组件使用。而 useMemo 更适合经过函数计算得到一个确定的值，比如记忆组件
 
**useContext**
- 接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值
  - 当前的 context 值由上层组件中距离当前组件最近的 `<MyContext.Provider>`的 value prop 决定
  - 调用了 useContext 的组件总会在 context 值变化时重新渲染。如果重渲染组件的开销较大，你可以 通过使用 `memoization` 来优化。
  - `useContext(MyContext)` 约等于 类中的`static contextType = MyContext` 或者 `<MyContext.Consumer>`

**useRef**
- `const refContainer = useRef(initialValue);`
- useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）
  - 返回的 ref 对象在组件的整个生命周期内保持不变
- 可以将其挂载到某个类组件上< ref={}>；或者将其当作函数组件的实例变量，保存某个变量(函数声明周期内不变)

**useImperativeHandle**：
- `useImperativeHandle(ref, createHandle, [deps])`
- 可以让你在使用 ref 时自定义暴露给父组件的实例值
- 

**useLayoutEffect**
- 在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。
- 尽可能使用标准的 useEffect 以避免阻塞视觉更新。
- 使用服务端渲染时的有关问题：
  - 无论 useLayoutEffect 还是 useEffect 都无法在 Javascript 代码加载完成之前执行
  - 解决这个问题，需要将代码逻辑移至 useEffect 中（如果首次渲染不需要这段逻辑的情况下），或是将该组件延迟到客户端渲染完成后再显示
  > 若要从服务端渲染的 HTML 中排除依赖布局 effect 的组件，可以通过使用 showChild && `<Child /> `进行条件渲染，并使用 useEffect(() => { setShowChild(true); }, []) 延迟展示组件。这样，在客户端渲染完成之前，UI 就不会像之前那样显示错乱了。

**useDebugValue**:
- useDebugValue 可用于在 React 开发者工具中显示自定义 hook 的标签。

- 延迟格式化 debug 值
  - 格式化值的显示可能是一项开销很大的操作。除非需要检查 Hook，否则没有必要这么做。
  - useDebugValue 接受一个格式化函数作为可选的第二个参数。该函数只有在 Hook 被检查时才会被调用。它接受 debug 值作为参数，并且会返回一个格式化的显示值