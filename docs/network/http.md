本文旨在用“通俗”一点、又不失专业的话术，来说明白这些知识点。

?> 如果你是为了**面试**而看本文的话，我建议你先去通读一遍《图解HTTP》，然后再通过这篇文章来巩固知识点，可达到事半功倍的效果。

### 网络分层和意义

- 原理型网络分层
	- 物理层：采集到的数据转换成二进制数据。以及网络采用什么介质。
	- 数据链路层：数据帧级别的传输问题。负责错误重发，流量控制，拥堵控制等
	- 网络层：负责点对点的传播，两个主机之间的识别。Ip协议
	- 传输层：负责两个主机之间建立连接。拥有tcp/udp协议
	- 应用层：负责应用之间的打包和解析。http协议

- ISO标准分层
	- 物理层
	- 数据链路层
	- 网络层
	- 传输层
	- 会话层：确定会话的连接和中断
	- 表示层：对数据进行翻译、加密、压缩等操作
	- 应用层：为特定类型的网络应用提供访问OSI环境的手段

### ISO网络层/应用层……实现了哪些协议？

![wAO22F.jpg](https://s1.ax1x.com/2020/09/05/wAO22F.jpg)

### HTTP请求方法
HTTP/1.0

- **GET**：向服务器请求，获取资源。eg：请求某个页面。
- **POST**：传输实体主体，将数据发送给服务器。eg：把登录信息发送给服务器验证是否可以登录。
- **HEAD**：请求资源头部信息，和GET的资源头部信息一致，只是不返回报文主体。eg：下载大文件之前，先获取其大小，再决定是否要下载，以此节省带宽资源。

HTTP/1.1

- **PUT**：传输文件给服务器，然后保存到URI指定位置。eg：博客添加新文章。
- **DELETE**：删除指定文件，与PUT相反。eg：博客删除已存在的文章。
- **OPTIONS**：获取目标资源所支持的请求方法。eg：黑客常用OPTIONS请求来查看目标服务器支持的HTTP请求方法。
- **TRACE**：原样返回客户端请求的内容。eg：测试代理服务器中转时，请求是否被篡改。
- **CONNECT**：与代理服务器建立隧道来通信（网页开发用不到）。eg：翻墙时，为了通过国外服务器进行中转，首先需要客户端与国外服务器建立连接，这时使用CONNECT方法。
- **PATCH**：局部更新资源，是对PUT方法对补充。eg：局部更新指定文章的标题。

### URL和URI的区别
- URI是统一资源标识符，只是一个字符串的格式标准，并没有指定它的用途。
- URL是统一资源定位符，是资源定位的规范，包括网址、ftp服务器、文件路径等。

可以把URI理解成URL更高层次的抽象，其实URI本来是由[URL和URN（统一资源名称）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web)组成的，但URN没流行起来，导致现在的URI基本都是URL。
### GET和POST有什么区别？
- 数据传输方式不同：get在URL中，post在请求体中
- 安全性不同：get在URL中，很容易通过历史记录、缓存查到数据信息
- 数据类型不同：get只允许ASCII字符，post无限制
- 特性：get安全幂等。post非安全非幂等。这里的安全是指不会引起服务器状态变化。

> 幂等(idempotence)：多次请求结果和一次请求结果相同。
>
> “Methods can also have the property of "idempotence" in that (aside from error or expiration issues) the side-effects of N > 0 identical requests is the same as for a single request.”

### PUT和POST都可以给服务器新增资源，有什么区别？
- PUT：幂等，URI指向单一资源。
- POST：非幂等，URI指向资源集合。

eg：比如博客系统，创建新文章，用`POST https://www.blog.com/articles`,多次调用，就创建多个文章。如果更新指定文章的标题，用`PUT https://www.blog.com/articles/000001`,多次调用，和第一次调用效果相同。
### PUT和PATCH都可以向服务器更新资源，有什么区别？
PATCH是对PUT的补充。PUT是更新整条记录，对重复字段也进行覆盖。PATCH对资源进行“局部更新”。

### HTTP请求报文是什么样的？
请求报文4部分组成：

- 请求行
- 请求头部
- 空行
- 请求体

![请求报文的组成](https://s1.ax1x.com/2020/03/25/8veMXq.png)

![《图解HTTP》——请求报文](https://s1.ax1x.com/2020/03/25/8vV8K0.jpg)

上图来自《图解HTTP》，GET方法无请求体，如果是POST方法，可能会有形如name=admin&password=admin的请求体。

### HTTP响应报文是什么样的？
请求报文有4部分组成:

- 响应行
- 响应头
- 空行
- 响应体

![响应报文组成](https://s1.ax1x.com/2020/03/25/8vmcZV.png)

![《HTTP图解》响应报文](https://s1.ax1x.com/2020/03/25/8ve64e.jpg)

### 聊一聊HTTP首部有哪些？
#### RFC2616定义的47种首部字段
##### 通用首部（General Header Fields）9个
|首部字段名|说明|
|-|-|
|Cache-Control✨|控制缓存|
|Connection✨|控制代理不再转发的首部字段、管理持久连接|
|Transfor-Encoding✨|指定报文主体的传输编码方式|
|Date|创建报文的日期|
|Via|代理服务器相关信息|
|Warning|错误通知|
|Upgrade|升级为其他协议|
|Trailer|记录了报文主体之后的首部字段|
|Pragma|报文指令，HTTP/1.1之前的历史遗留字段|

##### 请求首部（Request Header Fields）19个
|首部字段名|说明|
|-|-|
|Accept✨|客户端或代理能够处理的媒体类型|
|Accept-Encoding|优先的内容编码（gzip、deflate……）|
|Accept-Language|优先的语言（中文、英文……）|
|Accept-Charset|优先的字符集（ISO-8859-1……）|
|If-Match✨|比较实体标记(ETage),匹配一致则同意请求|
|If-None-Match✨|比较实体标记，匹配不一致则同意请求|
|If-Modified-Since✨|比较资源更新时间，如果当前在指定日期之后，则接受请求|
|If-Unmodified-Since✨|比较资源更新时间，与If-Modified-Since相反|
|Range✨|获取部分资源的范围。eg：bytes=5001-10000|
|If-Range|资源未更新时发送实体byte的范围请求|
|Authorization✨|Web的认证信息|
|Proxy-Authorization|代理服务器要求Web认证信息|
|Host✨|请求资源所在的服务器|
|From|用户的邮箱地址|
|User-Agent|客户端程序信息|
|Max-Forwrads|最大逐跳次数|
|TE|传输编码的优先级|
|Referer|请求中URI的原始获取方|
|Expect|期待服务器的特定行为|

##### 响应首部（Response Header Fields）9个
|首部字段名|说明|
|-|-|
|Accept-Range|是否接受字节范围请求|
|Age|推算资源创建经过的时间|
|ETag✨|实体标记，资源的匹配信息|
|Location✨|令客户端重定向到指定URI|
|Proxy-Authenticate|代理服务器对客户端的认证信息|
|Retry-After|下次请求服务器的时间，与503一起使用|
|Server✨|HTTP服务器的安装信息|
|Vary|代理服务器的缓存信息|
|WWW-Authenticate|服务器对客户端的认证信息|

##### 实体首部（Entiy Header Fields）10个
实体首部字段是包含在请求报文和响应报文中实体部分所用的首部，用于补充内容的更新时间等与实体相关的信息
。

|首部字段名|说明|
|-|-|
|Allow✨|资源可支持的HTTP方法|
|Expires✨|实体主体过期的日期时间|
|Last-Modified✨|资源最后的修改日期时间|
|Content-Encoding|实体主体使用的编码方式|
|Content-Language|实体主体的自然语言|
|Content-Length|实体主体的大小（单位：字节）|
|Content-Location|代替对应资源的URI|
|Content-MD5|实体主体的报文摘要|
|Content-Range|实体主体的位置范围|
|Content-Type|实体主体的媒体类型|

#### 非HTTP/1.1的首部字段
Cookie、Set-Cookie为cookie服务，Content-Disposition来指示如何显示附加的文件……

### HTTP状态码

|状态码|说明|
|-|-|
|200 Ok✨|客户端请求在服务器被正确处理|
|201 Created|请求已经实现，并且有新资源已经依据请求的需要被建立|
|202 Accepted|请求已接受，但还没执行，不保证完成请求|
|204 Not Content|请求成功，但相应报文不包含实体的主体部分|
|206 Partial Content✨|进行范围请求|
|301 Moved Permanently|永久重定向，资源被分配了新的URL|
|302 Found✨|临时重定向，资源被临时分配了新的URL|
|303 See Other|资源存在着另一个URL，应使用GET获取资源|
|304 Not Modified✨|服务器允许访问资源，但请求未满足条件|
|307 Temporary Redirect|临时重定向，和302含义相同|
|400 Bad Request✨|请求报文存在语法错误|
|401 Unauthorized✨|请求需要通过有HTTP认证信息|
|403 Forbidden✨|请求资源访问被服务器拒绝|
|404 Not Found✨|资源未找到|
|405 method not allowed|请求方式与后台规定的不符合|
|408 Request Timeout|客户端请求超时|
|409 Confict|请求资源可能引起冲突|
|500 Internal Sever Error✨|服务器在执行请求时发生了错误|
|503 Service Unavailable|服务器正超负载或停机维护|

### HTTP的keep-alive是干什么的？
早期HTTP/1.0每次请求都要重新建立一次连接，为了减少资源消耗、缩短响应时间，就要重用连接，想要保持长连接，需要在请求头上加上Connection:keep-alive。

### HTTP和HTTPS的区别？
https是安全版的http，http协议都是明文传输，https的加密传输可以最大程度保证通信安全。

### HTTP/1.0和HTTP/1.1有什么区别？
HTTP/1.0有`GET`、`POST`、`HEAD`三个请求方法，HTTP/1.1新增了`PUT`、`DELETE`、`PATCH`、`OPTIONS`、`TRACE`、`CONNECT`
请求方法，并且支持了持久连接。
### HTTP2相对于HTTP/1.X有什么优势？
- 多路复用，合并TCP请求✨
- 头部压缩，只发送差异数据，减少头部信息量
- 二进制分帧解析更高效
- 服务器主动推送

> - **多路复用：** 就是在一个TCP连接中可以存在多条流。换句话说，也就是可以发送多个请求，对端可以通过帧中的标识知道属于哪个请求。通过这个技术，可以避免HTTP旧版本中的队头阻塞问题，即消除了因多个TCP连接而带来的延时和内存消耗，极大的提高传输性能。

### HTTPS的加密通信是怎么样的？
HTTPS使用了两种加密，对称加密和非对称加密。

- 对称加密：通讯双方使用同一密钥。性能好，但不安全。
- 非对称加密：通讯双方各有一个私钥，共享一个公钥。安全，但性能差。

解决方案（混合加密）：
将*对称加密的密钥*通过*非对称加密的公钥*进行**加密**，并发送出去,通信双方在通过各自的*私钥*进行**解密**获得*对称加密的密钥*，然后双方就可以使用对称加密来沟通。

混合加密+数字证书保证安全性：

1. 服务器把自己的公钥登录到CA（数字证书认证机构）。
2. CA用自己的私钥部署数字签名并颁发公钥证书。
> 公钥证书=服务器的公开密钥+CA的数字签名。
3. 客户端拿到服务器的公钥证书后，用CA公钥进行验证真实性。
> CA的公钥，已经事先写进浏览器了。
4. 客户端使用服务器公钥加密报文。
5. 服务器使用自己的私钥进行解密。

### HTTP缓存过程是怎样的？
1. 客户端向服务器请求资源
2. 服务器缓存资源，并通过响应头决定缓存策略
3. 客户端根据响应头的缓存策略决定是否缓存（这里假设是），并将响应头与资源缓存下来
4. 当客户端再次请求命中资源的时候，检查缓存策略，根据策略不同、过期时间等判断直接读取本地缓存还是服务器协商缓存。

![缓存过程](https://s1.ax1x.com/2020/03/26/8z0ikT.png)

### 强缓存和协商缓存？
强缓存(Cache-Control)：*只有首次请求和服务器通信*，读取缓存不用发送请求。返回200

> 如果资源没过期，则直接从缓存读取（强制缓存），此时在Network一栏可以看到资源对应的状态码为200（from disk cache）或者是 200 （from memory cache）
> 
>比如，资源没过期的时候我们打开新的页面，资源会从硬盘缓存中读取（from disk cache）；如果我们此时又刷新页面，资源会从内存缓存中读取（from memory cache）

协商缓存(ETag/If-None-Match)：*总会与服务器交互*，第一次是拿数据和ETag，之后凭ETag询问是否更新。返回304

### 常见的网络攻击方法和解决方案？
##### SQL注入
攻击手段：

某些服务器处理用户账号，是直接将账号字符串拼接到SQL语句中，如果黑客利用规则拼接类似1=1的判断语句，数据库中可能会产生异常行为。

解决方案：

在Java中使用prepareStatement类预编译SQL语句，把固定格式的SQL编译后放入数据库缓冲池中，之后传入的字符串都当作参数来处理，而不是SQL指令。

##### XSS（跨站脚本攻击）
攻击手段：

攻击者往Web页面插入恶意`<script>`代码，当用户浏览页面时，嵌入其中的script恶意代码就会执行。

解决方案：

1. 过滤`<script>`、`<img>`、`<a>`标签
2. 对`<`、`>`在输入的时候进行编码转换
3. 限制字符串长度
4. 如果使用React的JSX语法，渲染输入内容之前会进行转义

##### CSRF（跨站请求伪造）
攻击手段：

用户登录受信任的A网站，产生Cookie，在没有登出A的情况下，访问了危险的B网站，B要求访问第三方站点（A），并发送一个请求（request），然后B就带着之前的Cookie（伪装成用户的名义）去请求。

解决方案：

验证码

### DNS域名解析
DNS系统是将域名解析为一个IP地址或者另一个域名CNAME。当请求方拿到ip地址之后，对服务器进行真正的请求调用。

DNS查询过程：

- 浏览器是否有缓存
- 操作系统是否有缓存
- 本地Hosts文件是否有缓存
- 本地DNS服务器是否有缓存
- 向根域名服务器查询，若知道对应IP则返回IP，不知道则告诉本地DNS服务器要去哪个顶级域名服务器查询
- 迭代，直到找到对应的ip

值得一提的是，CNAME是是实现CDN内容分发的关键。（引导面试官提问CDN）

### CDN（内容分发网络）
网络传输是依赖于网线的，请求双方距离越长，延迟就越久。
CDN内容分发网络，可以做到尽可能地缩短地域距离。CDN提供商在世界各地部署了大量的静态资源服务器，开发者将自己的静态资源传给CDN服务，这些静态资源将自动的分布到世界各地去。当客户端请求域名时，域名首先会被DNS解析成“CDN专用域名”（在阿里云服务中被叫做“加速域名”），然后CDN专用DNS服务器会解析加速域名，根据服务器上记录的所有CDN服务器地址，选出一个离用户最近的CDN服务器地址并返回给用户，用户即可以访问离自己最近的一台CDN服务器了。

> 用户的地理地址，是根据用户的ip进行判断，类似于“北京移动”、“上海电信”这种。在CDN服务商中有一套标准的“IP字典”，方便对应用户的地理地址。

[参考文章](https://juejin.im/post/5d2d8928f265da1b95708b97)

### CDN上如果提交了新资源怎么更新？

对于实时性要求不高的资源，我们初始化时在CDN控制台上自定义文件或目录的缓存策略，比如img文件的缓存时间等等，根据不同的缓存策略采用不同的更新方案。

如果对于实时性要求比较高的资源，可以将缓存时间设为0，在更新资源后，可以根据我们需要在CDN控制台中开启**缓存刷新**或**缓存预热**。

> 刷新：CDN节点缓存内容强制过期，当向CDN节点请求资源时，会从源站获取资源并将其缓存。
> 
> 预热：源站主动将对应资源缓存到CDN节点，首次请求时，就你那个直接从CDN节点缓存中获取最新资源。

### cookie，localStorage， sessionStorage三者区别

- cookie始终在同源的http请求中携带，即使不需要，cookie在浏览器和服务器中来回传递。而localStorage和sessionStorage一般用于本地存储，不会好服务器通信，也不会自动把数据发送给服务器。
- 存储大小不同，cookie为4kb左右；localStorage， sessionStorage可以达到5M
- 数据有效期不同，sessionStorage仅在同源窗口中有效，关闭浏览器窗口就消失了，cookie在过期时间前一直有效，即使在关闭浏览器之后(如果没有设置过期时间，关闭浏览器就过期了)，localStorage长期有效，除非主动删除
- localStorage， sessionStorage有现成的API， cookie需要程序员手动封装

## Cookie和Session

cookie和session都可以追踪会话状态，cookie也可以被用于保存用户的喜好等非敏感信息。

- session 在服务器端，cookie 在客户端（浏览器）
- session 默认被存在在服务器的一个文件里（不是内存）
- session 的运行依赖 session id，而 session id 是存在 cookie 中的，也就是说，如果浏览器禁用了 cookie ，同时 session 也会失效（但是可以通过其它方式实现，比如在 url 中传递 session_id）
- session 可以放在 文件、数据库、或内存中都可以。
