* HTML的 onclick函数的缺点：
    * 添加的事件处理函数是在全局环境下执行的，这污染了全局环境，很容易产生意料不到的后果：  
      * 给很多DOM元素添加onclick事件，可能会影响网页的性能，毕竟，网页需要的事件处理函数越多，性能就会越低
      * ·对于使用onclick的DOM元素，如果要动态地从DOM树中删掉的话，需要把对应的时间处理器注销，假如忘了注销，就可能造成内存泄露，这样的 bug很难被发现
* bable：把ES6语法的JavaScript代码转译成 浏览器普遍支持的JavaScript代码
* `React`:应用就是通过重复渲染来实现用户交互
    1. 利用Virtual DOM，让每次渲染都只重新渲染最少的DOM元素。
    2. React首先构建起Virtual DOM
    3. 每次自上而下渲染React组件时，会对比这一次产生的`Virtual DOM`和上一次渲染的`Virtual DOM`，对比就会发现差别，然后修改真正的DOM树时就只需要触及差别中的部分就行
     > `DOM`:是结构化文本的抽象表达 形式，特定于Web环境中，这个结构化文本就是HTML文本，HTML中的每 个元素都对应DOM中某个节点，这样，因为HTML元素的逐级包含关系， DOM节点自然就构成了一个树形结构，称为DOM树。

* 浏览器渲染HTML格式：将HTML文本解析以构建DOM树，然后根据DOM树渲染出用户看到的界面，当要改变界面内容的时候，就去改变DOM树上的节点。
* Web前端开发关于性能优化有一个原则：尽量减少DOM操作
* 高内聚 `High Cohesion` :指的是把逻辑紧密相关的内容放在一个组件中
* React组件的使用原则：prop是组件的对外接口，state是组件的内部状态，对外用prop，内部用state；
  * `prop` *property* 是从外部传递给组件的数据，一个 React组件通过定义自己能够接受的prop就定义了自己的对外公共接口
  > `<SampleButton  id="sample" borderWidth={2} onClick=>{onButtonClick}  style={{color: "red"}} />`  
  > borderWidth就是数字类型，onClick是函数类型，style的值是一个包含color字段的对象，当prop的类型不是字符串类 型时，在JSX中必须用花括号{}把prop值包住，所以style的值有两层花括 号，外层花括号代表是JSX的语法，内层的花括号代表这是一个对象常量  
  `函数类型的prop等于让父组件交给了子 组件一个回调函数，子组件在恰当的实际调用函数类型的prop，可以带上必要的参数，这样就可以反过来把信息传递给外部世界`
* `propTypr`: 在ES6方法定义的组件类中，可以通过增加类的propTypes属性来定义 prop规格，这不只是声明，而且是一种限制，在运行时和静态代码检查时， 都可以根据propTypes判断外部世界是否正确地使用了组件的属性。  
    *propTypes检查只是一个辅助开发的功能，比较适合放在开发阶段*
* state: 野蛮的修改state不会进行渲染，而setState首先进行state的修改，而后进行驱动组件进行个性内容
* `S&P` 总结：组件不应该改变prop的值，而state存在的目的就是让组件来改变的。
***
## 组件的生命周期
* 装载过程（Mount），也就是把组件第一次在DOM树中渲染的过程；
* 更新过程（Update），当组件被重新渲染的过程
* 卸载过程（Unmount），组件从DOM中删除的过程
 #### *当组件第一次被渲染的时候，依次调用的函数顺序*
 constructor->getInitialState->getDefaultProps->  
 componentWillMount->render->componentDidMount
  ### 装载过程
 * `constructor`: 为了如下目的，需要构造函数：  
    * 初始化state，因为组件生命周期中任何函数都可能要访问state，那么整个生命周期中第一个被调用的构造函数自然是初始化state最理想的地方
    * 绑定成员函数的this环境 *在ES6语法下，类的每个成员函数在执行时的this并不是和类实例自动 绑定的*
