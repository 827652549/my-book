
说句真心话，一切博文和专栏都不如红宝书《JavaScript高级程序设计》和犀牛书《JavaScript权威指南》，这两本书将会伴随你一生的前端生涯，**选一本**，**吃透它**，从此前端之路有条不紊！

## ES6新语法，包含es7、es8、es9更新的语法

- `let`声明变量，`const`声明常量，let声明的变量在未声明前不允许使用，即“暂时性死区”。
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
   
   Promise.race就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。

   race场景: 给异步请求设定一个超时时间.
   
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

- （必须）Access-Control-Allow-Origin: *
- （可选）Access-Control-Allow-Credentials: true//表示允许是否发送cookie，默认情况下cookie不包含cros请求中
- （可选）Access-Control-Expose-Headers: name//携带上制定响应头的字段

如果CORS请求了非简单请求，还会带来进行option的问题

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

## 简单请求和非简单请求，Option请求

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

- 请求方法是以下三种方法之一：
	- HEAD
	- GET
	- POST
- HTTP的头信息不超出以下几种字段：
	- Accept//客户端或代理能够处理的媒体类型
	- Accept-Language//优先的语言（中文、英文……）
	- Content-Language//	实体主体的自然语言
	- Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain

凡是不同时满足上面两个条件，就属于非简单请求。

- 对于简单请求，在头信息里加一个Origin字段（协议 + 域名 + 端口），直接请求，
- 对于非简单请求，需要先用options请求“预检”，

预检的请求头里有两个字段：
- Access-Control-Request-Method//列出浏览器的CORS请求会用到哪些HTTP方法
- Access-Control-Request-Headers//浏览器CORS请求会额外发送的头信息字段

服务器收到"预检"请求以后，检查了Origin、Access-Control-Request-Method和Access-Control-Request-Headers字段以后，确认允许跨源请求，就可以做出回应。如果服务器否定了"预检"请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，被XMLHttpRequest对象的onerror回调函数捕获。


## 原型链✨

绝大部分函数都有一个`prototype`属性，含义是**函数的原型对象**，所有被创建的对象都可以访问原型对象的属性和方法。在new的过程中，会将函数的prototype赋值给对象的`__proto__`属性，当访问一个对象的属性的时候，如果对象内部不存在这个属性，就从`__proto__`所指的父对象寻找，一直找到终点null，通过`__proto__`属性连接的这条链就是原型链。

> 原型链最顶端：`Object.prototype.__proto__=null`


## new一个对象发生了什么？

三步：

1. 创建以这个函数为原型的空对象；

2. 将函数的`prototype`赋值给对象的`__proto__`属性；

3. 将对象作为函数的this传进去，如果有return就返回return里面的内容，如果没有就创建这个对象。

## 基本类型和引用类型

**基本类型：**

`String`、`Boolean`、`Number`、`Null`、`Undefined`、`Symbol`、`BigInt`。

`Symbol`可以用来创建唯一常量。

- 基本类型的值不能被方法来改变，只能通过赋值的方式来改变，所谓“赋值”就是“指针指向的改变”，
- 基本数据类型的值不能添加方法和属性
- 一个变量向另一个变量赋值，是会在变量对象上创建新值，把新值复制到新变量分配到位置上，两个变量独立且不互相影响
- 基本数据类型的比较是值的比较
- 基本数据类型是存放在栈区的，变量的标识符和变量的值

**引用类型：**

引用类型统称为Object 类型，细分的话包括Object 类型、Array 类型、Date 类型、RegExp 类型、Function 类型 

- 引用类型的值是可以改变的
- 引用类型可以添加属性和方法
- 引用类型的值是按照引用访问的，当从一个变量向另一个变量赋值引用类型的值时，同样也会将储存在变量中的对象的值复制一份放到为新变量分配的空间中.引用类型保存在变量中的是对象在堆内存中的地址，所以，与基本数据类型的简单赋值不同，这个值的副本实际上是一个指针，而这个指针指向存储在堆内存的一个对象.那么赋值操作后，两个变量都保存了同一个对象地址，而这两个地址指向了同一个对象.因此，改变其中任何一个变量，都会互相影响
- 引用类型的比较是引用的比较
- 引用类型的值是同时保存在栈区和堆区中的

