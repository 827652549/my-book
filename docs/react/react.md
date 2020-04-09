在前端面试中，很多大公司都是React技术栈，一份扎实的React基础必不可少，这里汇总常见的React问题和回答，以及作者亲身经历的React问题。

?> **避坑指南**：本文已经尽力避免了“多说一些细节点”，只是在“听起来这个人基础还不错”的状态下完成回答，尽力避免给自己挖坑，以至于让面试官深挖，最后万劫不复。当然，如果你对自己有信心，可以尝试多说一些，只要能保证你补充的地方还能深挖，并且能答上来，那妥妥的加分项。但是如果从概率上考虑，可能数学期望为负数，你可要好好斟酌斟酌。

## 什么是React/优点？
React是一个用于构建用户界面的js库。使用JSX语法，专注于view层，遵循组件设计模式、声明式编程范式、函数式编程概念，使前端应用更加高效。使用虚拟DOM来有效操作DOM，单向数据流流动，同时React有很好的生态，提供了丰富的解决方案。

## 说一下React的声明周期

目前React的生命周期分为3个阶段: 挂载阶段（Mount）、更新阶段（Update）、卸载阶段（Unmount）

#### 挂载阶段（Mount）：
- **constructor**:构造函数，最先被执行，通常在state里初始化state对象或者给自定义方法绑定this。
- **getDerivedStateFromProps(nextProps,prevState)**：将传入的props映射到state上面
- **render**：render渲染
- **componentDidMount**：这里可以发送服务器请求、获取DOM节点、对canvas进行操作。

#### 更新阶段（Update）：
- **getDerivedStateFromProps**：新的props会传过来
- **shouldComponentUpdate(nextProps,nextState)**：返回一个布尔值，true表示触发重新渲染，false表示不会触发重新渲染，默认返回true
- **render**：更新阶段也会触发生命周期
- **getSnapshotBeforeUpdate(prevProps,prevState)**：返回值会传入componentDidUpdate作为第三个参数，必须和componentDidUpdate一起使用
- **componentDidUpdate(prevProps,prevState,snapshot)**：如果触发某些回调函数时需要用到DOM的状态（如滚动位置），则将对比和计算的过程写在getSnapshotBeforeUpdate，然后在componentDidUpdate里统一触发回调或更新状态

#### 卸载阶段（Unmount）：

- **componentWillUnmount**：当组件卸载和销毁时调用，可以在这个函数里去清除一些定时器，取消网络请求，清除无效DOM等垃圾清理工作







<details>
<summary>点击展开React最新生命周期图</summary>
<pre>
<iframe  
 height=850 
 width=90% 
 src="http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/"  
 frameborder=0  
 allowfullscreen>
 </iframe>
>
</pre>
</details>

## React请求放到哪个生命周期中？
挂载阶段渲染之后的componentDidMount
## React的合成事件？
例如`<button onClick={this.handleButtonCLick()}></button>`
React并非是直接在真实DOM上绑定事件，为了避免DOM事件滥用，在document监听所有事件，当事件冒泡至document处时，React将事件内容交给中间层SyntheticEvent负责事件合成，当事件触发时，对统一分发函数dispatchEvent指定函数运行。

在React里也可以使用原生事件（addEventListener），但最好不要混用，一旦stopPropagation阻止了冒泡，会导致其他React事件失效。
## setState是同步的还是异步的？
- 生命周期函数、合成函数中是**异步**的
- 原生事件、setTimeout、setInterval是**同步**的。

这里的异步其实是指将多个state合成到一起进行批量更新。

原理：setState会根据一个变量isBatchingUpdates判断是直接更新this.state还是放到队列回头再说，在React调用合成事件之前会调用batchedUpdates()，通过修改变量isBatchingUpdates为ture，使之为异步。
## 为什么使用Redux？
（Redux是什么）
Redux是为了解决组件间通信和组件状态共享。

（Redux带来了什么好处）
在我的项目中，涉及到很多物理实验，不同实验还有很多复杂的数据，又考虑到页面深度和未来的易扩展性，因为未来还要有很多物理实验要添加进去，决定统一使用Redux进行状态管理。

（我是如何使用Redux的）
view的组件派发action（用dispatch方法）给Store，借助reducer里根据action的type判断执行哪种操作，最后将新State返回给store进而转给component
## Redux如何实现异步操作？
?> Action 发出以后，Reducer 立即算出 State，这叫做同步；Action 发出以后，过一段时间再执行 Reducer，这就是异步。

我使用的是redux-thunk，通过Redux里applyMiddlewares()来添加中间件redux-thunk，通过改造store.dispatch()可以让它接收一个函数参数（正常情况下参数只能是对象），这个函数是一个可以返回函数的Action Creator，在这里面进行异步操作。
## 你还知道其他的Redux异步处理方式吗？

- **redux-promise**改造dispatch方法接收Promise对象作为参数
- **redux-saga**通过把异步处理剥离出来单独执行，利用generator实现异步处理

## Redux和Mobx区别
- Redux数据保存在单一store中，Mobx数据保存在多个store中
- redux使用是不可变状态，使用的是纯函数，而mobx的状态是可变的
- redux提供时间回溯的工具，调试更加容易，mobx更多的是抽象和封装，调试比较困难

（使用场景）

Mobx更适合数据不复杂的应用，面对复杂度高的应用，难以调试，状态无法回溯，会力不从心。

Redux适合有回溯需求的项目，比如画板，有时候需要撤销功能，就算是更复杂的项目，统一store管理数据，还有方便的调试工具，至少开发者不会感到无从下手。

## React如何实现组件间通讯
- 父组件向子组件通讯：直接通过props属性
- 子组件向父组件通讯：通过props传入一个函数，子组件通过函数参数传递信息到父组件作用域中
- 兄弟组件通讯：先通过父组件，在到子组件
- 跨层级通信：可以使用Context，但是会是组件的**复用性**变差
- 借助全局状态管理工具：Redux、Mobx

## React如何性能优化？
针对于React的优化可考虑通过shouldComponentUpdate控制不必要的渲染
其他的可参考浏览器的优化

## 为什么虚拟DOM能够提高性能？
因为DOM操作是很消耗性能的，而虚拟DOM相当于在js和真实DOM中间加了一个缓存，通过diff算法避免没必要的DOM操作，从而提高性能。

## 说一下diff算法？
将树形结构按照层级分解，只比较同级元素，并给每一个单元添加key属性，方便比较。调用setState的时候，给需要重新渲染的位置标记脏值dirty，最后会检查所有标记dirty的component重新绘制

## React Class component和function component有什么区别？
function comconent是一种无状态组件，更好体验容器和表现分离，直接接收props作为参数，return返回代码片段，没有生命周期，**提倡使用**。

class component有生命周期，用state改变内部状态，可以利用shouldComponentUpdate优化性能

## react hooks

|hook|说明|
|-|-|
|useState🏁|给组件添加内部state|
|useEffect🏁|相当于合并了componentDidMount、componentDidUpdate、componentWillUnmount，用于添加“副作用”；React 将按照 effect 声明的顺序依次调用组件中的每一个 effect；可根据第二个数组参数有选择调用，传入[]意味着只执行一次
|useContext🏁|不用组件嵌套就可以订阅React的Context|
|useReducer|通过reducer来管理本地复杂的state|

hook的规则：
- 只能在**函数最外层**调用hook，不要在循环、条件判断、子函数中调用
- 只能在**React函数组件**和**自定义hook**中调用hook
