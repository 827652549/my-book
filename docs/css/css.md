在面试中，CSS也是个经常问到的部分，一般都是从"当前问题"=>"解决方案"，比如给你说"一个div里有两个指定宽高的浮动元素，它在页面中如何表现的，有什么解决方案？"你可能会想到clear来清除浮动，如果你没说清clear的用法或者说错了，下个问题就可能问你"clear是清除本元素还是相邻元素"

CSS不像http一样把知识点逐个击破就能串联起来，它更多地是你的一个上手经验，所以不要手懒，多尝试，多总结。

## clear清除的是相邻元素？
clear清除的是相邻元素,其实更应该说是“让左/右边不允许出现其他浮动元素”。

## BFC
块级格式化上下文，相当于将元素内部与外部独立开来。

常用创建bfc：

- 根元素
- display：flex
- float不是node
- position不是static或relative
- overflow不是visible

bfc作用：
- 防止margin重叠
- 防止元素塌陷
- 创建两栏布局

## 选择器优先级

内联(A) > ID选择器(B) > 类/属性/伪类选择器(C) > 标签选择器/伪元素(D)

先比较A的出现的次数，在比较B出现的次数……

## link和@import

- link是XHTML标签、@import是CSS提供的
- link可被dom获取到，@import不可以
- 页面加载时，link会被同时加载，@import会在页面加载完再加载

## 怎么隐藏元素，有什么区别？

- `display：none` 不占空间
- `opacity:0`全透明效果
- `visibility：hidden`同上
- `overflow:hidden`隐藏溢出部分
- `z-index:-9999`置于底层
- `transform:scale(0,0)`缩放元素到0大小，依然占据空间

## em/rem/px/vw/vh/vmax/vmin区别？

px是绝对大小，em和rem是相对大小：rem基于根元素(html)的font-size、em基于父元素（一般1em=16px）

vw、vh、vmax、vmin都是基于视口，1vw是视口宽度的百分之一，1vh是视口高度的百分之一，1vmax、1vmin取视口宽高最大或最小的百分之一

## 让一行文字中的图片垂直居中对齐

图片vertical-align：middle

## 实现垂直居中的方式

- 文字垂直居中，line-height等于容器height
- 父容器设相等上下内边距，高度auto
- 父容器display：table、子元素display：table-cell、vertical-align：middle
- 子元素realtive相对定位，top:50%,再利用translateY(-50%)向上偏移50的子元素高度
- 利用flex布局，将align-items:center或主轴竖向并利用justify-content：center主轴方向居中
## 实现水平居中的方式

- 通过子元素外边距margin：0 auto；
- 父容器text-align：center，子元素设成行内块
- 子元素相对定位，left：50%，反向偏移transform:translateX(-50%);
- flex布局，justify-content: center

<details>
<summary>点击展开水平居中和垂直居中Demo</summary>
<pre>
<iframe  
 width=90% 
 src="https://827652549.github.io/exercise/HTML/30.html"  
 frameborder=0  
 allowfullscreen>
 </iframe>
>
</pre>
</details>


## padding、margin参数含义

- padding:10px;//上、下、左、右都是10px
- padding:10px 20px;//上下10px、左右20px
- padding:10px 20px 30px;//上10px、左、右20px、下30px
- padding：10px 20px 30px 40px;//上10px、右20px、下30px、左40px（顺时针）

## Flex布局

**父容器：**

`flex-direction`:row | row-reverse | column | column-reverse

> 定义主轴方向

`flex-wrap`:nowrap | wrap | wrap-reverse

>定义缠绕方式，即下一行如何换行


`flex-flow`:\<flew-direction\> || \<flex-wrap\>

> 复合属性，flew-direction || flex-wrap

`justify-content`:flex-start｜flex-end｜center ｜space-between｜space-around
>定义主轴上的对齐方式

`align-items`:flex-start｜flex-end｜center｜baseline｜stretch
>定义交叉轴上的对齐方式

`align-content`:flex-start｜flex-end｜center ｜space-between｜space-around
> 在wrap换行后，即多根轴线的对齐方式

** 子元素：**

`order`:数字表示排列顺序。数字越小，越靠前。
`flex-grow`:数字计算占比，基于剩余空间放大元素。默认为0。
`flex-shrink`:数字计算占比，基于不足空间缩小元素。默认为1。
`flex-basis`:初始的尺寸。相当于width/height，默认auto
`flex`:\<flex-grow\> | \<flex-shrink\> | \<flex-basis\>
>复合属性，默认为“0 1 auto”

## CSS定位方式

- static：默认值。正常流
- realtive：相对定位，相对于正常位置
- absolute：基于最近的非static进行偏移
- fixed：基于视口位置
- sticky：粘性定位

## 怎么理解z-index

z-index控制重叠元素的叠加顺序，默认为0，值大的在上层，小的在下层。z-index只能影响设置了position值的元素

## 盒模型

盒模型由content(内容)、padding（内边距）、border（边框）、margin（外边距）。

默认情况下，我们定义的width为content的值，即box-sizing：content-box；如果将box-sizing：border-box，此时宽高包括content、padding、border

## 为什么使用translate改变位置而不是定位？

translate()是transform的一个值，改变translate或opacity不会触发重流（reflow）和重绘（repaint），只会触发复合（compositions）。而绝对定位会触发。

## 实现小于12px的字体

chrome最小值支持字体是12px，不论设多小都显示的是12px，如果设为0，则直接不占空间。

可以使用transform:scale(0.5);display:inline-block;

另外也可以做到图片或canvas上，但是可维护性不是多好。

## 动画（transition/animation）区别

**区别：**
transition强调过渡，需要触发一个事件，比如鼠标移入、点击等。

animation设置多个关键帧，实现自由动画，不需要触发任何事件也可实现动画效果；而且@keyframe控制当前帧属性的样式，创建由当前样式逐渐改为新样式的动画，更灵活。

Canvas实现动画，性能更高。

## 非行内style中dom.style.top获取为空

使用`offsetLeft`、`offsetTop`来替代style.x,style.y

```html
<style>
#id{
	position:absolute;
	left:200px;
	top:300px;
}
</style>
<div id="red"></div>
<script>
document.getElementById('red').style.left;//''
document.getElementById('red').style.top;//''

document.getElementById('red').offsetLeft;//200
document.getElementById('red').offsetTop;//300

</script>
```