* getInitialState&&getDefaultProps 被抛弃的函数
* `render` : 父类React.Component类对除render之外的生命周期函数都有默认实现。  
    *render函数并不做实际的渲染动作，它只是返回一个JSX描述的结构，最终由React来操作渲染过程。*
    * 当组件的作用不是渲染界面时，返回false or null
    * 是一个纯函数
* `componentWillMount` & `componentDidMount`:
  * WiilMount执行时没有任何渲染的结果，因此所有..都可提前到constructor去做
  * `componentDidMount` 不会在render返回时立即被调用时，而是当render返回的东西已经引发了渲染，组件已经被“装载”到了DOM树上，才会调用didMount函数作为收尾
  * componentDidMount只在浏览器端执行
### 更新过程
* 调用顺序：componentWillReceiveProps->shouldComponentUpdate->
  componentWillUpdate->render->componentDidUpdate
  * `componentWillReceiveProps`: 
    1. 只要是父组件的render函数被调用，在render函数里面被 渲染的子组件就会经历更新过程，不管父组件传给子组件的props有没有改变，都会触发子组件的componentWill-ReceiveProps函数。
    *这个函数适合根据新的props值（也就是参数nextProps）来计算出是不是 要更新内部状态state，因此不会被setState调用*  
    2. 这个函数会把nextProp，和上一个prop作比较，只有变化时才会调用setState更新内部过程
  * `shouldComponentUpdate` 决定了一个组件什么时候不需要渲染  
      1. 要求有返回结果，布尔值，这个组件在这次更新过程中是否要继续。
      2. this.setState函数引发更新过程，并不是立刻更新组件的state值，在执行到shouldUpdate之前还是久值所以我们要做的实际上就是在nextProps、nextState、this.props和this.state中互相比对。
      3. **`shouldComponentUpdate` is invoked before rendering when new props or state are being received.**
  * `componentWillUpdate`和`componentDidUpdate`: 当shouldUpdate返回true时执行
### 卸载过程
 * `componentWillUnmount`：在componentDidMount中用非React的方法创造了一些DOM元素，如果 撒手不管可能会造成内存泄露，那就需要在componentWillUnmount中把这些 创造的DOM元素清理掉
### 组件向外传递数据: 子向父传
  * 将一个函数用prop传给子类
  * 子类将要更新的数据传入函数中
  * 父类绑定这个函数并调用
*缺陷：当子父组件出现状态不一致时，将会导致bug*  
*解决思路*：将数据源从组件中抽离，实现全局数据源
### 数据处理
* Flux
  * Dispatcher，处理动作分发，维持Store之间的依赖关系  
  * Store，负责存储数据和处理数据相关逻辑
  * Action，驱动Dispatcher的JavaScript对象
  * View，视图部分，负责显示用户界面
* 操作步骤和理解  
  `import {Dispatcher} from 'flux';`  
  `export default new Dispatcher();`
    1. `Dispatcher`:
       1. 创造一个dispatcher对象，几乎所有应用都只需要拥有一个  
       2. Dispatcher,Dispatcher存在的作用，就是用来派发action.  
    2. `Action`:  
    `export const INCREMENT = 'increment';`  
    `export const DECREMENT = 'decrement'`  
       1.  JavaScript对象，代表一个动作的纯数据，action对象必须有一个名为type的字段，代表这个action对象的类型.
       2.  为了记录日志和debug方便，这个type应该是字符串类型。
       3.  定义Action的构造函数  
      `export const increment = (counterCaption) => { `  
      `  AppDispatcher.dispatch({ `
      ` type: ActionTypes.INCREMENT,`  
      ` counterCaption: counterCaption`  
      ` }); };`
      4. 将创建好的action对象，使用Dispatcher.dispatch分发出去

    3. store ：
       1. 把store注册到Dispatcher对象上去  
       2. *当通过register函数把一个回调函数注册到Dispatcher之后， 所有派发给Dispatcher的action对象，都会传递到这个回调函数中来*
       3. 根据不同的type，会有不同的操作：函数体是一串if-else条件语句或者switch条件语句，而条件语句的跳转条件，都是针对参数action对象的type字段 
       4. waitfor：接受一个数组作为参数，数组中每个元素都是一个Dispatc-herregister函数的返回结果，也就所谓的dispatchToken。waitFor函数告诉Dispatcher，当前的处理必须要暂停，直到dispatchToken代表的那些已注册回调函数执行结束才能继续
       5. *Flux按照register调用的顺序去调用各个回调函数，我们也完全无法把握各个Store哪个先装载从而调用register函数*  是无法预期的
    4. 存在于Flux框架中的React组件需要实现以下几个功能：
       1.  创建时要读取Store上状态来初始化组件内部状态；
       2.  当Store上状态发生变化时，组件要立刻同步更新内部状态保持一致
       3.  View如果要改变Store状态，必须而且只能派发action
       4.  用didMount添加Store的监听函数，只要store的值变化，组件的onchange函数就会被执行
       5.  组件中会有两处用到store中函数的地方：
           1.  构造函数中初始化this.state的时候
           2.  第二处是在响应CounterStore状态变化的onChange函数中(setState)
           3.  无状态组件不需要
