$$context$$

>Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。

### 使用 Context 之前的考虑:
> Context 主要应用场景在于很多不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。
- 如果只是想避免层层传递属性，可以试试组件组合，也就是说，将需要这个属性的组件，当作props直接传下去
  - 这种将逻辑提升到组件树的更高层次来处理，会使得这些高层组件变得更复杂，并且会强行将低层组件适应这样的形式，这可能不会是你想要的。
- 或者，当

### context使用场景：
> Context 能让你将这些数据向组件树下所有的组件进行“广播”，所有的组件都能访问到这些数据，也能访问到后续的数据更新。
- 使用 context 的通用的场景包括管理当前的 locale，theme，或者一些缓存数据

### api
**React.createContext**：
- 创建一个 Context 对象
-  React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值
- 只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效
- 如果传入的是一个对象，那么当更改context，消费者使用Object.is去对比

**Context.Provider**
- 每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化
- Provider是需要使用Context的所有组件的根组件。
- Provider 接收**一个**value 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
- 它表示Context传递的值，它会修改你在创建Context时候设定的默认值。
- 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。
  - **Provider 及其内部 consumer 组件都不受制于 shouldComponentUpdate 函数**
- 通过新旧值检测来确定变化

**Class.contextType**
- 挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象
  - static声明
- 这能让你使用 this.context 来消费最近 Context 上的那个值。可在所有生命周期访问

**Context.Consumer**
  ```jsx
  <MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
  </MyContext.Consumer>
  ```
- 当 Consumer 被放在一个没有 Provider 包裹的树上时，会收到默认值
- 可以将consumer当作一个HOC，不必每次都为需要上下文的组件提供consumer

**Context.displayName**
- context 对象接受一个名为 displayName 的 property，类型为字符串
  - react DevTools 使用该字符串来确定 context 要显示的内容。
- **Consumer 使用 Render Props 模式**
  - Consumer 期望子组件是一个函数。然后它在渲染的时候调用这个函数，将它从包裹它的 Provider 上获得的值（或上下文的默认值，如果你没有传入默认值，那也可能是 undefined）传给子组件
***
- 注意事项：
- 每个消耗上下文的组件都会在上下文状态改变时重新渲染。
### 实例
- 


