对象属性 vs 访问器属性

对象标志
四个属性：
- value
- writable — 如果为 true，则值可以被修改，否则它是只可读的。
- enumerable — 如果为 true，则会被在循环中列出，否则不会被列出。
- configurable — 如果为 true，则此属性可以被删除，这些属性也可以被修改，否则不可以。

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

两种声明方式
- 当对象以字面量 和键值对的方式直接定义时，三个属性值为true
- 如果以createObject的方式声明，默认值为false

Object.defineProperty
- 如果该属性存在，defineProperty 会更新其标志。否则，它会使用给定的值和标志创建属性；没有指定标志时，默认值为false


查询有关属性的 完整信息：
- `descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);`
  - 返回值是一个所谓的“属性描述符”对象：它包含值和所有的标志。
  - 返回值为一个对象，不仅仅是简单的value
  - 访问器属性的设计思想：
    - 我们不以函数的方式 调用 user.fullName，我们正常 读取 它：getter 在幕后运行
    - 一个属性要么是访问器（具有 get/set 方法），要么是数据属性（具有 value）
    - 众所周知的约定，即以下划线 "_" 开头的属性是内部属性，不应该从对象外部进行访问

访问器描述符：
get —— 一个没有参数的函数，在读取属性时工作，
set —— 带有一个参数的函数，当属性被设置时调用，
enumerable —— 与数据属性的相同，
configurable —— 与数据属性的相同。

js Object：
枚举一个对象的所有属性：
- for...in 循环   for of 循环的是value，作用对象一般是数组
  - 包括原型，可枚举的属性
- Object.keys(o)
  - 不包含原型、可枚举
- Object.getOwnPropertyNames(o)
  - 不包含原型，包含不可枚举，属性名的数组

Object.prototype.hasOwnProperty()：
- 指示对象自身属性中是否具有指定的属性，不沿着原型链查找

Object.prototype.isPrototypeOf()
- 测试一个对象是否存在于另一个对象的原型链上。针对此对象的prototype进行检查

Object.assign(target, ...sources)：
- 用于将所有可枚举属性的值从一个或多个源对象复制到目标对象

Object.defineProperty(obj, prop)

Object.defineProperties(obj, props)：
- 直接在一个对象上定义新的属性或修改现有属性 {name:{},name2:{}};

Object.getOwnPropertyDescriptor(obj, prop)
- 返回指定对象上一个自身属性对应的属性描述符。

Object.getOwnPropertyDescriptors(obj)
- 返回所有描述符

Object.getOwnPropertyNames(obj)
- 返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括Symbol值作为名称的属性）组成的数组

Object.getOwnPropertySymbols(obj)
- 返回一个给定对象自身的所有 Symbol 属性的数组

Object.entries()
- 返回一个给定对象自身可枚举属性的键值对数组
- 顺序与for..in一致，但不同会遍历原型链上的值

Object.fromEntries(iterable)
- 把键值对列表转换为一个对象。

Object.getPrototypeOf(object)
- 方法返回指定对象的原型（内部`[[Prototype]]`属性的值）

Object.create(p)：
- 返回一个以p为原型的对象

Object.freeze()
- 冻结一个对象
  - 冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值，而且该对象的原型也不能被修改
- 返回，被冻结的对象

Object.is() 与 === 与 == 的对比:
- ==会做隐式的类型转换
- ===：将+0和-0视为相等，且认为Number.NAN!==Number.NAN;
- Object.is():正常的相等。。
