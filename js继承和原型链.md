* js的call apply 
call() 方法接受的是一个参数列表，而 apply() 方法接受的是一个包含多个参数的数组  

call()详解：

function.call(thisArg, arg1, arg2, ...)
thisArg：可选的。arg1, arg2, ... 指定的参数列表。  
返回值：使用**调用者提供的 this 值**和参数调用该函数的返回值。若该方法(调用该方法的方法xxx.call)没有返回值，则返回 undefined。  

apply类似于call

bind 可以改变函数体类的this的指向
例子代码：
```js
var bar = function(){
    console.log(this.x);
}
var foo = {
    x:3
}
var sed = {
    x:4
}
var func = bar.bind(foo).bind(sed);
func(); //3
 
var fiv = {
    x:5
}
var func = bar.bind(foo).bind(sed).bind(fiv);
func(); //3
```
解释：3的缘故，bind(foo)，将bar执行时的this设置成了foo，因此为3。
不是3？：bind的函数实现相当于使用函数在内部包了一个 call / apply 
结论：
1. 可以看出 call 和 apply 是为了动态改变 this 而出现的
2. 不明确参数是用apply，否则用call
    * 例子： Array.prototype.slice.call 转换为真正的数组的带有 length 属性的对象
3. 三者区别：当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。

代码如下：
```js
var obj = {
    x: 81,
};
 
var foo = {
    getX: function() {
        return this.x;
    }
}
 
console.log(foo.getX.bind(obj)());  //81 多了对括号
console.log(foo.getX.call(obj));    //81
console.log(foo.getX.apply(obj));   //81
```
btw 严格模式下，this的值为undefined

js 继承与原型链：
`__proto__` 是一种访问 [[Prototype]] 的方式，而不是 [[prototype]] 本身。
for in 循环也会遍历继承的值，只能枚举可以枚举的对象
- 通过 [[Prototype]] 引用的对象被称为“原型”。
- 继承： `x._proto_ = `
- 构造函数的使用方式：function F(){} F.prototype = super
  - 子类的生成：let x = new F()  --->  `x.__proto__  = F.prototype`

默认的 "prototype" 是一个只有属性 constructor 的对象，属性 constructor 指向函数自身。
F.prototype.constructor = F
可以使用 constructor 属性来创建一个新对象`let rabbit2 = new rabbit.constructor("Black Rabbit");`
只要不将F.prototype覆盖，就能保证 f.constructor指向创建它的构造函数
- F.prototype 在子类实例之后被覆盖，不会影响子类的内容，因为子类的内容通过_proto_获取
- 但改变F.prototype 引用，会改变子类的内容
- delete只会作用到对象，不会随着继承链往上传，若删除构造函数prototype的内容，会影响子类编程undefined

- 字面量的创建过程：
  -  ={} => = new Obejct()   
  -  `{}._proto_===Object.prototype`
- 基本数据类型:
  - 值 null 和 undefined 没有对象包装器，也没有原型
  - 字符串、数字和布尔值他们并不是对象，但如果我们访问他们的属性，，那么临时包装器对象将会通过内建的构造函数 String、Number 和 Boolean 被创建。它们提供给我们操作字符串、数字和布尔值的方法然后消失。
  - 原生的原型是可以被增加或删除一些方法的，这种方法的添加是全局的，尽量回避
  - 只有一种情况下允许修改原生原型。那就是 polyfilling，先判断有没有
  - 从原型中借用一些方法
- 直接用_proto_是不合适的，应该改为用
  - 创建一个以 animal 为原型的新对象：`let rabbit = Object.create(animal);`
  - 获取`_proto_` Object.getPrototypeOf(rabbit)
  - 修改原型：`Object.setPrototypeOf(rabbit, {});`
  - `Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));` 完全相同的浅拷贝
  - Object.setPrototypeOf 或 `obj.__proto__= `“即时”更改原型是一个非常缓慢的操作，因为它破坏了对象属性访问操作的内部优化
  - 如果要将一个用户生成的键放入一个对象，那么内建的 __proto__ getter/setter 是不安全的

- JavaScript对象标志
  - 对象属性（properties），除 value 外，还有三个特殊的特性（attributes）
  - 当对象以字面量 和键值对的方式直接定义时，三个属性值为true
  - 如果以createObject的方式声明，则为false
  - writable — 如果为 true，则值可以被修改，否则它是只可读的。
  - enumerable — 如果为 true，则会被在循环中列出，否则不会被列出。
  - configurable — 如果为 true，则此属性可以被删除，这些属性也可以被修改，否则不可以。
  - `descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);`
    - 返回值是一个所谓的“属性描述符”对象：它包含值和所有的标志。
    - 返回值为一个对象，不仅仅是简单的value
  - Object.defineProperty，没有指定标志后认为是false
    - 可以通过应用自己的 defineProperty 来改变value的值，只读只写
      - 只在严格模式下会出现 Errors，非严格无错误，但不会变
    - 对象内置的tostring是不可遍历的，因此keys不会返回
    - 不可配置性：configurable 是条单行道，理由如下
    - 不可配置性对 defineProperty 施加了一些限制：
      - 不能修改 configurable 标志。
      - 不能修改 enumerable 标志。
      - 不能将 writable: false 修改为 true
      - 不能修改访问者属性的 get/set
    - configurable: false 的思想是防止更改属性标志或删除属性，而不是更改它的值。
  - 访问器属性的设计思想：
    - 我们不以函数的方式 调用 user.fullName，我们正常 读取 它：getter 在幕后运行
    - 一个属性要么是访问器（具有 get/set 方法），要么是数据属性（具有 value）
    - 众所周知的约定，即以下划线 "_" 开头的属性是内部属性，不应该从对象外部进行访问
  - JavaScript的class语法：
    - `class MyClass { constructor(){} m1(){} }`
    - 当 new User("John") 被调用：
      - 一个新对象被创建
      - constructor 使用给定的参数运行，并为其分配 this.name。
    - class 并不是全新的语法：而是一个函数体
      - `class User {...}` 类的构造做了些啥：
        - 创建一个名为 User 的函数，该函数成为类声明的结果,代码来自于 constructor 方法
        - 存储类中的方法，例如 User.prototype 中的 sayHi。
      - class的声明与函数体的区别：
        - 创建的函数具有特殊的内部属性标记 `[[FunctionKind]]:"classConstructor"`
        - 调用类构造器时必须要用 new 关键词：
        - 类定义将 "prototype" 中的所有方法的 enumerable 标志设置为 false。
        - 类总是使用 use strict
    - 类表达式
      - 类可以在另外一个表达式中被定义，被传递，被返回，被赋值  `let User = class {}`
      - 可以动态地“按需”创建类
    - class的属性是创建在对象中的
  - extent关键字的实现：![继承图](img/继承.png)
    - 在 extends 后允许任意表达式
        ```js
        function f(phrase) {
        return class {
            sayHi() { alert(phrase) }
        }
        }

        class User extends f("Hello") {}

        new User().sayHi(); // Hello
        ```
    - 箭头函数没有 super，如果被访问，它会从外部函数获取。
    - 继承类的构造函数
      - new 执行一个常规函数时，它将创建一个空对象，并将这个空对象赋值给 this
      - 继承的 constructor 执行时，须调用 super 才能执行其父类（非派生的）的 constructor，否则 this 指向的那个对象将不会被创建
      - 