js异步：
- setTimeout：
    - `let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)`
    - timerId 为计时器的句柄，可用clearTimeout取消调度
- setInterval：
    - `let timerId = setInterval(func|code, [delay], [arg1], [arg2], ...)`
    - timerId同理 clearInterval
- 异同点：
  - setTimeout的嵌套可以准确的设置函数的执行间隔，并且可以递归调用，以实现间隔扩大
  - setInterval：当函数执行时间t < delay时，间隔为delay-t 否则，为t.
  - 当delay设置为0，几次后delay被强制为4ms

事件循环：
- 一个在 JavaScript 引擎等待任务、执行任务和休眠等待更多任务这几个状态之间的无穷无尽的循环
  - 算法：当有任务时->从最先进入的任务开始执行->休眠到有新的任务进入，然后到第 1 步
- 任务队列就是一个集合，引擎来处理它们，然后等待更多的任务（即休眠，几乎不消耗 CPU 资源）
  - 任务到来时，引擎可能处于运行状态，那么这个任务就入列，多个任务组成的这个队列就叫做“宏任务队列” 
  - 先进先出原则
  - 细节的点：
    - 引擎处理任务时不会执行渲染。对于 DOM 的修改只有当任务执行完成才会被绘制。
    - 如果一个任务执行时间过长，浏览器无法处理其他任务，在一定时间后就会在整个页面抛出一个如“页面未响应”的警示建议终止这个任务。
    - 用例1：基于以上特点，将大任务拆分成小的，利用setTimeout周期性的执行任务
      - 把耗时任务放在调用setTimeout后能获得优化
    - 用例2：进度指示器
      - 因为只有在完成js任务时，才会进行对DOM的修改，因此，利用setTimeout可以展现任务的中间态。
    - 用例3：在事件之后做一些事情：
      - 事件处理中我们可能要延期一些行为的执行，直到事件冒泡完成并被所有层级接手和处理之后。我们可以把这部分代码放在 0 延迟的 setTimeout。
    - 在每个宏任务之后，引擎立即执行所有微任务队列中的任务，比任何其他的宏任务或者渲染或者其他事情都要优先。
  - 1. 加入微任务，更详细的事件循环的算法：
      1. 从宏任务队列出列并执行最前面的任务（比如“script”）。
      2. 出列并运行最前面的微任务
         1. 执行所有的微任务：
            1. 当微任务队列非空时：
      4. 如有需要执行渲染。
      5. 如果宏任务队列为空，休眠直到一个宏任务出现。
到步骤 1 中。
*promise.then()，相当于是一个then中的回调函数和val放入微队列的过程*

- promise:
  - 在 executor 周围的“隐式 try..catch”自动捕获了 error，并将其变为  rejected promise。
  - 同样的，当.then处理程序中被throw，一样会被后面的catch reject 
  - unhandledrejection ：没有被promise catch 的错误
- 使用finally处理相同的情况;
  - finally() 方法返回一个Promise。在promise结束时，无论结果是fulfilled或者是rejected，都会执行指定的回调函数
- Promise.all:
  - 并行执行多个 promise，并等待所有 promise 都准备就绪。
  - `let promise = Promise.all([...promises...]);`
  - 返回的promise也是一个数组，与参数数组的顺序一样
  - 其中一个 promise 被 reject，Promise.all 就会立即被 reject，完全忽略列表中其他的 promise。它们的结果也被忽略
  - 通常，Promise.all(...) 接受可迭代对象（iterable）的 promise（大多数情况下是数组）。但是，如果这些对象中的任意一个都不是 promise，那么它将被“按原样”传递给结果数组。
  - 如果需要知道每一个promise的返回情况：
    - Promise.allSettled 等待所有的 promise 都被 settle，无论结果如何。结果数组具有：
      - {status:"fulfilled", value:result} 对于成功的响应，
      - {status:"rejected", reason:error} 对于 error。
    - allSettled的polyfill：
     ```js
      if(!Promise.allSettled) {
      Promise.allSettled = function(promises) {
          return Promise.all(promises.map(p => Promise.resolve(p).then(value => ({
          state: 'fulfilled',
          value
        }), reason => ({
          state: 'rejected',
          reason
        }))));
      };
    }
    ```
  - promise.race
    - 等待第一个 settled 的 promise 并获取其结果（或 error）
  - `Promise.resolve(value)` – 使用给定 value 创建一个 resolved 的 promise。
  - `Promise.reject(error)` – 使用给定 error 创建一个 rejected 的 promise。
  