**补充：基本包装类型**

字符串是基本类型，按理说不该有方法，但我们可以调用split()等等方法，主要是因为ECMAScript还提供了三个特殊的引用类型**Boolean**,**String**,**Number**.我们称这三个特殊的引用类型为**基本包装类型**，也叫**包装对象**.

也就是说当读取string,boolean和number这三个基本数据类型的时候，后台就会创建一个对应的基本包装类型对象，从而让我们能够调用一些方法来操作这些数据，最后再销毁基本包装类型的实例。正因为有这个销毁的动作，所以基本数据类型不可以添加属性和方法，

**生存周期**：
对象的生存期，使用new操作符创建的引用类型的实例，在执行流离开当前作用域之前都是一直保存在内存中；而自动创建的基本包装类型的对象，则只存在于一行代码的执行瞬间，然后立即被销毁

## 装箱和拆箱

**装箱：**
基本数据类型转换为对应的引用类型的操作

- **隐式装箱：** 在基本数据类型调用一些方法时，后台会创建对应的基本包装类型实例来操作，最后再销毁，这个过程就是个装箱。
- **显式装箱：** 当我们显式得把字符串传入new String()时就是显式装箱

**拆箱：**

拆箱就和装箱相反，是指把引用类型转换成基本的数据类型，JS标准规定了ToPrimitive用于拆箱转换。

## ToPrimitive

ToPrimitive 算法在执行时，会被传递一个参数 hint，表示这是一个什么类型的运算（也可以叫运算的期望值），根据这个 hint 参数，ToPrimitive 算法来决定内部的执行逻辑。

- ToPrimitive如果传入的hint为基本数据类型，则直接返回；Number先调用valueOf()，String先调用toString()。
- 若两属性均无，或没有返回基本类型，则会产生类型错误 TypeError: Cannot convert object to primitive value

## 作用域
在ES5中，只有函数作用域和全局作用域。

在ES6中，let和const拥有了块级作用域{}。

## 事件循环、宏任务和微任务

浏览器端事件循环中的异步队列有两种：macro（宏任务）队列和 micro（微任务）队列。

- 常见的 macro-task 比如：setTimeout、setInterval、script（整体代码）、 I/O 操作、UI 渲染等。
- 常见的 micro-task 比如: new Promise().then(回调)、MutationObserver(html5新特性，提供了监视对DOM树所做更改的能力) 等。

当某个宏任务执行完后,会查看是否有微任务队列。如果有，先执行微任务队列中的所有任务，如果没有，会读取宏任务队列中排在最前的任务，执行宏任务的过程中，遇到微任务，依次加入微任务队列。执行栈空后，再次读取微任务队列里的任务，依次类推。

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

## apply,call,bind区别

三者都是用于改变函数体内this的指向，但是bind与apply和call的最大的区别是：bind不会立即调用，而是返回一个新函数，称为绑定函数，其内的this指向为创建它时传入bind的第一个参数，而传入bind的第二个及以后的参数作为原函数的参数来调用原函数。

apply和call都是为了改变某个函数运行时的上下文而存在的（就是为了改变函数内部this的指向）；apply和call的调用返回函数执行结果；

如果使用apply或call方法，那么this指向他们的第一个参数，apply的第二个参数是一个参数数组，call的第二个及其以后的参数都是数组里面的元素，就是说要全部列举出来；
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

闭包的关键是函数有自己的作用域，这个作用域的寿命可可以存活的比函数本身还要长。

闭包："有权访问另一个函数作用域中变量的函数"（来自红宝书）

闭包的最大作用是“隐藏变量”，一大特性是**内部函数总是可以访问外部函数中声明的参数和变量，甚至在其外部函数被返回（寿终正寝）之后**，比如可以利用闭包访问私有变量。

缺点也很明显：
- 常驻内存，增加内存使用量
- 使用不当造成内存泄漏。

**写个闭包：**

```javascript
function fun(){
  var num = 1;
  return function () {
    console.log(++num)
  }
}

var count = fun()
count()//2
count()//3
count()//4
```

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
但即将加入标准的基本类型，它是为了解决超过Number的最大安全数字就会出现计算不准确的情况。

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

## 深拷贝和浅拷贝

