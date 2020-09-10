
## Node/特点/优点
nodeJS 是一门单线程!异步!非阻塞语言!

## Node里的模块汇总

这里汇总nodeV12.16.2版本的稳定模块，排除了不稳定或者废弃的模块

|模块|说明|
|-|-|
|assert（断言）|提供断言测试（一般assert用于捕捉程序员自己的错误，Error/Exception捕捉用户或者环境的错误）|
|async_hooks（异步钩子）|用于跟踪应用的异步资源|
|Buffer（缓冲器）|处理二进制数据|
|child_process（子进程）|衍生出子进程来操作|
|cluster（集群）|创建共享服务器端口的子进程，充分利用多核系统|
|console（控制台）|提供简单的调试控制台，类似于web浏览器提供的JavaScript控制台|
|crypto（加密）|提供加密功能，包括对OpenSSL的哈希、HMAC、加密、解密、签名、验证功能的一套封装|
|debugger（调试器）|进程外的调试程序，相当于IDE里的调试功能|
|dgram（数据报）|提供了UDP数据包socket实现|
|dns（域名服务器）|用于启用域名解析|
|Error（错误）|js运行或执行底层过程中抛出的错误|
|events（事件触发器）|绑定事件和触发事件|
|fs（文件系统）|与文件系统进行交互|
|global（全局变量）|全局变量，它的属性都是全局变量，相当于浏览器中的windows|
|http（HTTP）|实用HTTP服务器和客户端|
|http2（HTTP/2）|提供了HTTP/2协议的实现|
|https（HTTPS）|提供了HTTPS（TLS/SSL的HTTP协议）实现|
|module（模块）|实现模块化|
|net（网络）|创建基于流的TCP或IPC服务器和客户端|
|os（操作系统）|提供了与操作系统相关的方法和属性|
|path（路径）|处理文件与目录路径的工具|
|perf_hooks（性能钩子）|监控性能、测量异步操作时长|
|process（进程）|提供当前进程的信息并对其控制|
|querystring（查询字符串）|对HTTP请求所带的数据进行解析|
|readline（逐行读取）|可一行一行读取流|
|repl（交互式解释器）|提供了一种“读取-求职-输出”的循环实现，相当于在控制台直接启动node的`>`操作|
|stream（流）|处理流式数据的对象|
|string_decoder（字符串解码器）|安全地将Buffer对象解码为字符串|
|timer（定时器）|实现了与浏览器API类似的定时器|
|tls（安全传输层）|安全传输层（TLS）及安全套接层（SSL）协议实现，建立在OpenSSL基础上|
|tty（终端）|提供了tty.ReadStream和tty.WriteStream，大多数情况下用户不必手动创建这两个类的实例|
|url（URL）|处理和解析URL|
|util（实用工具）|提供了转换回调风格、调试输出、废弃API等相关的工具|
|v8（V8引擎）|暴露了Node底层二进制文件中的V8版本API|
|vm（虚拟机）|在V8虚拟机上下文中编译和运行代码|
|worker_threads（工作线程）|允许使用并行地执行JavaScript的线程|
|zlib（压缩）|实现压缩功能|

## 为什么要用Node

- 简单强大，轻量可扩展．简单体现在node使用的是javascript,json来进行编码，人人都会；
- 强大体现在非阻塞IO,可以适应分块传输数据，较慢的网络环境，尤其擅长高并发访问；
- 轻量体现在node本身既是代码，又是服务器，前后端使用统一语言;
- 可扩展体现在可以轻松应对多实例，多服务器架构，同时有海量的第三方应用组件．

## 什么用Nodejs,它有哪些优缺点？
简单强大，轻量可扩展

- 简单体现在node使用的是javascript,json来进行编码，人人都会；
- 强大体现在非阻塞IO,可以适应分块传输数据，较慢的网络环境，尤其擅长高并发访问；
- 轻量体现在node本身既是代码，又是服务器，前后端使用统一语言;
- 可扩展体现在可以轻松应对多实例，多服务器架构，同时有海量的第三方应用组件．
- 不用担心多线程，锁，并行计算的问题
- V8引擎速度非常快

缺点：
- nodejs更新很快，可能会出现版本兼容，比如node的事件循环11版本前后就发生了变化
- nodejs还不算成熟，还没有大制作
- nodejs不像其他的服务器，对于不同的链接，不支持进程和线程操作

## 什么是错误优先的回调函数？

错误优先(Error-first)的回调函数（Error-First Callback）用于同时返回错误和数据。第一个参数返回错误，并且验证它是否出错；其他参数返回数据。
```javascript
fs.readFile(filePath, function(err, data)
  {
      if (err)
      {
          // 处理错误
          return console.log(err);
      }
      console.log(data);
  });
```

## node架构
主要分为三层，应用app >> V8及node内置架构 >> 操作系统. 

V8是node运行的环境，可以理解为node虚拟机．

node内置架构又可分为三层: 核心模块(javascript实现) >> c++绑定 >> libuv + CAes + http.

## node有哪些核心模块?
EventEmitter, Stream, FS, Net和全局对象

## 事件循环机制在Node和浏览器中有什么区别

浏览器和Node 环境下，microtask 任务队列的执行时机不同

- Node端，microtask 在事件循环的各个阶段之间执行
- 浏览器端，microtask 在事件循环的 macrotask 执行完之后执行

node版本大于11的，和浏览器表现一致，小于11的，会有这样的策略：若第一个定时器任务出队并执行完，发现队首的任务仍然是一个定时器，那么就将微任务暂时保存，直接去执行新的定时器任务，当新的定时器任务执行完后，再一一执行中途产生的微任务。

## process.nextTick

process.nextTick 是一个独立于 eventLoop 的任务队列。

在每一个 eventLoop 阶段完成后会去检查这个队列，如果里面有任务，会让这部分任务优先于微任务执行。

