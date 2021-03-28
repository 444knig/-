### reconciliation-协调
- O(n) 的启发式算法
  - 两个不同类型的元素会产生出不同的树；
  - 开发者可以通过 key prop 来暗示哪些子元素在不同的渲染下能保持稳定
- Diffing算法：
  - 当对比两颗树时，React 首先比较两棵树的根节点。不同类型的根节点元素会有不同的形态
  - 比对不同类型的元素
     - 当根节点为不同类型的元素时，React 会拆卸原有的树并且建立起新的树 eg:`<img>-><span>`
       - 当拆卸一棵树时，对应的 DOM 节点也会被销毁
         - 组件实例将执行 componentWillUnmount() 方法
       - 当建立一棵新的树时，对应的 DOM 节点会被创建以及插入到 DOM 中
         - 组件实例将执行 componentWillMount() 方法
         - 紧接着 componentDidMount() 方法
       - 所有跟之前的树所关联的 state 也会被销毁。
  - 比对同一类型的元素
    - React 会保留 DOM 节点，仅比对及更新有改变的属性。
      - 只需要修改 DOM 元素上的 className 属性，当更新 style 属性时，React 仅更新有所更变的属性
  - 在处理完当前节点之后，React 继续对子节点进行递归。
  - 比对同类型的组件元素：
    - 当一个组件更新时，组件实例保持不变，React 将更新该组件实例的 props 以跟最新的元素保持一致
    - 调用该实例的 componentWillReceiveProps() 和 componentWillUpdate() 方法，
    - 调用 render() 方法，diff 算法将在之前的结果以及新的结果中进行递归
  - 列表的更新用'key'优化
    - key 应该具有稳定，可预测，以及列表内唯一的特质

#### Vitual Dom
- VD只是一个简单的JS对象，并且最少包含tag、props和children三个属性
  
- 为什么要使用vd：
  - 直接原因：DOM 操作真正的问题在于每次操作都会触发布局的改变、DOM树的修改和渲染
  - VD只是一个双缓冲技术

- JSX：HTML+JS：
  - 浏览器只懂js、html
  ```jsx
  <div className='cn'>
  Content!
  </div>
  ```
  **该代码被转为纯粹的js**
  ```js
  React.createElement(
  'div',
  { className: 'cn' },
  'Content!'
  );
  ```
- 参数：
  - 第一个是元素的type。对于HTML标签，它将是一个带有标签名称的字符串
  - 第二个参数是一个包含所有元素属性（attributes）的对象。如果没有，它也可以是空的对象。
  - **剩下的参数**都可以认为是元素的子元素（children），文本或节点
    - 基本类型 false, null, undefined, true、数组、React Components、fragment
- 当定义一个函数组件时`function Table({ rows }) {return ;}`
  - `<Table rows={rows} />`
- 该代码会被翻译为：`React.createElement(Table, { rows: rows });`
  - Table：引用，指向定义的函数，第二个参数`attributes`：接受props