* Flux的优势：
    1. 在Flux中，Store只有get方法，没有set 方法，根本可能直接去修改其内部状态，View只能通过get方法获取Store的 状态，无法直接去修改状态，如果View想要修改Store状态的话，只有派发 一个action对象给Dispatcher
* Flux的不足：
    1. Store之间依赖关系，存在复杂的依赖关系，不好处理
    2. 难以进行服务器端渲染
    3. Store混杂了逻辑和状态：Store封装了数据和处理数据的逻辑，当需要修改逻辑时，就无法保持store中储存的状态

## Redux
* 三个原则：
    1. 唯一数据源：
       * *应用的状态数据应该只存储在唯一的一个Store上。*
       * store上的状态是一个树形的对象
    2. 保持状态只读  
       *  要修改Store的状态，必 须要通过派发一个action对象完成
    3. 数据改变只能通过纯函数完成
       * Reducer的函数签名：reducer(state, action)，*arg1 当前的状态， *arg2 接收到的action对象
       * 纯函数的意义：函数的返回结果必须完全由参 数state和action决定，而且不产生任何副作用，也不能修改参数state和action对象
       * ，Redux的reducer只负责计算状态，却并不负责存储状态
       * 
* Redux中每个action构造函数 都返回一个action对象。
* React中的Redux基本要完成以下功能：
  * 和Redux Store打交道，读取Store的状态，用于初始化组件的状态，同时还要监听Store的状态改变；当Store状态发生变化时，需要更新组件状态，从而驱动组件重新渲染；当需要更新Store状态时，就要派发action对象
  * 根据当前props和state，渲染出用户界面
* 容器组件和展示组件 
  * 容器：负责和Redux Store打交道的组件， 处于外层
  * 展示：只专心负责渲染界面的组件，处于内层
  * 结果：状态由容器组件去管理，展示组件根据props去渲染
  * 完全没有state，只有一个render，所有数据来源于props，无状态组件
  * 将展示组件放入容器中，export一个容器即可
* react-redux的两个最主要功能：
  * connect：连接容器组件和傻瓜组件
  * Provider：提供包含store的context  

  * `connect`：connect 是`react-redux`提供的一个方法，这个方法接收两个参数`mapStateToProps`和`mapDispatch-ToProps`，执行结果依然是一个函数
    * 这里有两次函数执行，第一次是connect函数的执行，第二次是把connect函数返回的函数再次执行，最后产生的就是**容器组件**，功能相当于前面 redux_smart_dumb中的CounterContaine  

    * 该函数所作的行为有：
      * 把Store上的状态转化为内层傻瓜组件的props  `mapStateToProps`
      * 把内层傻瓜组件中的用户动作转化为派送给Store的动作
      * *内层傻瓜对象的输入，一个是内层傻瓜对象的输出*
  * `mapStateToProps`和`mapDispatch-ToProps`的参数：
    * `mapStateToProps`: 
      * 把Store上的状态转化为内层组件的 props，以对象的形式返回，`return {props:state}`
      * 接受的参数：`state`和ownProp
      * 如果不传，组件不会监听store的变化
      * **ownProps**：代表组件本身的props，如果写了第二个参数ownProps，那么当prop发生变化的时候，mapStateToProps也会被调用。例如，当 props接收到来自父组件一个小小的改动，那么你所使用的 ownProps 参数，mapStateToProps 都会被重新计算
    * `mapDispatch-ToProps`：
      * 把内层傻瓜组件暴露出来的函数类型的prop关联上dispatch函数的调用，每个prop代表的回调函数的主要区别就是dispatch函数的参数不同，可以理解为，当prop代表的回调函数发生时，调用dispatch产生一个action对象
      * 用于建立组件跟store.dispatch的映射关系,可以是一个object，也可以传入函数
  * `Provider`:
    * subscribe
    * dispatch
    * getState
    * 拥有上述三个函数的对象，才能称之为一个Redux的store
    * *react-redux定义了Provider的componentWillReceiveProps函数，在 React组件的生命周期中，componentWillReceiveProps函数在每次重新渲染时 都会调用到，react-redux在componentWillReceiveProps函数中会检查这一次 渲染时代表store的prop和上一次的是否一样。如果不一样，就会给出警告，这样做是为了避免多次渲染用了不同的Redux Store*
