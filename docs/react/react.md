在前端面试中，很多大公司都是React技术栈，一份扎实的React基础必不可少，这里汇总常见的React问题和回答，以及作者亲身经历的React问题。

?> 避坑指南：本文已经尽力避免了“多说一些细节点”，只是在“听起来这个人基础还不错”的状态下完成回答，尽力避免给自己挖坑，以至于让面试官深挖，最后万劫不复。当然，如果你对自己有信心，可以尝试多说一些，只要能保证你补充的地方还能深挖，并且能答上来，那妥妥的加分项。但是如果从概率上考虑，可能数学期望为负数，你可要好好斟酌斟酌。

## 说一下React的声明周期

目前React的生命周期分为3个阶段: 挂载阶段（Mount）、更新阶段（Update）、卸载阶段（Unmount）

### 挂载阶段（Mount）：
- **constructor**:构造函数，最先被执行，通常在state里初始化state对象或者给自定义方法绑定this。
- **getDerivedStateFromProps(nextProps,prevState)**：将传入的props映射到state上面
- **render**：render渲染
- **componentDidMount**：这里可以发送服务器请求、获取DOM节点、对canvas进行操作。

### 更新阶段（Update）：
- **getDerivedStateFromProps**：新的props会传过来
- **shouldComponentUpdate(nextProps,nextState)**：返回一个布尔值，true表示触发重新渲染，false表示不会触发重新渲染，默认返回true
- **render**：更新阶段也会触发生命周期
- **getSnapshotBeforeUpdate(prevProps,prevState)**：返回值会传入componentDidUpdate作为第三个参数，必须和componentDidUpdate一起使用
- **componentDidUpdate(prevProps,prevState,snapshot)**：如果触发某些回调函数时需要用到DOM的状态（如滚动位置），则将对比和计算的过程写在getSnapshotBeforeUpdate，然后在componentDidUpdate里统一触发回调或更新状态

### 卸载阶段（Unmount）：

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


## React如何性能优化
## shouldComponentMount如何优化性能？
## React是如何优化性能的？
## 虚拟DOM是什么？
## 说一下diff算法
