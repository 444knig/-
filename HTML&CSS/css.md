Normalize vs Reset
- Normalize.css 保护了有价值的默认值
  - Reset通过为几乎所有的元素施加默认样式，强行使得元素有相同的视觉效果
  - Normalize.css保持了许多默认的浏览器样式，当一个元素在不同的浏览器中有不同的默认值时，Normalize.css会力求让这些样式保持一致并尽可能与现代标准相符合。
- Normalize.css 修复了浏览器的bug
  - 修复了常见的桌面端和移动端浏览器的bug
    1. HTML5元素的显示设置
    2. 预格式化文字的font-size问题
    3. 在IE9中SVG的溢出
    4. 许多出现在各浏览器和操作系统中的与表单相关的bug
- Normalize.css 不会让你的调试工具变的杂乱
  - 使用Reset最让人困扰的地方莫过于在浏览器调试工具中大段大段的继承链
  - 但因为Normalize.css准则中对多选择器的使用时非常谨慎的，仅会有目的地对目标元素设置样式，调试时更加直观
- Normalize.css 是模块化的
  - Normalize.css项目已经被拆分为多个相关却又独立的部分，这使得你能够很容易也很清楚地知道哪些元素被设置了特定的值。因此这能让你自己选择性地移除掉某些永远不会用到部分
- Normalize.css 拥有详细的文档

CSS性能优化的8个技巧
- 内联首屏关键CSS
  - 首次有效绘制（First Meaningful Paint，简称FMP）:指页面的首要内容（primary content）出现在屏幕上的时间
  - 因为在下载完HTML后才知道所引用的css文件，所以，内联CSS能够使浏览器开始页面渲染的时间提前
    - TCP的初始化拥塞窗口有限制，如果内联CSS后的文件超出了这一限制，系统就需要在服务器和浏览器之间进行更多次的往返
    - 因此只将渲染首屏内容所需的关键CSS内联到HTML中。
    - 内联CSS有一个缺点，内联之后的CSS不会进行缓存，每次都会重新下载，但是14.6kb内的话是可以接受的

- 异步加载CSS
  - 将首屏关键CSS内联后，剩余的CSS内容的阻塞渲染就不是必需的了，可以使用外部CSS，并且异步加载。
  - css异步模拟的几种方式：
    -  <link> 元素的 rel 属性的属性值preload，可以在浏览器的主渲染机制介入前就进行预加载
    -  将link元素的media属性设置为用户浏览器不匹配的媒体类型
       -  media="print"，甚至可以是完全不存在的类型media="noexist"，因为不匹配时，其优先级会被降低
       -  as 属性值：规定预加载的内容
    -  古老写法：
       - 使用JavaScript动态创建样式表link元素，并插入header的最后位置。

- 文件压缩
- 去除无用CSS

建议：
- 有选择地使用选择器
  - 保持简单，不要使用嵌套过多过于复杂的选择器。因为CSS选择器的匹配是从右向左进行的
  - 通配符和属性选择器效率最低，需要匹配的元素最多，尽量避免使用。
  - 不要使用类选择器和ID选择器修饰元素标签，如h3#markdown-content，这样多此一举，还会降低效率。
-  减少使用昂贵的属性
   -  如box-shadow/border-radius/:nth-child
- 硬件加速减少重绘
   - transform  translateZ(0) || rotateZ(360deg);
     - 做动画时用transform进行定位比top||left更快
   - opacity：当属性值不为1时，会创建一个新图层
   - filter：不为None时，CSS属性将模糊或颜色偏移等图形效果应用于元素。
   - will-change：transform  开启硬件加速
   - flex (flexbox) 容器的子元素，且 z-index 值不为 auto；
   - z-index：只对指定了 positioned属性的元素有效。
     - 浏览器有可能给复合层之后的所有相对或绝对定位的元素都创建一个复合层来渲染
     - *Element has a sibling with a lower z-index which has a compositing layer (in other words the it’s rendered on top of a composited layer)* the element will be put on conposited layer
     - 用认为的z-index提升他的复合层
- 不要使用@import：
  - 使用@import引入CSS会影响浏览器的并行下载，@import只有在被解析到该条语句后才会被请求和reflow
  - 多个@import会导致下载顺序紊乱

- @import
  - @import is the CSS mechanism to include a style sheet and `<link>` the HTML mechanism
  - 在样式表中使用@import让页面的整体下载时间增加，因为如果有link文件，它要等link文件下载完才开始下载。
  - 在IE中使用@import会导致下载顺序被更改（最后插入的脚本被最先执行）