***
* 开始一个新应用要考虑的事：
  1. 代码文件的组织结构
  2. 确定模块的边界
  3. Store的状态树设计
   
   * 代码目录reducers、actions、components和containers
     * reducer目录包含所有Redux的reducer
     * actions目录包含所有action构造函数
     * components目录包含所有的傻瓜组件
     * containers目录包含所有的容器组件
   * 以功能为模块分类：
     * 将功能模块分别用各自的React组件、reducer和action定义
     * 使用index将模块的actions, reducer, view导出
   * 状态树的设计
     * 一个状态节点只属于一个模块：
       * 每个模块都有机会导出一个自己的reducer，这个导出的reducer只能最多更改 Redux的状态树上一个节点下的数据
       * reducer之间对状态树上的修改权 是互斥的，不可能让两个reducer都可以修改同一个状态树上的节点。*只能更新自己相关那一部分模块的数据*
     * 避免冗余数据
       * 使使用“范式化”的无冗余数据结构，我 们借助reselector等工具一样可以获得很高的性能
     * 树形结构扁平
       * 一个很复杂的状态树 是难以管理的
     * `combineReducers`
       * 函数`combineReducers`来把多个reducer函数 合成为一个reducer函数
       * combineReducers函数接受一个对象作为参数，**参数对象的每个字段名**->**对应了State状态上的字段名**，字段值是一个reducer函数，每个字段的值都是一个reducer函数，state的状态上的字段值就是从这里产生，在之后组件的map...函数中xx.调用
       * *combineReducers函数返回一个新的reducer函数，当这个新的reducer函数被执行时，会把传入的state参数对象拆开处理， todo字段下的子状态交给todoReducer，filter字段下的子状态交给filterReducer，然后再把这两个调用的返回结果合并成一个新的state，作为整体reducer函数的返回结果*
    * 总结一下Redux的组合reducer功能，利用combineReducers可以把 多个只针对局部状态的“小的”reducer合并为一个操纵整个状态树的“大的”reducer，更妙的是，没有两个“小的”reducer会发生冲突，因为无论怎么 组合，状态树上一个子状态都只会被一个reducer处理，Redux就是用这种方法隔绝了各个模块。因此*无论我们有多少“小的”reducer，也无论如何组合，都不用在乎它们被因为调用的顺序，因为调用顺序和结果没有关系。*
    * Ref函数的使用：
      * 包含ref属性的组件完成装载（mount）过程的时候，会看一看ref属性是不是一个函数，如果是，就会调用这个函数，参数就是这个组件代表的DOM元素，这样就可以访问到实际的DOM元素
      * ref实际上就是直接触及了DOM元素，与我们想远离DOM是非之地 的想法相悖
      * 要谨慎使用，如果要 用，我们也尽量让ref不要跨越组件的边界
    * `Form`的使用：在HTML中，一个form表单被提交的默认行为会引发网页跳转，在React 应用中当然不希望网页跳转发生，所以我们首先通过调用参数ev的 preventDefault函数取消掉浏览器的默认提交行为
    * `JSX`和`JavaScript`：JSX中可以使用任何形式的JavaScript表达，但也只是表达式，而while和for是语句，在JSX中不能使用

