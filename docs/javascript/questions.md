# Javascript

## 跨域原理，解决方案
浏览器的“同源策略”会导致跨域，其中同源是指“协议、域名、端口”都相同。我曾经做项目的时候就遇到过跨域问题，当时chrome控制台爆红，后来在jsonp和CORS之间进行对比，最后选择了CORS。

**为什么选择CORS？**因为jsonp只支持get请求，但我们还有post请求，CORS就满足了我的要求。服务端设置响应头中的Access-Control-Allow-Origin为对应的域名。（接下来有可能会提问网络问题）

jsonp原理：静态文件不受同源政策影响，我可以返回一个script里面有一个回调函数，函数的里面是我要的东西。
## 原型链✨
绝大部分函数都有一个prototype属性，含义是**函数的原型对象**，所有被创建的对象都可以访问原型对象的属性和方法。在new的过程中，会将函数的prototype赋值给对象的__proto__属性，当访问一个对象的属性的时候，如果对象内部不存在这个属性，就从__proto__所指的父对象寻找，一直找到终点null，通过__proto__属性连接的这条链就是原型链。

> 原型链最顶端：`Object.prototype.__proto__=null`


## new一个对象发生了什么？
三步：

1. 创建以这个函数为原型的空对象；

2. 将函数的prototype赋值给对象的__proto__属性；

3. 将对象作为函数的this传进去，如果有return就返回return里面的内容，如果没有就创建这个对象。
## 基本类型
String、Boolean、Number、Null、Undefined、Symbol。

Symbol可以用来创建唯一常量。
## this的指向问题
this在函数里：函数的拥有者默认绑定this
例如在对象的方法，this指向对象