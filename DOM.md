### HTML DOM
document对象的属性和方法的基本引用

addEventListener默认是冒泡 默认参数为false

捕抓事件和冒泡原理--DOM的两种事件流
冒泡：网上传  现代浏览器一般传到document->window
事件捕获:不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件  反方向
捕抓阶段->处于目标阶段->事件冒泡阶段

事件委托的优点：
- 做法：把这个点击事件绑定到他的父层，也就是 `ul` 上，然后在执行事件的时候再去匹配判断目标元素
- 减少内存消耗
- 动态绑定事件：绑定在父层的，和目标元素的增减是没有关系的

childNodes：返回指定元素的子元素集合，包括HTML节点，所有属性，文本。
当nodeType==1时才是元素节点，2是属性节点，3是文本节点

document.querySelector("p")-> 找到第一个`<p>` 元素
document.querySelector(".p")-> 找到第一个`class = 'p'` 元素
document.querySelector("#p")-> 找到第一个`id = 'p'` 元素