- 浅拷贝（shallow copy）：只复制指向某个对象的指针，而不复制对象本身，新旧对象共享一块内存；
- 深拷贝（deep copy）：复制并创建一个一摸一样的对象，不共享内存，修改新对象，旧对象保持不变。

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
## ==隐式转化

如果双等号两边有下列情形，则会依次发生这样的判定转化：

- **其中一个是布尔**：布尔=>数值（false->0、true->1）
- **一个字符串一个数值**：字符串=>数值
- **一个是对象另一个不是**：调用对象的valueOf()方法，如果不是基本类型，基于返回值再调用ToPrimitive算法，这是一个内部的转化为基本类型值的算法，得到了基本类型值再基于前面的规则进行比较。
- **两个都是对象：** 如果他们指向同一个对象，则返回true，否则返回false
- null===undefined//true
- **如果包含NaN：**如果有一个是NaN，甚至NaN==NaN都返回false，而NaN!=NaN返回true

>例题：
>
>[]==![]//true
>
>- !运算符的优先级大于 ==，会将后面的值转化为布尔值。即![]变成!Boolean([]), 也就是!true,也就是false。
>- 实际上是对比 [] == false;
>- 运用上面的顺序，false是布尔值，所以转化为数值Number(flase), 为0。
>- 对比[] == 0;
>- 满足第三条规则[] 是对象（数组也属于对象），0不是对象。所以ToPrimitive([])是""
>- 对比"" == 0;
>- 满足第二条规则，"" 是字符串，0是数值，对比Number("") == 0, 也就是 0 == 0，结果为true
>- 所以得出 [] == ![]为true

## js垃圾回收和内存管理

关键：标记清除、V8 新生代和老生代内存、新生代 From 空间和 To 空间的逻辑和好处（拾荒者算法更快+内存碎片）、内存泄露

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

检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。

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

## 事件委托

事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。

事件委托的好处

- 只绑定一次事件，无频繁访问DOM，性能较高
- 当有新DOM生成时，无需重复绑定事件，代码清晰简洁

（联想到React的合成事件）

## 前端发送请求的多种方法fetch/ajax/axios

**‌Ajax**：

异步网络请求。区别于传统web开发中采用的同步方式。

Ajax带来的最大影响就是页面可以无刷新的请求数据。以往，页面表单提交数据，在用户点击完”submit“按钮后，页面会强制刷新一下，体验十分不友好。

```javascript
var request = new XMLHttpRequest(); // 创建XMLHttpRequest对象

//ajax是异步的，设置回调函数
request.onreadystatechange = function () { // 状态发生变化时，函数被回调
    if (request.readyState === 4) { // 成功完成
        // 判断响应状态码
        if (request.status === 200) {
            // 成功，通过responseText拿到响应的文本:
            return success(request.responseText);
        } else {
            // 失败，根据响应码判断失败原因:
            return fail(request.status);
        }
    } else {
        // HTTP请求还在继续...
    }
}

// 发送请求:
request.open('GET', '/api/categories');
request.setRequestHeader("Content-Type", "application/json") //设置请求头
request.send();//到这一步，请求才正式发出
```

**axios**:

axios不是一种新的技术,axios 是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范，

> 实际上，axios可以用在浏览器和 node.js 中是因为，它会自动判断当前环境是什么，如果是浏览器，就会基于XMLHttpRequests实现axios。如果是node.js环境，就会基于node内置核心模块http实现axios
>
有以下特点：

- 从浏览器中创建 XMLHttpRequests
- 从 node.js 创建 http 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 CSRF/XSRF

**fetch**：

fetch是前端发展的一种新技术产物。Fetch API提供了访问和操控HTTP流的js接口，例如请求和响应。它还提供了一个全局 fetch()方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

fetch() 会返回一个 promise。然后我们用then()方法编写处理函数来处理promise中异步返回的结果。处理函数会接收fetch promise的返回值，这是一个 Response 对象。

在使用fetch的时候需要注意：

- 接受到错误状态码时，Promise还是resolve（但是会将 resolve 的返回值的 ok 属性设置为 false ）仅当网络故障时或请求被阻止时，才会标记为 reject。
- 默认情况下，fetch 不会从服务端发送或接收任何 cookies, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 credentials 选项）。