### Promisification
- 将一个接受回调的函数转换为一个返回 promise 的函数。
  ```js 代码例子：
  function promisify(f) {
    return function (...args) { // 返回一个包装函数（wrapper-function）
        return new Promise((resolve, reject) => {
            args.push((err, result)=>{
                if (err) {
                    reject(err);
                } else {
                    resolve(result);
                }
            }); // 将我们的自定义的回调附加到 f 参数（arguments）的末尾

            f.call(this, ...args); // 调用原始的函数
        });
    };
  };

  let loadScriptPromise = promisify(loadScript);
  loadScriptPromise("first").then(()=>{});

  function loadScript(src, callback) {
    //这个callback就是promisify中处理reject和resolve的内容
      let script = document.createElement('script');
      script.src = src;
      //resolve 之后执行then
      script.onload = () => callback(null, script);
      script.onerror = () => callback(new Error(`Script load error for ${src}`));

      document.head.append(script);
  }


  ```

### Async/await
**async** 
- 放置在函数前：这个函数总是返回一个 promise。其他值将自动被包装在一个 resolved 的 promise 中。当然也可以显示返回一个`Promise.resolve(1);`。这与结果没什么区别
- *总的来说：async 确保了函数返回一个 promise，也会将非 promise 的值包装进去*
- *async里如果有多个await函数的时候，如果其中任一一个抛出异常或者报错了，都会导致函数停止执行，直接reject*
  - 使用try catch

*调用async函数就像是强制异步函数的调用*

**await**
> 如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。
> 如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。(会执行微队列)
> await 阻塞的只是当前路径，并不阻塞其它路径的代码。不然异步就没意义了。(一定会执行同步栈的内容)
*只能在使用async定义的函数里面使用，等一个表达式的结果*
- await 字面的意思就是让 JavaScript 引擎等待直到 promise settle，然后以 promise 的结果继续执行
  ```js 相比与then，这样的写法更加优雅
  async function f() {

    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("done!"), 1000)
    });

    let result = await promise; // 等待，直到 promise resolve (*)

    console.log(result) // "done!"
  }
  ```
  - await 允许修室thenable对象
  - await all([...])
  - await的结果：
    - 如果有 error，就会抛出异常 — 就像那里调用了 throw error 一样。
    - 否则，就返回结果。
- await的执行含义是：
  - 实际上await是一个让出线程的标志。await后面的函数会先执行一遍(比如await Fn()的Fn ,并非是下一行代码)，然后就会**跳出整个async函数**来执行后面js栈的代码。等**本轮事件循环执行**(先执行完同步站，再执行完微队列)完了之后又会跳回到async函数中等待await后面表达式的返回值，如果返回值为非promise则继续执行async函数后面的代码，否则将返回的promise放入Promise队列（Promise的Job Queue）


**Generator 函数**
- Generator 函数是一个普通函数。
- 两个特征：
  - function关键字与函数名之间有一个星号
  - 函数体内部使用yield表达式，定义不同的内部状态

函数作用：
- 调用该函数后，返回一个指向内部状态的指针对象
- 每次调用next方法，就从函数头或者上一个yield出发执行，知道遇到下一个yield或return
  - 分段执行，yield表达式是暂停执行的标记，而next方法可以恢复执行
  - 调用next()时，返回一个有着value和done两个属性的对象，
  - value是yield后或返回的表达式，当done为true后，后面的调用都为undefined(或者没有return语句时).
  - done表示遍历是否结束。

一些语法：
- 包裹yield的函数必须带有*
- yield表达式如果用在另一个表达式之中，必须放在圆括号里面。

与iterator的关系：
- 可以把 Generator 赋值给对象的Symbol.iterator属性，从而使得该对象具有 Iterator 接口

next 方法的参数 
- yield表达式本身没有返回值，或者说总是返回undefined
- next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。
- 第一次调用时，参数无效    
*可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为*

for...of 循环
- 可以自动遍历 Generator 函数运行时生成的Iterator对象，且此时不再需要调用next方法。
- 一旦next方法的返回对象的done属性为true，for...of循环就会中止，且不包含该返回对象
- 为不可遍历的对象加上iterator接口

#### Generator的原型方法：
- Generator.prototype.throw
  - 可以在函数体外抛出错误，然后在 Generator 函数体内捕获。
  - throw方法可以接受一个参数，该参数会被catch语句接收
  - throw方法抛出的错误要被内部捕获，前提是必须至少执行过一次next方法。否则会再外部捕获
    - 如果外部捕获了throw的错误，其内部没有被捕获，将不会再执行
  - throw方法被捕获以后，会附带执行下一条yield表达式（额外调用一次yield）

