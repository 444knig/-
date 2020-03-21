### 组件
-  组件名称必须以大写字母开头，小写的是原生的DOM
-  强烈建议你不要创建自己的组件基类。组合>继承

**挂载**：斜体用得较少
   - constructor()
   - *static getDerivedStateFromProps()*
   - render()
   - componentDidMount()

**更新**：
- *static getDerivedStateFromProps()*
- *shouldComponentUpdate()*
- render()
- *getSnapshotBeforeUpdate()*
- componentDidUpdate()
  
- render：
  -  class 组件中唯一必须实现的方法。
  -  被调用时，会检查this.props 和 this.state 的变化并返回以下类型之一：
     -  React 元素： DOM 节点`<div/>` 或 自定义组件`<MyComponent />`
     -  数组或 fragments
  - 为纯函数，意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互

**卸载**：当组件从 DOM 中移除时会调用如下方法：
  - componentDidUpdate()

**错误处理**：当渲染过程，生命周期，或子组件的构造函数中抛出错误
  - *static getDerivedStateFromError()*
  - *componentDidCatch()*

*其他api*：
  - setState()
  - forceUpdate()


**fragment**：
  - 一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点，以前是额外加div
    - 15之前，每个render返回的元素都要有一个根节点，现在可以用fragment包起来
  - key 唯一属性

**Portals**：
- Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点
- `ReactDOM.createPortal(child, container)`，第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 fragment。第二个参数（container）是一个 DOM 元素。