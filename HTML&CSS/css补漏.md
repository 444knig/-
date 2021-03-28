hsla() 函数使用色相、饱和度、亮度、透明度来定义颜色。
色相（H）是色彩的基本属性，就是平常所说的颜色名称，如红色、黄色等。
饱和度（S）是指色彩的纯度，越高色彩越纯，低则逐渐变灰，取 0-100% 的数值。
亮度（L） 取 0-100%，增加亮度，颜色会向白色变化；减少亮度，颜色会向黑色变化。
透明度（A） 取值 0~1 之间， 代表透明度。

var() 函数用于插入自定义的属性值，如果一个属性值在多处被使用，该方法就很有用。

background属性：
- `<attachment>`：决定背景图像的位置是在视口内固定，还是随着包含它的区块滚动。
  - fixed：相对于视口固定。即使一个元素拥有滚动机制，背景也不会随着元素的内容滚动。
  - local：背景相对于元素的内容固定。如果一个元素拥有滚动机制，背景将会随着元素的内容滚动
  - scroll：此关键字表示背景相对于容器本身固定， 而不是随着它的内容滚动
- box:
- 可能出现0，1，2次，若出现两次，第一次的出现设置 background-origin，第二次的出现设置 background-clip
  - `background-clip`：设置元素的背景（背景图片或颜色）是否延伸到边框下面
    - border-box
    - padding-box
    - content-box
    - text
  - 该标签对背景(定义的颜色和图片当作一个图层)做直接的裁剪，像贴纸
  - `background-origin`:
    - border-box
    - padding-box
    - content-box
  - 该标签重定位背景图片的起始位置，颜色依然是铺满，默认值： padding box 
- `color`：定义背景颜色
- `background-image`：定义一个或多个背景图片，后面出现的会对前面的进行叠加
  - 可以用line-gradient函数进行定义
- `background-position`：定义背景图相对于bg-origin开始的位置
  - 可以用right，top来详细指定其位置
  - `bottom 10px right 20px;`
- `background-repeat`：定义背景图像的重复方式
  - repeat-x | repeat-y | repeat | space | round | no-repeat
- `background-size`：设置背景图片大小。图片可以保有其原有的尺寸，或者拉伸到新的尺寸
  - 使用前加一个 /: `[ / <bg-size> ]?` 目的是为了防止与position歧义