- Generator.prototype.return
  - 返回一个对象，value为return的参数值(默认为undefined)，done===true;
  - 如果 Generator 函数内部有try...finally代码块，且正在执行try代码块
    - return方法会立即进入finally代码块，执行完毕后才会返回
    - 如果finally中有yield，会等待这些代码被next调用，才会按规则1返回

- next()、throw()、return() 
  - 他们的共同点，都是让 Generator 函数恢复执行
  - next()是将yield表达式替换成一个值。
  - throw()是将yield表达式替换成一个throw语句。
  - return()是将yield表达式替换成一个return语句。

#### yield* 表达式
设计思想：
- 如果在 Generator 函数内部，调用另一个 Generator 函数。需要在前者的函数体内部，自己手动完成遍历。这种需求写起来非常麻烦。

yield*表达式
- 用来在一个 Generator 函数里面执行另一个 Generator 函数。
- 作用类似于`[...迭代器||function*]`  

#### 作为对象属性的 Generator 函数
如果一个对象的属性是 Generator 函数，可以简写成下面的形式。
  ```js
    let obj = {
      * myGeneratorMethod() {}
    };
  ```

#### Generator 函数的this
- Generator 函数总是返回一个遍历器，ES6 规定这个遍历器是 Generator 函数的实例，也继承了 Generator 函数的prototype对象上的方法
  ```js
  function* g() {}

  g.prototype.hello = function () {
  return 'hi!';
  };
  let obj = g();
  obj instanceof g // true
  obj.hello() // 'hi!'
  ```
- obj是g的实例，其__proto__指向g.prototype
  - 但是把g当作普通的构造函数，并不会生效，因为g返回的总是**遍历器对象**，而不是this对象

既可以用next方法，又可以获得正常的this：
  ```js
  function* F() {
  this.a = 1;
  yield this.b = 2;
  yield this.c = 3;
  }
  var obj = {};
  var f = F.call(obj);
  //方法二
  //var f = F.call(F.prototype);

  //方法三
  //function F() {
  //  return gen.call(gen.prototype);
  //}

  //var f = new F();

  f.next();  // Object {value: 2, done: false}
  f.next();  // Object {value: 3, done: false}
  f.next();  // Object {value: undefined, done: true}

  obj.a // 1
  obj.b // 2
  obj.c /
  ```
  - 将F中的this绑定obj对象，调用next时，即调用F的属性初始化，调用两次后九江属性都绑定在了obj上
  - 缺点，obj的属性依赖于F本身的执行
    - 改良，将obj改为prototype，即函数F的执行的结果是将属性添加到了prototype上
      - 缺点：属性是共享的，初始化的过程也是对象共享的。
  
#### Generator 与状态机

#### Generator 与协程
- Generator 函数是 ES6 对协程的实现，但属于不完全实现。Generator 函数被称为“半协程”（semi-coroutine），意思是只有 Generator 函数的调用者，才能将程序的执行权还给 Generator 函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

- 如果将 Generator 函数当作协程，完全可以将多个需要互相协作的任务写成 Generator 函数，它们之间使用yield表达式交换控制权。

#### Generator 与上下文
- Generator 函数执行产生的上下文环境，一旦遇到yield命令，就会暂时退出堆栈，里面的所有变量和对象会冻结在当前状态。等到对它执行next命令时，这个上下文环境又会重新加入调用栈，冻结的变量和对象恢复执行。

#### 应用：
- 异步操作的同步化表达
  - Generator 函数的暂停执行的效果，意味着可以把异步操作写在yield表达式里面
- 控制流管理
- 部署 Iterator 接口
- 作为数据结构：Generator 使得数据或者操作，具备了类似数组的接口。

***
$$Js回调函数$$
- 回调函数，第一个参数规定了是err
- 回调函数地狱：
  - 代码横向发展，多个异步操作形成了强耦合，只要有一个操作需要修改，上下的函数可能都要修改
- promise写法：
  - Promise 的写法只是回调函数的改进，使用then方法以后，异步任务的两段执行看得更清楚了
  - 最大的问题是代码冗余，原来的任务会被promise包装，然后多出来一堆then

Generator 函数
- 协程(coroutine)：
- 大致流程：
  - 协程A开始执行
  - 协程A执行到一半，进入暂停，执行权转移到协程B
  - （一段时间后）协程B交还执行权
  - 协程A恢复执行
- 这个协程的执行，实际上就是在做异步任务

- 协程的 Generator 函数实现
- Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权(暂停执行)

- Thunk 函数的含义
  - 一种传名调用的实现策略

- JavaScript 语言的 Thunk 函数