# Vue实现数据双向绑定的原理：Object.defineProperty（）
 
 vue实现数据双向绑定主要是：采用数据劫持结合**发布者-订阅者模式**的方式，通过Object.defineProperty（）来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应监听回调。当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 Object.defineProperty 将它们转为 getter/setter。用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。
 
 > Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。
 
 
 vue的数据双向绑定 将MVVM作为数据绑定的入口，整合Observer，Compile和Watcher三者，通过Observer来监听自己的model的数据变化，通过Compile来解析编译模板指令（vue中是用来解析 {{}}），最终利用watcher搭起observer和Compile之间的通信桥梁，达到数据变化 —>视图更新；视图交互变化（input）—>数据model变更双向绑定效果。
 
 **js实现简单的双向绑定：**
 
 ```html
 <body>
     <div id="app">
     <input type="text" id="txt">
     <p id="show"></p>
 </div>
 </body>
 <script type="text/javascript">
     var obj = {}
     Object.defineProperty(obj, 'txt', {
         get: function () {
             return obj
         },
         set: function (newValue) {
             document.getElementById('txt').value = newValue
             document.getElementById('show').innerHTML = newValue
         }
     })
     document.addEventListener('keyup', function (e) {
         obj.txt = e.target.value
     })
 </script>
 ```
# Object.defineProperty() 除get/set方法外，其他属性方法（属性描述）
  
  - get
  - set
  - value：该属性对应的值
  - writable：值为 true 时value才能被赋值改变
  - configurable：值为 true 时，该属性的描述符才能够被改变
  - enumerable：值为 true 时，该属性才会出现在对象的枚举属性中
  
# vue组件通讯方式

#### props和$emit

数据通过props从父组件流向子组件，子组件通过$emit触发父组件的对应事件，通过传递参数的形式拿到子组件的数据

#### $attrs和$listeners

$attrs可以收集父作用域中传过来的属性，并过滤掉被props中定义的部分。（通过v-bind="$attrs"传入内部组件）

类似的，$listeners包含了父作用域中的事件监听器，通过v-on="$listeners"传入内部组件

#### 中央事件总线EventBus

实例化新的vue实例，作为中央事件总线EventBus，添加到Vue的prototype上，供所有组件访问。通过bus.$emit触发事件，通过bus.$on监听事件，最后通过回调函数进行逻辑处理。

可用于任意组件之间的通讯，适合小型的项目，如果项目过大还是推荐使用vuex

#### provide和inject

类似于React的Context API，父组件通过provide以对象的形式向子组件暴露一些属性，子组件通过inject注入相应属性。

?> provide 和 inject 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。

#### v-model

给组件添加v-model属性时，默认会把value作为组件的属性，把input作为给组件绑定事件时的事件名.

#### $parent 和 $children

直接操作父子组件的实例。$parent 就是父组件的实例对象，而 $children 就是当前实例的直接子组件实例了，不过这个属性值是数组类型的，且并不保证顺序，也不是响应式的。

#### $boradcast 和 $dispatch

> 不过只是在 Vue1.0 中提供了，而 Vue2.0 被废弃了，但是还是有很多开源软件都自己封装了这种组件通信的方式，比如 Mint UI、Element UI 和 iView 等

#### Vuex 状态管理

类似redux

# vue生命周期

#### beforeCreate

创建一个Vue实例，此时实例上只有一些生命周期函数和默认的事件
此时data computed watch methods上的方法和数据均不能访问

#### created

可以对data数据进行操作，可进行一些请求，请求不易过多，避免白屏时间太长。

#### beforeMount

判断el的挂载方式
判断是否有template设置
将template进行渲染保存到内存当中，还未挂载在页面上

#### mounted

将内存中的模版挂载到页面上
此时可以操作页面上的DOM节点
此时组件从创建阶段进入运行阶段

#### beforeUpdate

页面显示的数据是旧的，此时data里面的数据是最新，页面数据和data数据暂未同步

#### updated

根据data里的最新数据渲染出最新的DOM树，然后将最新的DOM挂载到页面
此时data和页面数据一致，都是最新的

#### beforeDestroy

此时组件从运行阶段进入到销毁阶段
组件上的data和methods以及过滤器等都出于可用状态，销毁还未执行

#### destroyed

组件已经被完全销毁，组件中所有的数据、方法、指令、过滤器等，都已不可用

---
#### activated
在加载对应模块时会被调用，页面第一次加载时也会被调用

#### deactivated
组件切换出去时被调用

# 组件生命周期调用顺序

组件的调用顺序都是先父后子,渲染完成的顺序是先子后父。

组件的销毁操作是先父后子，销毁完成的顺序是先子后父。