***
## React组件的性能优化
* 单个React组件的性能优化
* 多个React组件的性能优化
* 利用reselect提高数据选取的性能
  1. 单个组件的优化：
       * connect过程产生了一个无名的React组件，这个组件定制了shouldComponentUpdate函数的实现，实现逻辑 是比对这次传递给内层傻瓜组件的props和上一次的props，因为负责“组件看 起来怎样”的傻瓜视图是一个无状态组件，它的渲染结果完全由传入的props 决定，如果props没有变化，那就可以认为渲染结果肯定一样  
       * 匿名对象如果在赋值时产生，那就意味着每渲染一次就会产生一个新的对象

  2. 单个组件的优化实例：
     1. 由于创建匿名函数会导致引用对象浅比较的shouldComponentUpdate直接返回false，因此容器组件中将不再为每一个item创建匿名函数，而实直接将函数传进每一个item，并让item实现mapDispatchToProps，让item自己发出，也就是说，将本在容器组件中发出的action，改为将函数传给子组件，然后推迟到props发生改变时，在调用props中的onToggle函数，*函数`onTogle`的实现为dispatcher一个action*
     2. 第二种优化，不将onToggle函数传给子组件，让子组件自个去调用dispatcher函数派发一个对象
  3. 多个React组件的性能优化：
     1. 考虑三个阶段：装载阶段、更新阶段和卸载阶段
        1. 对于装载，DOM树是无论如何都需要渲染一次，因此无法优化；卸载亦如此
     2. 前序知识：
        1. `Reconciliation`：找不同的过程
        2. 算法处理：
           1. 根节点不同的情况：
              1. 当DOM的节点树的根节点被换掉时，整棵子树会被unmount，然后Will、render、Did。
              2. 即使是将Div改为span，也会换掉整颗子树
           2. 根节点类型相同的情况，分DOM节点，HTML节点(div,span)，React节点：
              1. 对于DOM元素类型，React会保留节点对应的DOM元素，只对树形结构根节点上的属性和内容做一下比对，然后只更新修改的部分。
              2. 对于React节点根据新节点的props去更新原来根节点的组件实例，引发这个组件实例的更新过程，然后引发一下元素`shouldComponentUpdate`->`componentWillReceiveProps`->`componentWillUpdate`->`render`->`componentDidUpdate`；如果shouldupdate返回false，那更新过程就此打住
           3. 多个子组件的情况：
              1. React组件的key类似于index，唯一
              2. key和ref是React保留的两个特殊prop，没有预期让组件直接访问。    
  * 用reselect提高数据获取性能
    1. mapStateToProps函数一定要快
    2. select库原理：
       1. 被用来创造“选择器”，所谓选择器，就是接受一个state作为参 数的函数，这个选择器函数返回的数据就是我们某个mapStateToProps需要的结果。
       2. select库认为选择器工作有两个步骤
          1. 从输入参数state抽取第一层结果，将这第一层结果和之前抽取的第一层结果做比较，如果发现完全相同，就没有必要进行第二部分运算了，选择器直接把之前第二部分的运算结果返回就可以了，对象==
          2. 根据第一层结果计算出选择器需要返回的最终结果。
    3. `createSelector`：一个高阶函数，也就是接受函数为参数来产生一个新函数的函数
       1. 参数一：一个函数数组，每个元素代表了选择器步骤一需要做的映射计算。  
        * `const getFilter = (state) => state.filter;`
       2.  参数二：代表着步骤2的计算过程
    4. 不是纯函数，但是表现得像纯函数。有“记忆”这个副作用 
 * 范式化状态树  
