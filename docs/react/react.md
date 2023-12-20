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

## 为什么JSX组件名要以大写字母开头？

React要知道要渲染的是组件还是HTML元素

## refs 的作用是什么？

Refs 是 React 提供给我们的安全访问 DOM 元素或者某个组件实例的句柄。

我们可以为元素添加ref属性然后在回调函数中接受该元素在 DOM 树中的句柄，该值会作为回调函数的第一个参数返回

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

## 高阶组件HOC和render props是什么？带来了什么问题

#### 高阶组件

高阶组件可以看作 React对装饰模式的一种实现，高阶组件是一个函数，接收一个组件，然后返回一个新的组件。

> 高阶组件（ HOC）是 React中的高级技术，用来重用组件逻辑。但高阶组件本身并不是 ReactAPI。它只是一种模式，这种模式是由 React自身的组合性质必然产生的。

**手写一个高阶组件：** HOC的简单应用，函数接收一个组件作为参数，并返回一个新组件，新组建可以接收一个 visible props，根据 visible的值来判断是否渲染Visible。

 ```javascript
// 手写一个高阶组件
 function visible(WrappedComponent) { 
	return class extends Component {
		render() {
			const { visible, ...props } = this.props; 
			if (visible === false) return null;
			return <WrappedComponent {...props} />;
		}
	}
}
 ```
 
 **高阶组件的优点：**
 
 - 不会影响内层组件的状态, 降低了耦合度
 
 **高阶组件的缺点：**
 
 - 相同的props会发生覆盖：如果一个已经封装好的高阶组件，在外用了name属性，如果被包裹组件也用了name属性，则会造成覆盖，增加了调试和代码修复的时间。
 - 被包裹组件的静态方法会消失：静态方法本来在组件的statics对象内，由于组件被包裹，返回的组件已经是新组件。如果需要保留，可以手动将原组件方法拷贝给新组件。
 - 高阶组件和 Render Props 一样，本质上都是将复用逻辑提升到父组件中，很容易产生很多包装组件，带来的「嵌套地域」。

#### render props

将一个组件内的 state 作为 props 传递给调用者, 调用者可以动态的决定如何渲染.

**缺点：**
- 无法在 return 语句外访问数据
- 导致嵌套地狱

## React hooks解决了什么问题

**发展由来，为什么要有React Hooks：**

React的核心是组件，在V16.8之前，组件的标准写法是类（class），而由类写成的组件，其实是相对较“重”的，随着React应用的负复杂层级不断叠加，就会更复杂。

缺点：

- 组件类引入了复杂的编程模式，比如 render props 和高阶组件。
- 大型组件难以拆分和重构，和难以测试
- 业务逻辑分散在各个方法中，导致重复逻辑或关联逻辑

React团队希望：**组件的最佳写法应该是函数，而不是类。**

虽然React早期就支持函数组件，但是必须是纯函数，不能包含状态，也不支持生命周期方法，所以不能取代类。

所以**React Hooks 的设计目的，就是加强版函数组件，完全不使用"类"，就能写出一个全功能的组件。**

## react hooks

React Hooks 的意思是，组件尽量写成纯函数，如果需要外部功能和副作用，就用钩子把外部代码"钩"进来。

|hook|说明|
|-|-|
|useState🏁|给组件添加内部state|
|useEffect🏁|相当于合并了componentDidMount、componentDidUpdate、componentWillUnmount，用于添加“副作用”；React 将按照 effect 声明的顺序依次调用组件中的每一个 effect；可根据第二个数组参数有选择调用，传入[]意味着只执行一次
|useContext🏁|不用组件嵌套就可以订阅React的Context|
|useReducer|通过reducer来管理本地复杂的state|
|useCallback|缓存函数的引用，把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。第二个参数依赖项(deps)是一个空数组它代表这个函数在组件的生成周期内会永久缓存。|
|useMemo|缓存计算数据的值|
|useLayoutEffect|和useEffect类似|

hook的规则：
- 只能在**函数最外层**调用hook，不要在循环、条件判断、子函数中调用
- 只能在**React函数组件**和**自定义hook**中调用hook

## Hooks写输入组件,实现change进行请求接口

```javascript
import React, { useEffect, useState } from 'react'

//url从外部传入
export default function Search(props) {
  //设置初始值
  const [value,setValue] = useState('')
  //value设置成当前改变后的值
  const setSearchText = e=>{
    setValue(e.target.value)
  }
  useEffect(()=>{
    //请求搜索接口
    axios.post(props.url,{value},{headers:{
        'content-type':'application/json'
      }}).
    then(function (response) {
      console.log(response);
    })
    .catch(function (error) {
      console.log(error);
    });
  })
  return <input value={value} onChange={setSearchText}/>
}
```

## useCallback和useMemo的区别

先说结论：useCallback和useMemo都可缓存函数的引用或值，但是从更细的使用角度来说useCallback缓存函数的引用，useMemo缓存计算数据的值。

useCallback:
父组件重新渲染，本来子组件也要全部重新渲染，可以通过 useCallback 保护一些函数不必重新渲染，以此优化性能。

useMemo:
缓存值的计算结果

**useCallback：**


假设一个累加器，我们对count使用useState，那么我们或许定一个handleCount来执行setCount(count+1)。实际上，每一次重新渲染组件时，handleCount都是新创建的函数，那么将handleCount作为props传递给子组件时，shouldComponentUpdate相关优化就会失效，因为每次都是不同的函数。而如果使用了useCallback，那么则得到的是一个缓存函数，只有依赖项发生改变时才会更新。

**useMemo：**

useMemo和useCallback很像,区别是

- useCallback是根据第二个参数依赖(deps)缓存第一个入参的(callback)，是回调函数的缓存版本。
- useMemo是根据依赖(deps)缓存第一个入参(callback)执行后的值，可用于密集型计算大的一些缓存。

**useCallback(fn, deps) 相当于 useMemo(() => fn, deps)。**

## useEffect和useLayoutEffect

useEffect 用来取代 componentDidMount 和 componentDidUpdate。主要作用是当页面渲染后，进行一些副作用操作（比如访问 DOM，请求数据）。

**区别：**

useLayoutEffect 的作用和 useEffect 几乎差不多，不过useLayoutEffect 可以使用它来读取 DOM 布局并同步触发重渲染。因为useLayoutEffect会在组件树构建完毕后同步立刻执行，而useEffect会等其他js代码执行完毕后执行（或者遇到下一次刷新任务前），也就是说等待 useLayoutEffect 内部状态修改后，才会去更新页面，因此可以通过这个机制，来解决一些特性场景下的页面闪烁问题，

