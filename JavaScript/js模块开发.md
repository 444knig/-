### CommonJS:
- NodeJS是CommonJS规范的实现
- commonJS用同步的方式加载模块
- 服务端会比较快
用法：
  - 模块对外输出变量：module.exports = variable;
CommonJS的模块实现原理
  - `module.exports`
### AMD:
- Asynchronous Module Definition：提前声明好
- 采用异步方式加载模块，模块的加载不影响它后面语句的运行
- 用require.config()指定引用路径等，用define()定义模块，用require()加载模块
  - 引用模块的时候，我们将模块名放在[]中作为reqiure()的第一参数
  - 如果我们定义的模块本身也依赖其他模块,那就需要将它们放在[]中作为define()的第一参数。
### CMD
- 另一种js模块化方案
- 依赖就近、延迟执行
  - 需要的时候再声明
  
- CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

#### CommonJS 模块输出的是值的拷贝:
- 看例子：
- ```js
  // lib.js
  var counter = 3;
  function incCounter() {
    counter++;
  }
  module.exports = {
    counter: counter,
    incCounter: incCounter,
  };
  ```
  ```js 输出结果：
  // main.js
  var mod = require('./lib');

  console.log(mod.counter);  // 3
  mod.incCounter();
  console.log(mod.counter); // 3
  ```
  lib.js模块加载以后，它的内部变化就影响不到输出的mod.counter了。这是因为mod.counter是一个原始类型的值，会被缓存。除非写成一个函数，才能得到内部变动后的值。利用闭包保持值的更改

**ES6**
  - JS 引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值 *unix符号链接*
  - **原始值变了，import加载的值也会跟着变**
  - 只读，可以添加属性，不能赋值
  - ES6 模块的循环加载 ：通过函数变量提升的方法实现。正在加载的函数被认为是已加载的模块
  

### 模块：
- 一个模块（module）就是一个文件。一个脚本就是一个模块。`<script type="module"> `
- 相互加载，使用特殊的指令 export 和 import 来交换功能
  - export 关键字标记了可以从当前模块外部访问的变量和函数。
  - import 关键字允许从其他模块导入功能
- 模块核心功能:对服务、客户端都有效
  - 模块始终默认使用 use strict
  - 一个模块中的顶级作用域变量和函数在其他脚本中是不可见的。只有export的才可见，这些代码只是用于初始化，和内部数据结构的创建
- 代码仅在第一次导入时被解析
- 若是多个文件对某个导出做了修改，其他文件也可见这个修改
- import.meta 对象包含关于当前模块的信息
  - *内容取决于其所在的环境。在浏览器环境中，它包含当前脚本的 URL，或者如果它是在 HTML 中的话，则包含当前页面的 URL。*
- 在一个模块中，顶级 this 是 undefined。非模块脚本的顶级 this 是全局对象：
- 在生产环境中，出于性能和其他原因，开发者经常使用诸如 Webpack 之类的打包工具将模块打包到一起。

### ES6 Module
- 两个命令构成：export和import
  - export 规范对外
  - import 规范对内
  - export default：模块指定默认输出，对应的import语句不需要使用大括号
  - import命令会被 JavaScript 引擎静态分析，在编译时就引入模块代码，而不是在代码运行时加载，所以无法实现条件加载。也正因为这个，使得静态分析成为可能。