本章小结：
  * 提供的shouldComponentUpdate实 现来提高组件渲染功能的方法，一个要诀就是避免传递给其他组件的prop值是一个不同的对象，不然会造成无谓的重复渲染
  * React能够在 O（N）时间复杂度下完成Virutal DOM的比较
  * 不能随意修改一个作为容器的HTML节点的类型
  * 对于动态数量的同类型子组件，一定要使用key这个prop。
  * 学习了利用reselect库来实现高效的数据获取。因为reselect 的缓存功能，开发者不用顾忌范式化的状态树会存在性能问题，Redux Store的状态树应该按照范式化原则来设计，减少数据冗余，这样利于保持数据一致。
  * ***
## 第六章：React高级组件
* 探讨如何构建更易于复用、更灵活的React高级组件
  1. 高阶组件的概念及应用
  2. 以函数为子组件的模式
* 高阶组件(Higher Order Component)
  * 一个高阶组件就是一个函数，这个函数接受一个组件作为输入，然后返回一个新的组件作为结果.
  * 严谨的说法：函数返回的结果才应该叫“高阶组件”， 而这个函数本身应该叫做“高阶组件工厂函数”
  * 高阶组件的两类实现方式：
    * 代理方式的高阶组件
      1. 特点是返回 的新组件类直接继承自React.Component类
      2. 可应用场景
         1. 操纵prop：render函数相当于一个代理，完全决定如何使用被包裹的组件
         2. 抽取状态
            1. connect函数的执行结果是另一个函数-高阶组件
               1. 传递给connect函数的mapStateToProps和mapDispatchToProps两个参数在 render函数中被使用，根据执行store.getState函数来得到Redux Store的状态， 通过store.dispatch可以得到传递给mapDispatchToProps的dispatch方法
               2. 简易实现在p271
         3. 包装组件
            1. 类似于装饰者模式
    * 继承方式的高阶组件
      * 代理方式下，render函数中的使用被包裹组件。组件经理了一个完整的生命周期  `return <WrappedComponent {...otherProps} />`
      * 在继承方式下，`return super.render()`，此时的生命周期是一个函数而已
      * 总结：在代理方式下产生的新组件和参数组件是两个不同的组件，一次渲染，两个组件都要经历各自的生命周期，在继承方式下两者合二为一，只有一个生命周期
      * 可用于场景：
        * 操纵Props--过于繁琐
        * 操纵生命周期函数
          * 可以重新定义任何一个React组件的生命周期函数
          * 重新定义render，根据prop
          * 重新定义shouldComponentUpdate函数
      * 优先考虑组合，然后才考虑继承。
  * 以函数为子组件
    * 高阶组件的缺点：
    * 如果组件X并不支持这些props， 或者对这些props的命名有不同，或者使用方式不是预期的方式，那也就没有办法应用这个高阶组件 
    * 优点：利用这种模式会很灵活，因为预期子组件是一个函数，而函数使得一切皆有可能  
    * 缺点：
      * 每次外层组件的更新过程，都要执行一个函数获得子组件的实际渲染。
      * 当子组件使用lambda表达式时，就意味着每次渲染都要创建于一个函数对象，此时shouldupdate函数就容易失效（影响不大）。**代码灵活性和性能无法兼得**  
  
**代码块解读**：
 ```js
 componentDidMount() {    
    this.intervalHandle = setInterval(() => {    const newCount = this.state.count - 1;    
    if (newCount >= 0) {      
      this.setState({count: newCount});    } 
    else {      
      window.clearInterval(this.intervalHandle);    }  
    }, 1000); 
  }
```
为什么在setInterval的第一个函数参数中能使用this去访问state?
答：因为我们setInterval第一个参数是ES6的箭头函数形式，箭头 函数会自动将自身的this绑定为所处环境的this，因为这个箭头函数所处环境 是componentDidMount，所以this自然就是组件实例本身

* 本章小结：
1. React高级组件
  * 高阶组件
    1. 代理方式 容易实现和控制
    2. 继承方式 唯一优势是可以操纵特定组件的生命周期函数
  * 以函数为组件 灵活，连接两个组件的方式可以非常自由

