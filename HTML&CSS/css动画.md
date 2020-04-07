**transform**
常用函数：基于元素的中心来变换
- translate(x,y):定义 2D 转换，沿着 X 和 Y 轴移动元素
- rotate(angle):定义 2D 旋转，在参数中规定角度
- scale(x,y)：定义 2D 缩放转换，改变元素的宽度和高度
- skew(x-angle,y-angle)：定义 2D 倾斜转换，沿着 X 和 Y 轴
- matrix(n,n,n,n,n,n)：定义 2D 转换，使用六个值的矩阵

transform-origin：
*对元素进行基点位置改变*
- transform-origin: x-axis y-axis z-axis
- 可以取字符参数值left=0%;center=50%;right=100%，也可以取百分比

3D transform变换方法
- 3D位移：CSS3中的3D位移主要包括translateZ()和translate3d()两个功能函数；
- 3D旋转：CSS3中的3D旋转主要包括rotateX()、rotateY(deg)、rotateZ()和rotate3d()四个功能函数；
- 3D缩放：CSS3中的3D缩放主要包括scaleZ()和scale3d()两个功能函数；
- 3D矩阵：CSS3中3D变形中和2D变形一样也有一个3D矩阵功能函数matrix3d()。
- 几个转换属性
  - transform-style： 规定被嵌套元素如何在 3D 空间中显示。
  - perspective： 规定 3D 元素的透视效果。
  - perspective-origin： 规定 3D 元素的底部位置。
  - backface-visibility： 定义元素在不面对屏幕时是否可见。

**Transition**
提供了一种在更改CSS属性时控制动画速度的方法。 其可以让属性变化成为一个持续一段时间的过程，而不是立即生效
语法：transition:transition-property | duration | timing function | delay */
先定义一个选择器，再用伪类(里面是transition的最终状态)修饰这个选择器
4个属性：
- transition-property：指定对HTML元素的哪个css属性进行过渡渐变处理，这个属性可以是color、width、height等各种标准的css属性
- transition-duration：指定属性过渡的持续时间
- transition-timing-function：指定渐变的速度函数
  1. ease：（逐渐变慢）默认值，ease函数等同于贝塞尔曲线(0.25, 0.1, 0.25, 1.0)；
  2. linear：（匀速），linear 函数等同于贝塞尔曲线(0.0, 0.0, 1.0, 1.0)；
  3. ease-in：(加速)，ease-in 函数等同于贝塞尔曲线(0.42, 0, 1.0, 1.0)；
  4. ease-out：（减速），ease-out 函数等同于贝塞尔曲线(0, 0, 0.58, 1.0)；
  5. ease-in-out：（加速然后减速），ease-in-out 函数等同于贝塞尔曲线(0.42, 0, 0.58, 1.0)；
  6. cubic-bezier：（该值允许你去自定义一个时间曲线）， 特定的cubic-bezier曲线。 (x1, y1, x2, y2)四个值特定于曲线上点P1和点P2。所有值需在[0, 1]区域内，否则无效。
- transition-delay：指定延迟时间，也就是经过多长时间才开始执行过渡过程。

过渡事件：
- transitionstart   开始
- transitioncancel  被取消
- transitionend      已完成
- transitionrun     开始，即delay过后

**animation**
语法： animation-name | during | time function | delay | time | direction | play-state
- animation-name:规定 @keyframes 动画的名称
- animation-duration：规定动画完成一个周期所花费的秒或毫秒
- animation-timing-function：规定动画的速度曲线	
- animation-delay：规定动画何时开始
- animation-iteration-count：规定动画被播放的次数
- animation-direction：规定动画是否在下一周期逆向地播放
  - reverse：反向播放；
  - alternate：奇数次正向播放，偶数次反向播放；
  - alternate-reverse：奇数次反向播放，偶数次正向播放。
- animation-play-state：规定动画是否正在运行或暂停 "running" "paused"

- @keyframes name{}
  - 关键帧用百分比规定动画的变换
  - 通过animation{name:}把它绑定到一个选择器

- 监听事件：
  - ` 'animationstart' `：动画第一次启动时，事件触发。
  - ` 'animationinteration' `：会在每一次新的动画执行过程中被触发，即除了第一次之外的每一个迭代过程。
  - ` 'animationend' `：事件会在动画结束时被触发。

### filter
filter函数的取值：
- blur()：给图像设置高斯模糊，0-max px;
- brightness()：给图片应用一种线性乘法 0%全黑  100%无变化
- contrast()：调整图像的对比度  0%全黑-100%无变化
- drop-shadow()：给图像设置一个阴影效果
  - <offset-x> <offset-y> (必须) 阴影出现的位置
  - <blur-radius> (可选) 值越大越模糊
  - <spread-radius>(可选) ：正值会使阴影扩张和变大，负值会是阴影缩小.
  - <color> (可选)
- grayscale()：将图像转换为灰度图像 0%原始-100%灰值图像
- hue-rotate()：给图像应用色相旋转 deg
- invert()：反转输入图像 0%无变化-100%完全反转
- opacity()：转化图像的透明程度。
- saturate()：转换图像饱和度。0%完全不饱和-100%无变化
- sepia()：将图像转换为深褐色，0%无变化-100%深褐色
以上函数可以复合使用，空格分开