
## `<form>`的enctype

- application/x-www-form-urlencoded（默认），在发送前编码所有字符
- multipart/form-data，不对字符编码，使用文件上传时必须使用改值

## Canvas优缺点，应用场景

**优点：**
- 定制性强，像素级别的绘制可以绘制图像。
- 画布中的形状非DOM节点，用js进行绘制，涉及到动画性能更高。
- 可使用WebGL加速。

**缺点：**
- 交互性弱，不支持事件处理，交互区域和拖拽等功能需要根据鼠标坐标手动编程。
- 功能简单，只能脚本驱动。
- 弱的文本渲染能力。
- 无法SEO。

**应用场景：**
- 复杂丰富的数据可视化展示。
- 高性能滤镜，光线追踪器。
- 2D休闲游戏。
- 动态渲染。

## Canvas性能优化

- **剪辑区域：** 假设在某个Canvas动画中，仅有右下角的小人儿展示跳跃的动画，其他画面元素不变，可以通过剪辑区域设置为小人儿的区域，之后的每一帧的重复擦出绘制都仅绘制小人儿区域的画布区域，从而优化性能。

- **离屏canvas：** 动画中的每种元素（层），对渲染和动画的要求是不一样的。对很多游戏而言，主要角色变化的频率和幅度是很大的（他们通常都是走来走去，打打杀杀的），而背景变化的频率或幅度则相对较小，很明显，我们需要很频繁地更新和重绘人物，但是对于背景，我们也许只需要绘制一次，或者以很慢的频率，但绝对不必达到16ms刷新一次。使用上，分层 Canvas 也很简单。我们需要做的，仅仅是生成多个 Canvas 实例，把它们重叠放置，每个 Canvas 使用不同的 z-index 来定义堆叠的次序。然后仅在需要绘制该层的时候进行重绘。

- **避免16ms阻塞：** canvas的api毕竟是用js执行的，有时候可能会因为运行了复杂的算法来导致了较短的阻塞，让执行下一帧的绘制超过了16ms，进而导致丢帧，如果算法和下一帧的绘制无关，我们可以将复杂的算法利用Web Worker或者将任务拆分来优化。

- **开销较大的context赋值：** canvas的api是在context上调用的，我做了对一些常用api进行百万次规模赋值并测试运行时间，比如lineWidth、font、shadowColor等等，发现比对一个普通对象的属性赋值的开销要大1-3个数量级，其中开销最大的是font属性，所以对于优化来说，不要频繁设置绘图上下文的 font 属性，尽可能地避免无谓的绘制操作。

- **阴影耗时：** 在浏览器渲染canvas的过程中，为了绘制阴影，浏览器需要将阴影先渲染到一个辅助的位图中，在将位图上的内容与屏幕Canvas进行图像合成。由于使用了这种辅助位图，所以绘制阴影可能是一件很耗时的操作。


> **16ms:** 大部分显示屏是60hz的刷新率，这是因为人眼与大脑之间的协作无法感知超过60fps的画面更新。12fps大概类似手动快速翻动书籍的帧率，这明显是可以感知到不够顺滑的。24fps使得人眼感知的是连续线性的运动，这其实是归功于运动模糊的 效果。24fps是电影胶圈通常使用的帧率，因为这个帧率已经足够支撑大部分电影画面需要表达的内容，同时能够最大的减少费用支出。但是低于30fps是无法顺畅表现绚丽的画面内容的，为了达到极致的动画效果，此时就需要用到60fps来达到想要的效果

## Canvas的浏览器渲染过程原理

在向Canvas上绘制图形或图像的时候，浏览器需要按照如下步骤进行操作：

**详细过程：**

- 将图形或图像绘制到一个无限大的透明位图中，在绘制时遵从当前的填充模式、描边模式以及线条样式。
- 将图形或图像的阴影绘制到另一幅位图中，在绘制时使用当前绘图环境的阴影设定。
- 将阴影中每一个像素的alpha分量乘以绘图环境对象的globalAlpha属性值。
- 将绘有阴影的位图与经过剪辑区域剪切过的canvas进行图像合成。在操作时使用当前的合成模式参数。
- 将图形或图像的每一个像素颜色分量，乘以绘图环境对象的globalAlpha属性值。
- 将绘有图形或图像的位图，合成到当前经过剪辑区域剪切过的canvas位图之上，在操作时使用当前的合成操作符（composition operator）。

只有在启动阴影效果时才会执行2-4步。

**简单理解：**

- 将图按照你规定的样式或模式绘制到一张图中（不包括透明度）
- 将阴影按照你规定的绘制样式到另一张图中（不包括透明度）
- 设置阴影透明度
- 将阴影区域和剪辑区域合成
- 设置图的透明度
- 将图和剪辑区域合成

## Canvas和SVG的区别

canvas和svg都是HTML5推荐使用的图形技术，Canvas基于像素，提供2D绘制函数，是一种HTML元素类型，依赖于HTML，只能通过脚本绘制图形；SVG为可伸缩矢量图形，提供一系列图形元素（Rect, Path, Circle, Line …，负责的图形就通过的属性进行解释），还有完整的动画，事件机制，本身就能独立使用，也可以嵌入到HTML中。

**适用场景：**

- Canvas提供的功能更原始，适合像素处理，动态渲染和大数据量绘制
- svg适合静态图片展示，高保真文档查看和打印的应用场景

**方案选型：**

- 对于canvas，通常随着屏幕尺寸的增加，需要绘制的像素点就增多，画布需要绘制更多的像素。
- 对于svg，随着屏幕上对象数量的增加，我们将其不断添加到dom中，则消耗更多的性能。

具体点选型，还是要依赖于现实和平台。