- ReactDOM库的render方法
  - 当ReactDOM.render被调用时，React.createElement最终也会被调用，返回一个js对象
    - ![](../img/VDOM&#32;object.png)
  - **这些对象，在React的角度上，构成了虚拟DOM**。
*他们将在所有进一步的渲染中相互比较，并最终转化为 真正的DOM（virtual VS real, 虚拟DOM VS 真实DOM）*

- `React.createElement`：除了type和attribute以外的属性，原本是单独传进来的，转换之后，会作为在props.children以一个数组的形式打包存在 —— 在生成的虚拟DOM对象的时候，它们最后都会被打包在一起的。
- 也就是说一下代码是等价的
    ```jsx
    React.createElement(
    'div',
    { className: 'cn' },
    'Content 1!',
    'Content 2!',
    );
    ```
    `<div className='cn' children={['Content 1!', 'Content 2!']} />`
    以上代码都会变成
    ```jsx
    {
    type: 'div',
    props: {
        className: 'cn',
        children: [
        'Content 1!',
        'Content 2!'
        ]
    }
    }
    ```
**以上便是构建一个VDOM节点的大致过程**
之后ReactDOM.render将会按下面的原则，尝试将其转换为浏览器可以识别和展示的DOM节点：
- 如果type包含一个带有String类型的标签名称（tag name）—— 创建一个标签，附带上props下所有attributes。
  - HTML原生元素
- 如果type是一个函数（function）或者类（class），调用它，并对结果递归地重复这个过程。
  - react组件
- 如果props下有children属性 —— 在父节点下，针对每个child重复以上过程。
- 最后得到HTML

#### 重新构建DOM（Rebuilding the DOM）
- 在实际应用场景，render通常在根节点调用一次，后续的更新会有state来控制和触发调用。
  - 当我们想更新一个页面而不是全部替换时，Rebuild就会生效
- 更新阶段，React将启动其`diff`算法，去那些决定需要更新的节点
  - 场景1：type是一个字符串，type在通话中保持不变，props也没有改变。
    - DOM保持不变
  - 场景2：type仍然是相同的字符串，props是不同的。
    - 调用标准DOM API来更改元素的属性（而不是删除节点重建）、
  - 场景3：type已更改为不同的String或从String组件。
    - 会直接将它以及其所有子节点替换，不会采取更新的方法
    - **React使用Object.is()来比较type的值**
  - 场景4：type是一个component。
    - React会持续地去检查组件的内部逻辑，以确保render返回的值不会改变（类似对副作用的预防措施）。对树中的每个组件进行遍历和扫描，if type is reference and we started reconciliation process
  - 当子节点是个数组，用key去优化
- 当state发生了改变：
  - 调用this.setState会导致re-render（重新渲染）
  - 只会影响组件本身及其children组件。父母和兄弟姐妹都不会受到影响
- 优化我们的代码：Mounting / Unmounting
  - 利用短路求值，将false保留在props.children中，避免大规模的unmount+mount相同的子组件(数组)
    - 不用keys的原因：只是react将这些component当作了array，它们只是组合起来的
  - HOC：在render函数外使用HOC，避免连等运算失败
  - pure component适用于复杂的表单和表格。不适用于简单元素（按钮、图标）
    - 有时候 swallowCompare比diff操作更昂贵

#### diff算法：
- diff算法的前提：
  - Web UI 中 DOM 节点跨层级的移动操作特别少，可以忽略不计
  - 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构。
  - 对于同一层级的一组子节点，它们可以通过唯一 id 进行区分
   
- tree diff
  - 两棵树只会对同一层次的节点进行比较。(基于策略一)
    - React 通过 updateDepth 对 Virtual DOM 树进行层级控制，只会对相同颜色方框内(同一层内)的 DOM 节点进行比较
    - 当发现节点已经不存在，则该节点及其子节点会被完全删除掉
    - **React 官方建议不要进行 DOM 节点跨层级的操作。**
     >注意：在开发组件时，保持稳定的 DOM 结构会有助于性能的提升。例如，可以通过 CSS 隐藏或显示节点，而不是真的移除或添加 DOM 节点。

- component diff
  - 如果是同一类型的组件，按照原策略继续比较 virtual DOM tree。
  - 如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点。
  - 对于同一类型的组件，有可能其 Virtual DOM 没有任何变化，如果能够确切的知道这点那可以节省大量的 diff 运算时间
   
- element diff
  - 当节点处于同一层级时，React diff 提供了三种节点操作
  - 老component：上一次DOM的snapshot，新...
    - INSERT_MARKUP（插入）
      - 新的 component 类型不在老集合里，是新的节点，需要对新节点执行插入操作。
    - MOVE_EXISTING（移动）
      - 在老集合有新 component 类型，且 element 是可更新的类型，generateComponentChildren 已调用 receiveComponent，这种情况下 prevChild=nextChild，就需要做移动操作，可以复用以前的 DOM 节点。
    - REMOVE_NODE（删除）
      - 老 component 类型，在新集合里也有，但对应的 element 不同则不能直接复用和更新，需要执行删除操作；或者老 component 不在新集合里的，也需要执行删除操作。

  - 配合key的element diff算法：
    - 算法过程：
      - 通过唯一 key 可以判断新老集合中是否存在相同的节点
      - 如果存在相同节点，则进行移动操作判断：
        - lastIndex 一直在更新，表示访问过的节点在老集合中最大的位置：相当于，一个标记，标记前的元素是有序||将要有序的(Math.max()操作之后)
        - 如果新集合中当前访问的节点比 lastIndex 大
      - `<待考证>`：由于lastIndex的排序策略：在开发过程中，尽量减少类似将最后一个节点移动到列表首部的操作，当节点数量过大或更新操作过于频繁时，在一定程度上会影响 React 的渲染性能。同时可以得到：将第一个节点移到最后一个位置，只需要一次移动操作






