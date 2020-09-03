
说句真心话，一切博文和专栏都不如红宝书《JavaScript高级程序设计》和犀牛书《JavaScript权威指南》，这两本书将会伴随你一生的前端生涯，**选一本**，**吃透它**，从此前端之路有条不紊！

## ES6新语法，包含es7、es8、es9更新的语法

- `let`声明变量，`const`声明常量
- 解构赋值
- 模版字符串、字符串被for…of遍历
- 字符串的正则
- Number的二进制前缀0b/0B和八进制前缀0o/0O
- 数组的map、forEach等便利方法，另外Array.from()可以将类似数组的对象和可以遍历的对象转化为数组；Array.of()将一组值转化为数组
- 函数参数设置默认值、箭头函数、扩展运算符(...)
- Object.assign()合并对象
- Symbol
- Promise
- Set：类似于数组，成员值唯一
- Map：键值对数据结构
- Proxy和Reflect。Proxy用于对编程语言编程，对外界对访问进行过滤和改写。Reflect是为了代替Object操作对象提供的新API，比如异常情况下Object.defineProperty会抛出错误，而Reflect.defineProperty会返回false
- Iterator和for...of循环
- Generator/yield用于异步编程
- async与await。async是Generator的语法糖，完全可以看作多个异步操作，包装成的一个Promise对象，而await命令就是内部then命令的语法糖。
- class
- 模块化(Module)，主要由export 和 import组成。
- 修饰器（Decorator）

## 箭头函数和普通函数的区别

1、没有自己的this、super、arguments和new.target绑定。
2、不能使用new来调用。
3、没有原型对象。
4、不可以改变this的绑定。
5、形参名称不能重复。

箭头函数中没有this绑定，必须通过查找作用域链来决定其值。

如果箭头函数被非箭头函数包含，则this绑定的是最近一层非箭头函数的this，否则this的值则被设置为全局对象。

## Promise.all和Promise.race
   
   Promise.all可以将多个Promise实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是一个结果数组，而失败的时候则返回最先被reject失败状态的值。
   
   Promse.race就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。
## 跨域原理，解决方案

