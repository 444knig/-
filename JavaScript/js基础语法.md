* 创建一个正则表达式:
  * `var re = /ab+c/;` 其由包含在斜杠之间的模式组成
    * 脚本加载后，正则表达式字面量就会被编译。当正则表达式保持不变时，使用此方法可获得更好的性能

* 类型和语法：
  * Number：使用固定数量的位（64 位）来存储单个数字值
  * 特殊数字：
    * Infinity 正无穷 无穷减一”仍然是“无穷“
    * -Infinity
    * NaN代表“不是数字”，即使它是数字类型的值。Infinity - Infinity或任何其他数字操作，它不会产生有意义的结果时，你将得到此结果
  * 字符串：
    * 反引号（` ）引用字符串时，换行符（当你按回车键时获得的字符）可能会被包含，而无需转义
    * JavaScript 的表示为每个字符串元素使用 16 位 Unicode
    * Unicode 定义的字符多于此(16 位 Unicode) - 大约是此处的两倍。 所以有些字符，比如许多 emoji，在 JavaScript 字符串中占据了两个“字符位置”
  * 二元运算符
  * 只有一个值不等于其自身，那就是NaN
  * ||优先级最低，其次是&&，接着是比较运算符（>，==等），最后是其他运算符
* 空值：
  * null和undefined
  * JavaScript 会悄悄地将该值转换为所需的类型，并使用一组通常不是你想要或期望的规则。 这称为类型转换
  * 当某些不能明显映射为数字的东西（如"five"或undefined）转换为数字时，你会得到值NaN
  * 但如果运算符两侧存在null或undefined，那么只有两侧均为null或undefined时结果才为true
  * 字符串和数字转换为布尔值的规则表明，0，NaN和空字符串（""）计为false，而其他所有值都计为true。
  * 逻辑运算符的短路特性
  * undefined 是声明了变量但未对其初始化时赋予该变量的值，null 则用于表示尚未存在的对象
* Number(undefined) NAN  Nunber(null) 0   
***
* JavaScript表达式：
* 逗号操作符会对所有表达式求值并且返回第二个表达式的结果表达式是一组代码的集合，它返回一个值
* 值的转换
  * ToPrimitive是一个内部函数
    * 如果输入是原始的，则按原样返回。
    * 否则，输入是一个对象。调用obj.valueOf（）。如果结果是原始的，则将其返回。
    * 否则，调用obj.toString（）。如果结果是原始数据，则将其返回。否则，抛出TypeError。
    * Number(), toString() 的内部实现
    * `{} + {}`: NaN  第一个{}被解析为了statement
       * +{}
       *  Number({})
       *  Number({}.toString())  // {}.valueOf() isn’t primitive
       *  Number("[object Object]")
       *  NaN
    * Number()  先转valueof,再tostring，如果仍非(Bool,Num,Und,Null,Str) 则抛出异常
  * JavaScript的值可以分为：
    * primitives 5种 (Bool,Num,Und,Null,Str)
    * object 
      * primitives的包装类
      * Creatable by literals. 
        * [] is the same as new Array()
        * {} is the same as new Object()
        * function() {} is the same as new Function()
        * `/\s*/` is the same as `new RegExp("\\s*")`
      * Dates: new Date("2011-12-24")
    * 默认情况下，对象是可变的 
    * Primitives are immutable: any property you add will be immediately forgotten.
    * Primitives don’t have their own methods and borrow them from wrappers:
      * Methods in Object.prototype are available to all primitives
    * 没有任何功能可以做到这一点，因为您不能将这样的变量传递给它
  * instanceOf:
    * instanceOf：value instanceof Constructor -> Constructor.prototype.isPrototypeOf(value)
    *  Primitives are not an instance of anything.
***
* 绑定：
  * 名称：美元符号（$）或下划线（_）
* 绑定和作用域
  * 对于在任何函数或块之外定义的绑定，作用域是整个程序 - 您可以在任何地方引用这种绑定。它们被称为全局的
  * 但是为函数参数创建的，或在函数内部声明的绑定，只能在该函数中引用，所以它们被称为局部绑定
* 嵌套作用域
  * 局部作用域也可以看到所有包含它的局部作用域
  * 块内可见的绑定集，由这个块在程序文本中的位置决定
  * 每个局部作用域也可以看到包含它的所有局部作用域，并且所有作用域都可以看到全局作用域。 这种绑定可见性方法称为词法作用域。
    * **函数表达式**是一个常规绑定，其中绑定的值是一个函数：function(){}; 需要分号，
    * **符号声明**：function f (){} 不需要分号
    * **箭头函数** ()=>{}
    * 立即调用函数表达式：(function(){} ())
  * 函数声明不是常规的从上到下的控制流的一部分。 在概念上，它们移到了其作用域的顶部，并可被该作用域内的所有代码使用。
  * 可选参数
  * JavaScript 对传入函数的参数数量几乎不做任何限制。如果你传递了过多参数，多余的参数就会被忽略掉，而如果你传递的参数过少，遗漏的参数将会被赋值成undefined
* 闭包
  * 函数可以作为值使用，而且其局部绑定会在每次函数调用时重新创建
    * 如果函数已经执行结束，那么这些由函数创建的局部绑定会如何处理呢？
    * 这种情况很好地证明了一个事实，每次调用都会重新创建局部绑定，而且不同的调用不能覆盖彼此的局部绑定
  * 一个好的模型是，将函数值看作值，包含他们主体中的代码和它们的创建环境。 被调用时，函数体会看到它的**创建环境**，而不是它的调用环境。
  * 函数及其副作用:
    * 一类调用后产生副作用，而另一类则产生返回值
    * 相比于直接产生副作用的函数，产生返回值的函数则更容易集成到新的环境当中使用。
    * 纯函数: 是一种特定类型的，生成值的函数，它不仅没有副作用，而且也不依赖其他代码的副作用
      * 当用相同的参数调用它时，它总是产生相同的值（并且不会做任何其他操作）
      * 这种函数的调用，可以由它的返回值代替而不改变代码的含义
    * 有时在副作用的帮助下，有些操作则更易、更快实现，因此考虑到运算速度，有时候纯函数并不可取。
  * 属性：
    *  JavaScript 中，几乎所有的值都有属性。但null和undefined没有
    *  在JavaScript中访问属性的两种主要方式是点（.）和方括号（[]）
       *  点后面的单词是该属性的字面名称。 使用方括号时，会求解括号内的表达式来获取属性名称
       *  元素以数组属性的形式存储，使用数字作为属性名称
       *  我们可以使用=运算符来给一个属性表达式赋值。如果该属性已经存在，那么这项操作就会替换原有的值。如果该属性不存在，则会在目标对象中新建一个属性
       *  delete运算符切断章鱼的触手。 这是一个一元运算符，当应用于对象属性时，将从对象中删除指定的属性。 这不是一件常见的事情，但它是可能的
       *  二元in运算符会告诉你该对象是否具有名称为它的属性。 将属性设置为undefined，和实际删除它的区别在于，在第一种情况下，对象仍然具有属性（它只是没有有意义的值），而在第二种情况下属性不再存在，in会返回false
       *  Object.assign函数可以将一个对象的所有属性复制到另一个对象中。拷贝属性和引用
       *  数组只是一种对象，专门用于存储对象序列。 如果你求解typeof []，它会产生object
       *  ECMAScript 的原始类型，即 Undefined、Null、Boolean、Number 和 String 型
       *  未定义变量除了typeof运算符，其余都会运行错误
  * [] 数组的函数：
    * shift unshift indexOf slice[ , )  concat 
- 对象不可变：
  - Object.freeze() 方法可以冻结一个对象，并返回被密封后的参数对象，该对象的原型也不能被修改。
    - 浅冻结
  - isSeal(): 密封也是浅层属性的密封
  - Object.isExtensible() 方法判断一个对象是否是可扩展的 浅层
- for .. in
- 语法：
  ```js
  for (variable in object)
  statement
  ```
  - variable不声明的时候，默认为var
  - 遍历可枚举属性（包括它的原型链上的可枚举属性）但一般原型链上的属性方法都是不可迭代的
  - 循环以任意序迭代一个对象的属性
  - 在一次循环中，关于 delete add：
    - *If a property is modified in one iteration and then visited at a later time, its value in the loop is its value at that later time*
    - 一个属性在被访问之前delete，那之后不会再访问到
    - 循环时添加属性，不一定会被访问
  - 在迭代过程中最好不要在对象上进行添加、修改或者删除属性的操作，除非是对当前正在被访问的属性
  - 遍历数组时不要用此方法，因为此方法的次序是依赖于操作系统的
- for...of
- ```js
  for (variable of iterable) {
  statement
  }
  ```
  - variable不声明的时候，默认为var
  - 在可迭代对象上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句
    - 判断一个对象是否为可迭代的：`obj!==null && typeof obj[Symbol.iterator] === function`

iterator接口  
- 原生具备 Iterator 接口的数据结构：
  - Array、Map、Set、String、TypedArray、函数的 arguments 对象、NodeList 对象
- 普通对象部署该接口没有效果

- 一些调用该接口的场合：
  - 解构赋值
    - 对数组和 Set 结构进行解构赋值时，会默认调用Symbol.iterator方法。
  - 扩展运算符
  - yield*
    - yield*后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。
  - 其他场合：
    - for...of
    - Array.from()
    - Map(), Set(), WeakMap(), WeakSet()（比如new Map([['a',1],['b',2]])）
    - Promise.all()
    - Promise.race()

try-catch-finally
- 只要有finally，而且代码进入了try-catch块，则必会执行finally的内容
- 而且finally的return语句会覆盖之前的return

JSON.parse():
- 接受一个 JSON 字符串并将其转换成一个 JavaScript 对象。
-  JSON.parse(text[, reviver(k,v)]) 返回值Object类型
- 提供可选的 reviver 函数用以在返回之前对所得到的对象执行变换(操作)
  - reviver接受 key value 两个参数，当reviver 返回 undefined，则当前属性会从所属对象中删除
  - 遍历到最顶层的值（解析值text）时，传入 reviver 函数的参数会是空字符串 ""
  - 该函数的遍历顺序从最内层开始，按照层级顺序，依次向外遍历
- JSON.parse() 不允许用逗号作为结尾

JSON.stringify(obj):
- JSON.stringify() 方法将一个 JavaScript 值（对象或者数组）转换为一个 JSON 字符串
 
- 语法：JSON.stringify(value[, replacer [, space]])
  - replacer：
    - 如果该参数是一个函数：被序列化的值的每个属性都会经过该函数的转换和处理
    - 如果该参数是一个数组，则只有包含在这个数组中的属性名才会被序列化到最终的 JSON 字符串中
    - 如果该参数为 null 或者未提供，则对象所有的属性都会被序列化
    - 如果返回 undefined，该属性值不会在 JSON 字符串中输出
  - space：
    - 指定缩进用的空白字符串，用于美化输出（pretty-print）
    - 如果参数是个数字，它代表有多少的空格  `1<x<10`
- 描述:
  - 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
  - 布尔值、数字、字符串的包装对象在序列化过程中会自动拆包
  - 在非数组对象的属性值中，undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略；在数组中会被转为null
  - 对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方法，会抛出错误。
  - Date 日期调用了 toJSON() 将其转换为了 string 字符串（同Date.toISOString()），因此会被当做字符串处理。
  - NaN 和 Infinity 格式的数值及 null 都会被当做 null
  - 其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会**序列化可枚举的属性**。
- 用处：
  - 目前所有的浏览器中都会把localStorage的值类型限定为string类型，这个在对我们日常比较常见的JSON对象类型需要一些转换。
  - localStorage的值只能是string类型

- toJSON 方法
  - 如果一个被序列化的对象拥有 toJSON 方法，那么该 toJSON 方法就会覆盖该对象默认的序列化行为：不是该对象被序列化，而是调用 toJSON 方法后的返回值会被序列化，例如：

***
$$Object.prototype$$
- 属性的值是 null 或 undefined，只要属性存在，hasOwnProperty 依旧会返回 true。

- JavaScript 类数组对象的定义
  - 可以通过索引访问元素，并且拥有 length 属性；
  - 没有数组的其他方法，例如 push ， forEach ， indexOf 等。