fetch代表着更先进的技术方向，但是目前兼容性不是很好，在项目中使用的时候得慎重。

**缺点：** 

虽然fetch比XHR有极大的提高，特别是它在Service Worker中的集成，但是 Fetch 现在还没有方法中止一个请求，除非使用实验性功能， AbortController 和 AbortSignal，这是个通用的API 来通知 中止 事件。另外用 Fetch 不能监测上传进度。如果需要的话，还是使用axios

```javascript
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

**另外还有JQuery,request封装了网络请求**

## 什么是同步和异步

我们可以通俗理解为异步就是一个任务分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。排在异步任务后面的代码，不用等待异步任务结束会马上运行，也就是说，异步任务不具有”堵塞“效应。不连续的执行，就叫做异步。相应地，连续的执行，就叫做同步

"异步模式"非常重要。在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应，最好的例子就是Ajax操作。在服务器端，"异步模式"甚至是唯一的模式，因为执行环境是单线程的，如果允许同步执行所有http请求，服务器性能会急剧下降，很快就会失去响应。

## js中实现异步编程的六种方案

#### 回调函数

回调是一个简单的函数，它作为参数传递给另一个函数，并且在事件发生的时候会执行。在 JavaScript中，函数可以赋值给一个变量，作为其他函数的参数。

回调函数的优点是简单、容易理解和实现，缺点是不利于代码的阅读和维护，各个部分之间高度耦合，使得程序结构混乱、流程难以追踪（尤其是多个回调函数嵌套的情况），而且每个任务只能指定一个回调函数。此外它不能使用 try catch 捕获错误，不能直接 return。

#### 事件监听

这种方式下，异步任务的执行不取决于代码的顺序，而取决于某个事件是否发生。

这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"，有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。

#### 发布订阅

我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern）

#### Promise

**Promise的三种状态:**

- Pending----Promise对象实例创建时候的初始状态
- Fulfilled----可以理解为成功的状态
- Rejected----可以理解为失败的状态

这个promise一旦从等待状态变成为其他状态就永远不能更改状态了

promise的链式调用:

- 每次调用返回的都是一个新的Promise实例(这就是then可用链式调用的原因)
- 如果then中返回的是一个结果的话会把这个结果传递下一次then中的成功回调
- 如果then中出现异常,会走下一个then的失败回调
- 在then中使用了return，那么return的值会被Promise.resolve() 包装
- then中可以不传递参数，如果不传递会透到下一个then中
- catch 会捕获到没有捕获的异常

#### 生成器Generators/ yield

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同，Generator 最大的特点就是可以控制函数的执行。

- 语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。
- Generator 函数除了状态机，还是一个遍历器对象生成函数。
- 可暂停函数, yield可暂停，next方法可启动，每次返回的是yield后的表达式结果。
- yield表达式本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。

```javascript
function *foo(x) {
  let y = 2 * (yield (x + 1))
  let z = yield (y / 3)
  return (x + y + z)
}
let it = foo(5)
console.log(it.next())   // => {value: 6, done: false}
console.log(it.next(12)) // => {value: 8, done: false}
console.log(it.next(13)) // => {value: 42, done: true}
```

手动迭代Generator 函数很麻烦，而实际开发一般会配合 co 库去使用。co是一个为Node.js和浏览器打造的基于生成器的流程控制工具，借助于Promise，你可以使用更加优雅的方式编写非阻塞代码。

#### async/await

- async/await是基于Promise实现的，它不能用于普通的回调函数。
- async/await与Promise一样，是非阻塞的。
- async/await使得异步代码看起来像同步代码，写法优雅，处理 then 的调用链，能够更清晰准确的写出代码

## async/await比Generator好在哪

- 内置执行器。Generator 函数的执行必须靠执行器，所以才有了 co 函数库，而 async 函数自带执行器。也就是说，async 函数的执行，与普通函数一模一样，只要一行。
- 更广的适用性。 co 函数库约定，yield 命令后面只能是 Thunk 函数或 Promise 对象，而 async 函数的 await 命令后面，可以跟 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。
- 更好的语义。 async 和 await，比起星号和 yield，语义更清楚了。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。

## 浏览器实现提供的通信手段

[各种浏览器通信测试网站](https://alienzhou.github.io/cross-tab-communication)

|通信手段|常用场景|同源同 Tab|同源跨 Tab|跨域|
|-|-|-|-|-|
|`SessionStorage`|单页面临时状态|✔️|||
|`Web Workers`|独立线程，复杂计算|✔️|||
|`ServiceWorker`|独立线程，离线/弱网|✔️|||
|`Shared Workers`|多标签页共享的线程|✔️|✔️||
|`BroadcastChannel API`|多标签页广播通信|✔️|✔️||
|`localStorage`|长期本地存储|✔️|✔️||
|`IndexedDB`|大量结构化数据存储|✔️|✔️||
|`cookies`|用户身份认证、会话持久化|✔️|✔️||
|`postMessage`|不同窗口间安全通信|✔️|✔️|✔️|
|`WebSocket`|C/S双向通信|✔️|✔️|✔️|
|`CORS+Fetch/XMLHttpRequest`|跨域常用解决方案|✔️|✔️|✔️|
|`JSONP`|get 跨域|✔️|✔️|✔️|
|`WebRTC`|C/C的P2P通信|✔️|✔️|✔️|


## localStorage和sessionStorage


localStorage和sessionStorage一样都是用来存储客户端临时信息的对象。

- localStorage生命周期是永久，这意味着除非用户主动在浏览器上清除localStorage信息，否则这些信息将永远存在。
- sessionStorage生命周期为当前窗口或标签页，一旦窗口或标签页被永久关闭了，那么所有通过sessionStorage存储的数据也就被清空了。

不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享sessionStorage的信息。这里需要注意的是，页面及标签页仅指顶级窗口，如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的。

使用时使用相同的API：

- localStorage.setItem('myCat', 'Tom');
- let cat = localStorage.getItem('myCat');
- localStorage.removeItem('myCat');
- localStorage.clear();// 移除所有

localStorage的除了get的API都会触发storage事件，可以利用这个来做不同标签页的通信，比如多个页面的购物车数据同步。

## cookie和session的区别

Cookie与Session都能够进行会话跟踪，普通状况下二者均能够满足需求

- cookie数据存放在客户的浏览器（客户端）上，session数据放在服务器上，但是服务端的session的实现对客户端的cookie有依赖关系的；
- cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用session；
- session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用COOKIE；
- 单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能超过3K；

## 冒泡和捕获
**冒泡：** 

微软提出了事件冒泡的事件流，事件会从最内层的元素开始发生，一直向上传播，直到document对象。p元素上发生click事件的顺序应该是p -> body -> html -> document

**捕获：**

网景提出了事件捕获的事件流，事件冒泡相反，事件会从最外层开始发生，直到最具体的元素。p元素上发生click事件的顺序应该是document -> html -> body -> div -> p

W3C制定了统一的标准，**先捕获再冒泡**。

可以通过addEventListener()的第三个参数来确定捕获还是冒泡，第一个参数是要绑定的事件，第二个参数是回调函数，第三个参数默认是false，代表**事件冒泡阶段**调用事件处理函数，如果设置成true，则在**事件捕获阶段**调用处理函数。

在浏览器中，如果父子元素**都设置了捕获和冒泡**的输出，当点击子元素时：

- 先捕获父元素
- 子元素输出先注册的事件，如果是捕获先输出捕获，如果是冒泡先输出冒泡
- 在输出父元素的冒泡事件

**阻止冒泡和捕获：**
event.stopPropagation(); 以阻止事件的继续传播。

## ECMAScript 新特性&&常用

ECMAScript 2022: 
Array.at(-1)从后向前取

ECMAScript 2021: 
- Promise.any()和AggregateError
- 逻辑赋值运算符 (&&=, ||=, ??=)
- 数字字面量中使用下划线（_）作为分隔符,以增加可读性

ES 2020:
- 新的逻辑操作符??，只有当左侧的表达式结果为 null 或者 undefined 时，才会返回右侧的表达式。
- BigInt
- Promise.allSettled()
- 查询嵌套结构时,允许a?.b的语法,避开报错或特殊处理

ES 2021:
- Object.fromEntries(ECMAScript 2019)：该方法把键值对列表转换为一个对象