## 第七章：Redux和服务器通信
* React组件访问服务器的方式  
  * js是单线程语言，要通过异步请求获得数据
  * 因为React组件的装载过程和更新过程中生命周期函数是同步执行的，没有任何机会等待一个异步操作
  * 因此需要分两步，显示正在装载，获取数据后，显示数据
  * 这个过程至少需要经历装在过程和更新过程，至少渲染两次
  * 通常在DidMount中发出请求，因为对服务器的请求可能依赖 于已经渲染的内容
  * DidMount代码：
```js
componentDidMount() {
   const apiUrl = `/data/cityinfo/${cityCode}.html`;  
   fetch(apiUrl).then((response) => { 
        if (response.status !== 200) { 
            throw new Error('Fail to get response with status ' + response.stat    
            } 
            response.json().then(
              (responseJson) => {
                  this.setState(
                    {weather: responseJsonweatherinfo});
                  }).catch(
                    (error) => {      
                      this.setState({weather: null}); });
                        }).catch((error) => {      
                          this.setState({weather: null};
                          }); 
                    }
```
* 这段代码看起来相当复杂，解析如下：
  1. ，那就是fetch认为只要服务器返回一个合法的HTTP响 应就算成功，就会调用then提供的回调函数。因此我们需要判断网络状态码。
  2. 当response.status为200时，也不能直接读取response中的内容，因为fetch 在接收到HTTP响应的报头部分就会调用then，不会等到整个HTTP响应完成。
  3. 所以，response.body函数执行并不是返回JSON内容，而是返回一个新的Promise，这时候又要then和catch
* 用Redux来访问服务器
  * 前序知识：redux-thunk中间件：
    * redux-thunk的想法，在Redux的单向数据流中，在action对象被 reducer函数处理之前，是插入异步功能的时机
    * 一个action对象在通过store.dispatch派发，在调用reducer函数之前，会先经过一个中间件(Redux-thunk)的环节，这就是产生异步操作的机会，
  * 异步action对象
    * 一个函数
  * redux-thunk的工作：
    * 不是函数则放行，若是函数，则执行，并把Store的dispatch函数和getState函数作为参数传递到函数中，不会让该函数传到reducer中
    * 以下函数能够法出一个"让Counter组件在1秒 之后计数加一"的action，该函数被dispatch派发后，会被redux-thunk中间件执行，于是setTimeout函数就会发生作用，于是在一秒之后，利用参数dispatch函数派发出同步action构造函数increment的结果。
```js
const incrementAsync = () => { 
   return (dispatch) => {
      setTimeout(() => { 
        dispatch(increment());
      }, 1000);
  }; };
```
* redux访问服务器总结：
  * action对象函数中完全可以通过fetch发起一个对服务器的异步请求，当 得到服务器结果之后，通过参数dispatch，把成功或者失败的结果当做action 对象再派发出去。这一次派发的是普通的action对象，就不会被redux-thunk截获，而是直接被派发到reducer，最终驱动Store上状态的改变。

* 异步操作的模式
  * 一个访问服务器的action，需要三个类型。
  * 表示异步操作已经开始的action类型，在这个例子里，表示一个请求天 气信息的API请求已经发送给服务器的状态
    * 异步操作正在进行中  普通action对象
  * 表示异步操作成功的action类型，请求天气信息的API调用获得了正确 结果，就会引发这种类型的action
    * 异步操作已经成功完成  异步action对象
  * 表示异步操作失败的action类型，请求天气信息的API调用任何一个环节出了错误，无论是网络错误、本地代理服务器错误或者是远程服务器返回 的结果错误，都会引发这个类型的action
    * 异步操作已经失败。
  * fetch引发的异步操作完成之前，Redux正常工作，不会停留在fetch函数执行上，如果有其他任何action对象被派发，Redux照常处理
异步模式的代码
```js
export const sampleAsyncAction = () => {
    return (dispatch, getState) => {
          //在这个函数里可以调用异步函数，自行决定在合适的时机通过dispatch参数    
          //派发出新的action对象。  
          }
       }
```
* 中断api请求的实现方式：
  * 使用一个全局变量来确认这个action是当前的action
  * 忽略请求返回的结果，来达到了中止一个API请求一样的效果。
* 研究让 Redux支持异步操作的库时需要考虑哪些方面。

* 单元测试：
  * 

















