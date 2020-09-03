# Webpack

## Loader和Plugin的区别

**区别一：**

`Loader` 用来告诉webpack如何转换某一类型的文件，并且引入到导出的文件中。本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。其实就是


`Plugin` 就是插件，作用更大，可以打包优化，资源管理和注入环境变量。基于事件流框架 Tapable，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

**区别二**：

`Loader` 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。

`Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

## 常见的Loader

- file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件 (处理图片和字体)
- url-loader：与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)
- svg-inline-loader：将压缩后的 SVG 内容注入代码中
- image-loader：加载并且压缩图片文件
- json-loader 加载 JSON 文件（默认包含）
- babel-loader：把 ES6 转换成 ES5
- ts-loader: 将 TypeScript 转换成 JavaScript
- awesome-typescript-loader：将 TypeScript 转换成 JavaScript，性能优于 ts-loader
- sass-loader：将SCSS/SASS代码转换成CSS
- css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
- style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS
- eslint-loader：通过 ESLint 检查 JavaScript 代码
- mocha-loader：加载 Mocha 测试用例的代码
- coverjs-loader：计算测试的覆盖率
- vue-loader：加载 Vue.js 单文件组件
- cache-loader: 可以在一些性能开销较大的 Loader 之前添加，目的是将结果缓存到磁盘里

## 是否写过Loader？简单描述一下编写loader的思路？

Loader 支持链式调用，所以开发上需要严格遵循“单一职责”，每个 Loader 只负责自己需要负责的事情。
Loader的API 可以去官网查阅

- Loader 运行在 Node.js 中，我们可以调用任意 Node.js 自带的 API 或者安装第三方模块进行调用
- Webpack 传给 Loader 的原内容都是 UTF-8 格式编码的字符串，当某些场景下 Loader 处理二进制文件时，需要通过 exports.raw = true 告诉 Webpack 该 Loader 是否需要二进制数据
- 尽可能的异步化 Loader，如果计算量很小，同步也可以
- Loader 是无状态的，我们不应该在 Loader 中保留状态
- 使用 loader-utils 和 schema-utils 为我们提供的实用工具
- 加载本地 Loader 方法（Npm link和ResolveLoader）

## 常见的Plugin

- html-webpack-plugin：简化 HTML 文件创建 (依赖于 html-loader)
- web-webpack-plugin：可方便地为单页应用输出 HTML，比 html-webpack-plugin 好用
- webpack-parallel-uglify-plugin: 多进程执行代码压缩，提升构建速度
- mini-css-extract-plugin: 分离样式文件，CSS 提取为独立文件，支持按需加载 (替代extract-text-webpack-plugin)
- serviceworker-webpack-plugin：为网页应用增加离线缓存功能
- clean-webpack-plugin: 目录清理
- ModuleConcatenationPlugin: 开启 Scope Hoisting
- speed-measure-webpack-plugin: 可以看到每个 Loader 和 Plugin 执行耗时 (整个打包耗时、每个 Plugin 和 Loader 耗时)
- webpack-bundle-analyzer: 可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)

## 是否写过Plugin？简单描述一下编写Plugin的思路？

webpack在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在特定的阶段钩入想要添加的自定义功能。Webpack 的 Tapable 事件流机制保证了插件的有序性，使得整个系统扩展性良好。

Plugin的API 可以去官网查阅

- compiler 暴露了和 Webpack 整个生命周期相关的钩子
- compilation 暴露了与模块和依赖有关的粒度更小的事件钩子
- 插件需要在其原型上绑定apply方法，才能访问 compiler 实例
- 传给每个插件的 compiler 和 compilation对象都是同一个引用，若在一个插件中修改了它们身上的属性，会影响后面的插件
- 找出合适的事件点去完成想要的功能
	- emit 事件发生时，可以读取到最终输出的资源、代码块、模块及其依赖，并进行修改(emit 事件是修改 Webpack 输出资源的最后时机)
	- watch-run 当依赖的文件发生变化时会触发
- 异步的事件需要在插件处理完任务时调用回调函数通知 Webpack 进入下一个流程，不然会卡住

## Webpack构建流程简单说一下

- 初始化：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
- 编译：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
- 输出：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

## webpack开发时，你用过哪些可以提高效率的插件？

- webpack-dashboard：可以更友好的展示相关打包信息。
- webpack-merge：提取公共配置，减少重复配置代码
- speed-measure-webpack-plugin：简称 SMP，分析出 Webpack 打包过程中 Loader 和 Plugin 的耗时，有助于找到构建过程中的性能瓶颈。
- size-plugin：监控资源体积变化，尽早发现问题
- HotModuleReplacementPlugin：模块热替换

## source map是什么？生产环境怎么用？

`source map` 是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 `soucre map`。
map文件只要不打开开发者工具，浏览器是不会加载的。
线上环境一般有三种处理方案：

- hidden-source-map：借助第三方错误监控平台 Sentry 使用
- sourcemap：通过 nginx 设置将 .map 文件只对白名单开放(公司内网)
- nosources-source-map：只会显示具体行数以及查看源代码的错误栈。安全性比 sourcemap 高

注意：避免在生产中使用 inline- 和 eval-，因为它们会增加 bundle 体积大小，并降低整体性能。

## 模块打包原理知道吗？

Webpack 实际上为每个模块创造了一个可以导出和导入的环境，本质上并没有修改代码的执行逻辑，代码执行顺序与模块加载顺序也完全一致。

## 文件监听原理

在发现源码发生变化时，自动重新构建出新的输出文件。
Webpack开启监听模式，有两种方式：

- 启动 webpack 命令时，带上 --watch 参数在配置 
- webpack.config.js 中设置 watch:true
- 
缺点：每次需要手动刷新浏览器

原理：轮询判断文件的最后编辑时间是否变化，如果某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等 aggregateTimeout 后再执行。

## Webpack的热更新原理

Webpack 的热更新又称热替换（Hot Module Replacement），缩写为 HMR。 这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

HMR的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个 Websocket，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发起 Ajax 请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 jsonp 请求获取该chunk的增量更新。

后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 HotModulePlugin 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像react-hot-loader 和 vue-loader 都是借助这些 API 实现 HMR。

## 如何对bundle体积进行监控和分析

VSCode 中有一个插件 Import Cost 可以帮助我们对引入模块的大小进行实时监测，还可以使用 webpack-bundle-analyzer 生成 bundle 的模块组成图，显示所占体积。

bundlesize 工具包可以进行自动化资源体积监控。

## 配置文件上百行乃是常事，如何保证各个loader按照预想方式工作？

可以使用 enforce 强制执行 loader 的作用顺序，pre 代表在所有正常 loader 之前执行，post 是所有 loader 之后执行。(inline 官方不推荐使用)

## Babel原理

大多数JavaScript Parser遵循 estree 规范，Babel 最初基于 acorn 项目(轻量级现代 JavaScript 解析器)
Babel大概分为三大部分：

- 解析：将代码转换成 AST
	- 词法分析：将代码(字符串)分割为token流，即语法单元成的数组语法分析：
	- 分析token流(上面生成的数组)并生成 AST
- 转换：访问 AST 的节点进行变换操作生产新的 AST
	- Taro就是利用 babel 完成的小程序语法转换
- 生成：以新的 AST 为基础生成代码