加载渲染过程
父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount- >子mounted->父mounted

子组件更新过程
父beforeUpdate->子beforeUpdate->子updated->父updated

父组件更新过程
父 beforeUpdate -> 父 updated

销毁过程
父beforeDestroy->子beforeDestroy->子destroyed->父destroyed

# computed与watch

#### computed：
computed是计算属性，依赖其他的属性值，并且computed的属性值有缓存属性，当属性值变化的时候，下一次获取computed属性的时候才会重新计算computed的值。

#### watch:
更多的是一种观察的作用，用于监听某些数据的回调。每当所监听的数据发生变化时才能执行回调处理后续操作。

# 侦听属性watch如何监听一个对象内部的变化。

- 如果只是监听obj内的某一个属性变化，可以直接obj.key进行监听。
- 如果对整个obj深层监听，将deep设为true

# 侦听属性watch的immediate的作用

我们在watch中的默认写的方法就是handler方法。

当值第一次进行绑定的时候并不会触发watch监听，使用immediate：true则可以在最初绑定的时候执行handler。
# 计算属性computed以及里面的getter和setter

computed是计算属性，依赖其他的属性值，并且计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才才会重新求值。

getter作为默认值常常省略不写，所依赖的变量发生变化时相应地改变自身的值（这里依赖的变量一定要在模版中引用）

当前变量直接被设置成其他值时会触发setter，新值会作为参数传到set中，然后再进行逻辑处理（`姓名`分为`姓`和`名`重新赋值到data中）。

官方例子：
```JSX
<template>
    <div id="demo">
         <p> {{ fullName }} </p>
         <input type="text" v-model="fullName">
         <input type="text" v-model="firstName">
         <input type="text" v-model="lastName">
    </div>
</template>

var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'zhang',
    lastName: 'san'
  },
  computed: {
    fullName: {
      //getter 方法
        get(){
            console.log('computed getter...')
            return this.firstName + ' ' + this.lastName
        }，
   //setter 方法
        set(newValue){
            console.log('computed setter...')
            var names = newValue.split(' ')
            this.firstName = names[0]
            this.lastName = names[names.length - 1]
            return this.firstName + ' ' + this.lastName
        }
      
    }
  },
  updated () {
     console.log('updated')
  }
})
```


