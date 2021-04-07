**入口起点**
entry points

Tree shaking
原理：DEC

dead code elimination: 特征  uglify做的
- 代码不会被执行，不可到达
- 执行的结果不会被用到
- 代码只会影响死变量

ES6的特点：
- 只能作为模块顶层的语句出现
- import的模块名只能是字符串常量
- import 的 binding 是 immutable 的

只有ES6的模块依赖才能支持tree shaking

一般来说只处理function & variable
静态分析无法消除类函数

Tree-shaking的实践
组件库的优化

PostCss：解析css->AST抽象语法树
这样就可以删除没用到的css选择器

副作用：一个函数会、或者可能会对函数外部变量产生影响的行为。

webpack不支持导出ESM，所以组件库才需要使用babel插件来转换import语句