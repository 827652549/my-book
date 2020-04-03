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

## em/rem/px区别？

px是绝对大小，em和rem是相对大小：rem基于根元素、em基于父元素（一般1em=16px）

## 实现垂直居中的方式

- 文字垂直居中，line-height等于容器height

## 实现水平居中的方式
## padding、margin参数含义

- padding:10px;//上、下、左、右都是10px
- padding:10px 20px;//上下10px、左右20px
- padding:10px 20px 30px;//上10px、左、右20px、下30px
- padding：10px 20px 30px 40px;//上10px、右20px、下30px、左40px（顺时针）