[跨域参考链接](https://segmentfault.com/a/1190000011145364)

浏览器的“同源策略”会导致跨域，其中同源是指“协议、域名、端口”都相同。

跨域解决方案

1. **通过jsonp跨域**

jsonp原理：静态文件不受同源政策影响，我可以返回一个script里面有一个回调函数，函数的里面是我要的东西。

2. **document.domain + iframe跨域**

此方案仅限主域相同，子域不同的跨域应用场景。

实现原理：两个页面都通过js强制设置document.domain为基础主域，就实现了同域。

3. **location.hash + iframe**

实现原理： a欲与b跨域相互通信，通过中间页c来实现。 三个页面，不同域之间利用iframe的location.hash传值，相同域之间直接js访问来通信。

具体实现：A域：a.html -> B域：b.html -> A域：c.html，a与b不同域只能通过hash值单向通信，b与c也不同域也只能单向通信，但c与a同域，所以c可通过parent.parent访问a页面所有对象。

4. **window.name + iframe跨域**

window.name属性的独特之处：name值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值（2MB）。

通过iframe的src属性由外域转向本地域，跨域数据即由iframe的window.name从外域传递到本地域。这个就巧妙地绕过了浏览器的跨域访问限制，但同时它又是安全操作。

5. **postMessage跨域**

postMessage是HTML5 XMLHttpRequest Level 2中的API，且是为数不多可以跨域操作的window属性之一，它可用于解决以下方面的问题：


- 页面和其打开的新窗口的数据传递
- 多窗口之间消息传递
- 页面与嵌套的iframe消息传递
- 上面三个场景的跨域数据传递

用法：postMessage(data,origin)方法接受两个参数

data： html5规范支持任意基本类型或可复制的对象，但部分浏览器只支持字符串，所以传参时最好用JSON.stringify()序列化。

origin： 协议+主机+端口号，也可以设置为"*"，表示可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"。

6. **跨域资源共享（CORS）**

**为什么选择CORS？** 因为jsonp只支持get请求，但我们还有post请求，CORS就满足了我的要求。服务端设置响应头中的Access-Control-Allow-Origin为对应的域名。

7. **nginx代理跨域**

##### nginx反向代理接口跨域
 
浏览器跨域访问js、css、img等常规静态资源被同源策略许可，但iconfont字体文件(eot|otf|ttf|woff|svg)例外，此时可在nginx的静态资源服务器中加入以下配置。
 
 ```‌
 location / {
  add_header Access-Control-Allow-Origin *;
}
 ```
##### nginx配置解决iconfont跨域

跨域原理： 同源策略是浏览器的安全策略，不是HTTP协议的一部分。服务器端调用HTTP接口只是使用HTTP协议，不会执行JS脚本，不需要同源策略，也就不存在跨越问题。

实现思路：通过nginx配置一个代理服务器（域名与domain1相同，端口不同）做跳板机，反向代理访问domain2接口，并且可以顺便修改cookie中domain信息，方便当前域cookie写入，实现跨域登录。
 
8. **nodejs中间件代理跨域**

node中间件实现跨域代理，原理大致与nginx相同，都是通过启一个代理服务器，实现数据的转发，也可以通过设置cookieDomainRewrite参数修改响应头中cookie中域名，实现当前域的cookie写入，方便接口登录认证。

9. **WebSocket协议跨域**

WebSocket protocol是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很好的实现。

原生WebSocket API使用起来不太方便，我们使用Socket.io，它很好地封装了webSocket接口，提供了更简单、灵活的接口，也对不支持webSocket的浏览器提供了向下兼容。




## 原型链✨

绝大部分函数都有一个`prototype`属性，含义是**函数的原型对象**，所有被创建的对象都可以访问原型对象的属性和方法。在new的过程中，会将函数的prototype赋值给对象的`__proto__`属性，当访问一个对象的属性的时候，如果对象内部不存在这个属性，就从`__proto__`所指的父对象寻找，一直找到终点null，通过`__proto__`属性连接的这条链就是原型链。

> 原型链最顶端：`Object.prototype.__proto__=null`


## new一个对象发生了什么？

三步：

1. 创建以这个函数为原型的空对象；

2. 将函数的`prototype`赋值给对象的`__proto__`属性；

3. 将对象作为函数的this传进去，如果有return就返回return里面的内容，如果没有就创建这个对象。


## 基本类型
`String`、`Boolean`、`Number`、`Null`、`Undefined`、`Symbol`,还有一个还没有被正式纳入标准的`BigInt`。

`Symbol`可以用来创建唯一常量。

## 作用域
在ES5中，只有函数作用域和全局作用域。

在ES6中，let和const拥有了块级作用域{}。

## this的指向问题

this在函数里：函数的拥有者默认绑定this
例如在对象的方法，this指向对象

如果要判断一个运行中函数的 this 绑定， 就需要找到这个函数的直接调用位置。 找到之后
就可以顺序应用下面这四条规则来判断 this 的绑定对象。

1. new 调用：绑定到新创建的对象，注意：显示return函数或对象，返回值不是新创建的对象，而是显式返回的函数或对象。
2. call 或者 apply（ 或者 bind） 调用：严格模式下，绑定到指定的第一个参数。非严格模式下，null和undefined，指向全局对象（浏览器中是window），其余值指向被new Object()包装的对象。
3. 对象上的函数调用：绑定到那个对象。
4. 普通函数调用： 在严格模式下绑定到 undefined，否则绑定到全局对象。
ES6 中的箭头函数：不会使用上文的四条标准的绑定规则， 而是根据当前的词法作用域来决定this， 具体来说， 箭头函数会继承外层函数，调用的 this 绑定（ 无论 this 绑定到什么），没有外层函数，则是绑定到全局对象（浏览器中是window）。 这其实和 ES6 之前代码中的 self = this 机制一样。

DOM事件函数：一般指向绑定事件的DOM元素，但有些情况绑定到全局对象（比如IE6~IE8的attachEvent）。

## 解释下变量提升✨

JS引擎的工作方式是，先解析代码，获取所有被声明的变量，再一行一行运行，所有变量的声明语句会被提升带代码的头部，这叫做变量提升（hoisting）。

```javascript
console.log(a);//undefined
var a = 1;
```

代码的实际执行顺序是这样的：

```javascript
var a;
console.log(a);
a=1;
```
## 声明一个函数有几种方式，有什么区别

**函数式声明**

```
//ES5
funciton name(){}
function (){}//匿名函数
//ES6
()=>{}
```

**函数表达式（函数字面量）**

```
let fun = function(){}
let fun = ()=>{}
let fun = function name(){}

(function name(){})()
```

**Function构造**

let fun = new Function('arg1','arg2','alert(arg1+","+arg2)')


**区别**：

- 构造函数需要被解析两次，第一次解析js代码，第二次解析传入构造函数的字符串，影响性能。
- 基于变量提升和函数提升的机制，函数的声明优先级更高，会直接提升当前作用域的顶端，然后表达式和变量按顺序执行。

## 理解闭包吗？✨
?> 这个问题是在问：闭包是什么？闭包有什么作用？

闭包："有权访问另一个函数作用域中变量的函数"（来自红宝书）

闭包的最大作用是“隐藏变量”，一大特性是**内部函数总是可以访问外部函数中声明的参数和变量，甚至在其外部函数被返回（寿终正寝）之后**，比如可以利用闭包访问私有变量。

## 作用域链理解吗？

> 区别于原型链，作用域链是为了访问**变量**而存在的链，原型链是为了访问**对象的属性**而存在的链。

专业回答：JS在执行的过程中会创建**可执行上下文**，可执行上下文的词法环境中含有外部词法环境的引用，通过这个引用可以获取到外部词法环境的变量的声明，这些引用串联起来会一直指向全局的词法环境，因此形成了作用域链。

![作用域和执行上下文环境](https://s1.ax1x.com/2020/03/26/GpzpN9.jpg)
> 通俗理解：按照我们的常规思路，其实完全可以把“作用域”和“执行上下文环境”当作一个东西来理解，毕竟只是概念性的东西，不用过多深究。真实情况下“作用域”是在函数定义的时候存在的，而“执行上下文环境”是JS执行之前确定的，从内到外查找变量，找不到就向外层去找同名变量，这种作用域产生的“由内向外”的过程就是作用域链。

## Module模块化的意义

- 解决命名冲突，防止全局变量的污染
- 管理文件依赖
- 保护私有属性
- 易于代码的扩展和维护

## 前端模块化有几种

ES6（ES2017）之前，前端模块规范有三种： CommandJS、AMD和CMD

CommandJS用在服务端模块化规范，AMD和CMD用在浏览器模块化规范

ES6之后，就可以直接食用import和export来实现模块化


CommandJS：同步加载---NodeJS
AMD:提前执行（异步加载，回调执行）---RequireJS
CMD:延迟执行（运行时按需加载，顺序执行）---SeaJS


## ES6模块和CommonJS模块有什么区别？
CommonJS是对模块的浅拷贝，可以改变require引入的变量，是node广泛使用的模块化机制。

ES6模块是对模块的引用，通过import引入的是只读状态，不允许修改只读变量的值。

CommonJS模块是对象，运行时加载，ES6模块不是对象，编译时加载。

## 聊一聊BigInt
BigInt是没有正式发布(截止到2020年3月26日)的，但即将加入标准的基本类型，它是为了解决超过Number的最大安全数字就会出现计算不准确的情况。

声明方法是，"阿拉伯数字+n"，如`let b = 10n`;

> 不建议BigInt和Number进行转换，因为会损失精度。

## null和undefined有什么区别？
- null表示“没有对象”，即该处不应该有值
- undefined表示“缺少值”，此处应该有一个值，只是没有定义

## 轮询和长轮询

轮询：客户端定时向服务器发送请求，服务器接收到请求后马上返回响应信息并关闭连接。

长轮询：客户端向服务器发送请求，服务器接收到请求之后并不马上响应，等待资源更新时再响应并关闭连接。

轮询请求大半无用，浪费带宽，适用于中小项目。

长轮询看起来美好，但占用服务器资源，用于实时性比较高的项目，比如WebQQ。

## map、reduce、foreach、filter区别

- foreach按顺序传入每个值，不返回内容
- reduce，将每个值从第二个参数中获取，在第一个参数中保存
- filter过滤器，只返回符合条件的值
- map按顺序传入每个值，return的值改变当前值

filter和map是返回新数组，reduce返回结果值，foreach不返回

## Object.assign()是深拷贝还是浅拷贝

当对象中只有一级属性，没有二级属性的时候，此方法为深拷贝，但是对象中有对象的时候，此方法，在二级属性以后就是浅拷贝。

## ==和===和Obejct.is()的区别

三者都用于相等性比较：

- ==比较宽松，会自动转化数据类型，含义是“值相等”
- ===严格相等，要求值和数据类型都相等
- Object.is(v1,v2)是ES6新语法，传入两个被比较的元素，返回布尔值。类似于===，但有点区别。

```javascript

+0 === -0 //true
NaN === NaN // false
Object.is(+0, -0) // false
Object.is(NaN, NaN) // true

```

## js垃圾回收机制

JS的垃圾回收机制是为了以防内存泄漏。变量生命周期结束后会被释放内存，全局变量的生命周期持续到浏览器关闭页面，局部变量的生命周期在函数执行后就结束了。

> 内存泄漏的含义就是当已经不需要某块内存时这块内存还存在着，垃圾回收机制就是间歇的不定期的寻找到不再使用的变量，并释放掉它们所指向的内存。

js垃圾回收有两种方式：**标记清除**、**引用计数**

**标记清除**：

- 标记阶段：垃圾回收器从根对象开始遍历，每一个可以从根对象访问到的对象都会被添加一个可到达对象的标识。
- 清除阶段：垃圾回收器会对堆内存从头到尾进行线性遍历，如果有对象没有被标识为可到达对象，那么就将对应的内存回收，并清除可到达对象的标识，以便下次垃圾回收。

**引用计数**：

低版本的IE使用这种方式，但常常会引起内存泄露。原理是跟踪一个值的引用次数，当声明一个变量并将一个引用类型赋值给该变量时引用次数就是1，同一个值又被赋给另一个变量，引用次数+1，包含这个值当引用的变量取得新值，则引用次数-1，当垃圾回收器下次运行时，就会释放那些引用次数0的值占用的内存。

内存泄漏：循环引用指的是对象A中包含一个指向对象B的指针，而对象B中也包含一个指向对象A的引用。
```
function problem(){     
    var objectA = new Object();
    var objectB = new Object(); 
 
    objectA.someOtherObject = objectB;
    objectB.anotherObject = objectA; 
}
```

## js如何判断数据类型。如何判断数组
**typeof**(不可以)

typeof [] === "object"
typeof Null === "object"

**instanceof**

```
const a = [],b={};
console.log(a instanceof Array);//true
console.log(a instanceof Object);//true,在数组的原型链上也能找到Object构造函数
console.log(b instanceof Array);//false
```

**constructor**

```
const a = [];
console.log(a.constructor);//function Array(){ [native code] }
```

但不能保证constructor属性被改写的情况。

**Object.toString()和call/apply改写isArray**

除了对象之外，其他的数据类型的toString返回的都是内容的字符串，只有对象的toString方法会返回对象的类型，可以通过call/appy改变toString的执行上下文。

```
const isArray = (something)=>{
    return Object.prototype.toString.call(something) === '[object Array]';
}

cosnt a = [];
const b = {};
isArray(a);//true
isArray(b);//false
```

**Array.isArray()**最靠谱

```
const a = [];
const b = {};
Array.isArray(a);//true
Array.isArray(b);//false
```
