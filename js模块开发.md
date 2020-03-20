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
  
### ES6 Module
- 两个命令构成：export和import
  - export 规范对外
  - import 规范对内
  - export default：模块指定默认输出，对应的import语句不需要使用大括号
  - import命令会被 JavaScript 引擎静态分析，在编译时就引入模块代码，而不是在代码运行时加载，所以无法实现条件加载。也正因为这个，使得静态分析成为可能。