1. 如果一个数据依赖于其他数据，那么把这个数据设计为computed的,因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
2. 当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 (访问一个API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

# 接口请求一般放在哪个生命周期中

接口请求一般放在mounted中，但需要注意的是服务端渲染时不支持mounted，需要放到created中。

# v-if和v-show

当条件不成立时，v-if不会渲染DOM元素，v-show操作的是样式(display)，切换当前DOM的显示和隐藏。

# 组件中的data为什么是一个函数

一个组件被复用多次的话，也就会创建多个实例。本质上，这些实例用的都是同一个构造函数。如果data是对象的话，对象属于引用类型，会影响到所有的实例。所以为了保证组件不同的实例之间data不冲突，data必须是一个函数。

# v-model的原理

v-model本质就是一个语法糖，可以看成是value + input方法的语法糖。
可以通过model属性的prop和event属性来进行自定义。原生的v-model，会根据标签的不同生成不同的事件和属性。

# Vue事件绑定原理说一下

原生事件绑定是通过addEventListener绑定给真实元素的，组件事件绑定是通过Vue自定义的$on实现的。

# Vue模版编译原理知道吗

简单说，Vue的编译过程就是将template转化为render函数的过程。会经历以下阶段：

- 生成AST树
- 优化
- codegen


首先解析模版，生成AST语法树(一种用JavaScript对象的形式来描述整个模板)。
使用大量的正则表达式对模板进行解析，遇到标签、文本的时候都会执行对应的钩子进行相关处理。

Vue的数据是响应式的，但其实模板中并不是所有的数据都是响应式的。有一些数据首次渲染后就不会再变化，对应的DOM也不会变化。那么优化过程就是深度遍历AST树，按照相关条件对树节点进行标记。这些被标记的节点(静态节点)我们就可以跳过对它们的比对，对运行时的模板起到很大的优化作用。

编译的最后一步是将优化后的AST树转换为可执行的代码。

# 为什么v-for要加key

最大化利用dom节点，提升性能。diff算法默认使用“就地复用”的策略，是一个首尾交叉对比的过程。

在vue的源码中，有个sameVnode()函数，用于判断两个节点是否为同一节点（也就是是否可复用），他会先判断key和tag等是否相同，如果不加key，key都是undefined，默认相同，就会就地复用相同类型的元素，效率很低，并且有可能会因为数据和UI不一致带来一些问题。如果添加了key属性，它会基于key的变化重新排列元素顺序，并且会移除 key 不存在的元素。

举个例子：

有一个列表我们现在在中间插入了一个元素，diff算法会默认复用之前的列表并在最后追加一个，如果列表存在选中一类的状态则会随着复用出现绑定错误的情况而不是跟着原元素，key的作用就可以给他一个标识，让状态跟着数据渲染。

要注意：有相同父元素的子元素必须有独特的 key。重复的 key 会造成渲染错误。

# 为什么不建议用index作为key

**index作为key，其实就等于不加key：**虽然加上了key，但key是就是当前的顺序索引值，此时sameNode(A,B)依然是为true，所以与不加key时一样。

index作为key，只适用于不依赖子组件状态或临时 DOM 状态 的列表渲染输出。(比如表单输入值) 

# Vue2.x和Vue3.x渲染器的diff算法分别说一下
简单来说，diff算法有以下过程

- 同级比较，再比较子节点
- 先判断一方有子节点一方没有子节点的情况(如果新的children没有子节点，将旧的子节点移除)
- 比较都有子节点的情况(核心diff)
- 递归比较子节点

正常Diff两个树的时间复杂度是O(n^3)，但实际情况下我们很少会进行跨层级的移动DOM，所以Vue将Diff进行了优化，从O(n^3) -> O(n)，只有当新旧children都为多个子节点时才需要用核心的Diff算法进行同层级比较。

Vue2的核心Diff算法采用了双端比较的算法，同时从新旧children的两端开始进行比较，借助key值找到可复用的节点，再进行相关操作。相比React的Diff算法，同样情况下可以减少移动节点次数，减少不必要的性能损耗，更加的优雅。

Vue3.x借鉴了
ivi算法和 inferno算法

在创建VNode时就确定其类型，以及在 mount/patch 的过程中采用位运算来判断一个VNode的类型，在这个基础之上再配合核心的Diff算法，使得性能上较Vue2.x有了提升。

该算法中还运用了动态规划的思想求解最长递归子序列。

# hash路由和history路由实现原理说一下

hash模式：在浏览器中符号“#”，#以及#后面的字符称之为hash，用window.location.hash读取，可以使用`hashchange`监听hash事件变更；hash虽然在URL中，但不被包括在HTTP请求中；用来指导浏览器动作，对服务端安全，hash不会重新加载页面。

history模式：history采用HTML5的新特性；且提供了两个新方法：pushState（），replaceState（）可以对浏览器历史记录栈进行修改，以及popState事件的监听到状态变更。

# Vue2.x响应式数据原理

Vue在初始化数据时，会使用Object.defineProperty重新定义data中的所有属性，当页面使用对应属性时，首先会进行依赖收集(收集当前组件的watcher)如果属性发生变化会通知相关依赖进行更新操作(发布订阅)。

# Vue3.x响应式数据原理

Vue3.x改用Proxy替代Object.defineProperty。因为Proxy可以直接监听对象和数组的变化，并且有多达13种拦截方法。并且作为新标准将受到浏览器厂商重点持续的性能优化。

但是，Proxy只会代理对象的第一层，Vue3判断当前Reflect.get的返回值是否为Object，如果是则再通过reactive方法做代理，
这样就实现了深度观测。

有时候，监测数组的时候可能触发多次get/set，为了防止触发多次，
我们可以判断key是否为当前被代理对象target自身属性，也可以判断旧值与新值是否相等，只有满足以上两个条件之一时，才有可能执行trigger。
# vue2.x中如何监测数组变化

使用了函数劫持的方式，重写了数组的方法，Vue将data中的数组进行了原型链重写，指向了自己定义的数组原型方法。这样当调用数组api时，可以通知依赖更新。如果数组中包含着引用类型，会对数组中的引用类型再次递归遍历进行监控。这样就实现了监测数组变化。

# nextTick知道吗，实现原理是什么？

在下次 DOM 更新循环结束之后执行延迟回调。nextTick主要使用了宏任务和微任务。根据执行环境分别尝试采用

- Promise
- MutationObserver
- setImmediate
- 如果以上都不行则采用setTimeout

定义了一个异步方法，多次调用nextTick会将方法存入队列中，通过这个异步方法清空当前队列。
# 那你能讲一讲MVVM吗

MVVM是Model-View-ViewModel缩写，也就是把MVC中的Controller演变成ViewModel。Model层代表数据模型，View代表UI组件，ViewModel是View和Model层的桥梁，数据会绑定到viewModel层并自动将数据渲染到页面中，视图变化的时候会通知viewModel层更新数据。

# keep-alive了解吗

keep-alive可以实现组件缓存，当组件切换时不会对当前组件进行卸载。

常用的两个属性include/exclude，允许组件有条件的进行缓存。

两个生命周期activated/deactivated，用来得知当前组件是否处于活跃状态。

keep-alive的中还运用了LRU(Least Recently Used)算法。
# SSR服务器端渲染了解吗？

SSR也就是服务端渲染，也就是将Vue在客户端把标签渲染成HTML的工作放在服务端完成，然后再把html直接返回给客户端。

SSR有着更好的SEO、并且首屏加载速度更快等优点。不过它也有一些缺点，比如我们的开发条件会受到限制，服务器端渲染只支持beforeCreate和created两个钩子，当我们需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于Node.js的运行环境。还有就是服务器会有更大的负载需求。

# vue2.X和vue3的区别/vue3新特性

- 监测机制的改变

	- 3.0 将带来基于代理 Proxy的 observer 实现，提供全语言覆盖的反应性跟踪。
	- 消除了 Vue 2 当中基于 Object.defineProperty 的实现所存在的很多限制：
- 只能监测属性，不能监测对象

	- 检测属性的添加和删除；
	- 检测数组索引和长度的变更；
	- 支持 Map、Set、WeakMap 和 WeakSet。
- 模板

	- 模板方面没有大的变更，只改了作用域插槽，2.x 的机制导致作用域插槽变了，父组件会重新渲染，而 3.0 把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能。
	- 同时，对于 render 函数的方面，vue3.0 也会进行一系列更改来方便习惯直接使用 api 来生成 vdom 。
- 对象式的组件声明方式

	 - vue2.x 中的组件是通过声明的方式传入一系列 option，和 TypeScript 的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦。
	 - 3.0 修改了组件的声明方式，改成了类式的写法，这样使得和 TypeScript 的结合变得很容易
 - 其它方面的更改

	- 支持自定义渲染器，从而使得 weex 可以通过自定义渲染器的方式来扩展，而不是直接 fork 源码来改的方式。
	- 支持 Fragment（多个根节点）和 Protal（在 dom 其他部分渲染组建内容）组件，针对一些特殊的场景做了处理。
	- 基于 tree shaking 优化，提供了更多的内置功能。

# vue和react区别

- 同

	- 使用 Virtual DOM
	- 提供了响应式 (Reactive) 和组件化 (Composable) 的视图组件。
	- 将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库。
- 异

	- 在 React 应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树（除非使用PureComponent/shouldComponentUpdate），在 Vue 应用中，组件的依赖是在渲染过程中自动追踪的，所以系统能精确知晓哪个组件确实需要被重渲染
	- 在 React 中，一切都是 JavaScript。不仅仅是 HTML 可以用 JSX 来表达，现在的潮流也越来越多地将 CSS 也纳入到 JavaScript 中来处理
	- Vue 的路由库和状态管理库都是由官方维护支持且与核心库同步更新的。React 则是选择把这些问题交给社区维护，因此创建了一个更分散的生态系统，所以有更丰富的生态系统
	- Vue 提供了CLI 脚手架，能让你通过交互式的脚手架引导非常容易地构建项目。你甚至可以使用它快速开发组件的原型。React 在这方面也提供了create-react-app，但是现在还存在一些局限性
	- React Native 能使你用相同的组件模型编写有本地渲染能力的 APP，Vue 和Weex会进行官方合作，Weex 是阿里巴巴发起的跨平台用户界面开发框架，同时也正在 Apache 基金会进行项目孵化，另一个选择是NativeScript-Vue，一个用 Vue.js 构建完全原生应用的NativeScript插件

# 路由懒加载/按需加载

结合 Vue 的异步组件和 Webpack 的代码分割功能，轻松实现路由组件的懒加载。

异步组件定义为返回一个 Promise 的工厂函数，在Webpack中，我们可以使用动态import返回Promise,简化之后的写法就是

```
const Foo = () => import('./Foo.vue')
```

# 组件懒加载/按需加载
 
 webpack 中提供了 require.ensure()来实现按需加载。

# vue.use()做了什么
官网给出的解释是:通过全局方法 Vue.use() 使用插件，

- 如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。
- 通过全局方法 Vue.use() 使用插件
- 该方法需要在调用 new Vue() 之前被调用。
- 当 install 方法被同一个插件多次调用，插件将只会被安装一次。

**Vue.use()有什么用：**

在 install 里我们可以拿到 Vue 那么和 Vue 相关的周边工作都可以考虑放在 Vue.use() 方法里，比如：

- directive注册
- mixin注册
- filters注册
- components注册
- prototype挂载
- ……
