
手撸代码不仅考察代码逻辑，还考察格式规范，这个注意一下。


## 实现生物上的S型增长曲线

**美团点评**

面试官："在实现动画的过程中，想要控制某些动画速度先快后慢，假如说现在要自定义一个速度曲线，应该怎么实现？比如**先慢速增加，再快速增加，再慢速增加**"

我提到了动画的`animation-timing-function`，大概就是类似`linear`、`ease`、`ease-in-out`……这种，不过意思是要实现定制化

思路：曲线其实就是"sin(t)"的变形，然后取中间一部分
![sin函数](https://s1.ax1x.com/2020/04/04/GwyBLT.jpg)

以便于调节单位长度的变化，我们最好将曲线变形成这样：
![sin函数变形](https://s1.ax1x.com/2020/04/04/G0ti8S.jpg)

我已经将函数变形完成，公式如下：（这里将3.14=Math.PI）突然懵逼的小伙伴还请复习三角函数知识。

![变形公式](https://s1.ax1x.com/2020/04/05/GBmRMV.jpg)

转化为代码：

```javascript
function l(x) {
    let {sin,PI} = Math;
    let y = 0.5*((sin(PI*x-PI/2.0)+1));
    return y;
}
```

传入{0-1}，按照速度曲线变化，输出{0-1}

## 深拷贝

百度二面

注意JSON.parse(JSON.stringify(json));这个

```javascript

// 深拷贝
// 局限性：循环引用，不能处理无原型链的对象
let deepCopy = (obj: any): any => {
    if (obj === null) return null
    let clone = Object.assign({}, obj)
    // 拷贝属性
    Object.keys(obj).forEach(k => {
        // 特殊对象还是需要特殊处理，需要拷贝更多，需要添加更多
        if (typeof obj[k] === 'function') {
            clone[k] = obj[k].bind(clone)
        } else if (obj[k] instanceof RegExp) {
            clone[k] = new RegExp(obj[k])
        } else if (obj[k] instanceof Date) {
            clone[k] = new Date(obj[k])
        } else if (obj[k] instanceof Set) {
            clone[k] = new Set(deepCopy(Array.from(obj[k])))
        } else if (obj[k] instanceof Map) {
            clone[k] = new Map(deepCopy(Array.from(obj[k])))
        } else if (typeof obj[k] === 'object') {
            clone[k] = deepCopy(obj[k])
        } else {
            clone[k] = obj[k]
        }
    })

    if (Array.isArray(obj)) {
        clone.length = obj.length
        return Array.from(clone)
    }
}


```

## 数组乱序

百度二面

```javascript

let arr  = [1,2,3,4,4,5,6,7,7,5,5,2,3];

//方法一：sort随机排序
function fun(arr){
	arr.sort(()=>)
}

/**
 * 方法二：随机交换每个下表值
 * @param arr
 * @returns {*}
 */
function fun(arr) {

    let len = arr.length;
    for (let i = 0; i < len; i++) {
        let handleIndex = Math.floor(Math.random() * len);

        let temp = arr[i];
        arr[i] = arr[handleIndex];
        arr[handleIndex] = temp;
        temp = arr[i];
    }

    return arr;
}

console.log(fun(arr));

```

## 数组去重

```javascript
let arr = [1, 2, 3, 4, 5, 5, 3, 2, 1];

function removeRepeat(arr) {
    let newArr = [];

    //排序数组
    arr.sort();

    //当前值
    let curr = null;
    
    for (let i = 0; i < arr.length; i++) {
        if (curr===arr[i]){
            continue;
        }

        curr = arr[i];
        newArr.push(arr[i]);
    }
    return newArr;
}

console.log(removeRepeat(arr));
```

## 数组扁平化

```javascript
let arr = [1, 2, 3, [4, 5], [6, [7, 8]],];

//方法一：flat扁平化数组，默认为1
console.log(arr.flat(Infinity));

//方法二：递归
function fun(arr){
    let newArr = [];
    arr.forEach(value => {
        if (Array.isArray(value)){
            newArr = newArr.concat(fun(value));
        }else {
            newArr =newArr.concat(value);
        }
    });
    return newArr;
}
console.log(fun(arr));

//方法三：toString()
//实现到Array上
Array.prototype.f2=function(){
 let arr=this;
 return arr.toString().split(',')
}
console.log(f2(arr));


```

## 手撸继承

创建一个 Person 类，其包含公有属性 name 和私有属性 age 以及公有方法 setAge ；创建一个 Teacher 类，使其继承 Person ，并包含私有属性 studentCount 和私有方法 setStudentCount 

```javascript

const [Person, Teacher] = (function () {
    const _age = Symbol('_age');

    const _studentCount = Symbol('_studentCount');
    const _setStudentCount = Symbol('_setStudentCount');


    class Person {
        constructor(name, age) {
            this.name = name;
            this[_age] = age;
        }

        setAge(age) {
            this[_age] = age;
        }
    }

    class Teacher extends Person{
        constructor(name,age,studentCount){
            super(name,age);
            this[_studentCount] = studentCount;
        }

        /**
         * 私有方法，设置学生数量
         * @param studentCount
         */
        [_setStudentCount](studentCount){
            this[_studentCount] = studentCount;
        }

        setCount(count){
            this[_setStudentCount](count);
        }

    }

    return [Person, Teacher];
})();

const p = new Person('初始名字',0);
const t = new Teacher('老师',24,55);

```

其实未来js是可以使用#来实现私有属性和私有方法的，截止2020年4月，这个提案已经被审核到[stage3](https://github.com/tc39/proposal-private-methods)阶段，即作为候选的完善阶段。

## 实现Promise

**第一版 Promise：能保存回调方法**

思路是在 .then() 方法中, 将 fullfill 和 reject 结果的回调函数保存下来, 然后在异步方法中调用. 因为是异步调用, 根据 event-loop 的原理, promiseAsyncFunc().then(fulfillCallback, rejectCallback) 传入的 callback 在异步调用结束时一定是已经赋值过了.

```javascript
// Promise 形式的异步方法定义
var promiseAsyncFunc = function() {
  var fulfillCallback
  var rejectCallback

  setTimeout(() => {
    var randomNumber = Math.random()
    if (randomNumber > 0.5) fulfillCallback(randomNumber)
    else rejectCallback(randomNumber)
  }, 1000)
  return {
    then: function(_fulfillCallback, _rejectCallback) {
      fulfillCallback = _fulfillCallback
      rejectCallback = _rejectCallback
    }
  }
}

// Promise 形式的异步方法调用
promiseAsyncFunc().then(fulfillCallback, rejectCallback)

```

<details>
<summary>【点击展开】更高级的实现Promise的方法</summary>
<pre>
**‌第二版 Promise：实构造函数：**

当前我们的实现 Promise 中，异步逻辑代码和 Promise 的代码是杂糅在一起的，让我们将其区分开：

定义一个新方法 ownPromise() 用于创建 Promise，并在promiseAsyncFunc() 中暴露出 fulfill 和 reject 接口方便异步代码去调用。

```javascript
var promiseAsyncFunc = function() {
  var fulfillCallback
  var rejectCallback

  return {
    fulfill: function(value) {
      if (fulfillCallback && typeof fulfillCallback === 'function') {
        fulfillCallback(value)
      }
    },
    reject: function(err) {
      if (rejectCallback && typeof rejectCallback === 'function') {
        rejectCallback(err)
      }
    },
    promise: {
      then: function(_fulfillCallback, _rejectCallback) {
        fulfillCallback = _fulfillCallback
        rejectCallback = _rejectCallback
      }
    }
  }
}

let ownPromise = function(asyncCall) {
  let defer = promiseAsyncFunc()
  asyncCall(defer.fulfill, defer.reject)
  return defer.promise
}

// Promise 形式的异步方法调用
ownPromise(function(fulfill, reject) {
  setTimeout(() => {
    var randomNumber = Math.random()
    if (randomNumber > 0.5) fulfill(randomNumber)
    else reject(randomNumber)
  }, 1000)
})

```

**‌第三版 Promise: 支持状态管理**

为了实现规范中对于 Promise 状态变化的要求, 我们需要为 Promise 加入状态管理, 可以利用 Symbol 来表示状态常量

为了判断 Promise 的状态, 我们加入了 fulfill 和 reject 两个方法。并在其中判断 promise 当前状态。如果不是 pending 状态则直接 return(因为 Promise 状态只可能改变一次)。

要实现这样的规范：

- 只允许改变一次状态
- 只能从 pending => fulfilled 或 pending => rejected

```javascript
const PENDING = Symbol('pending')
const FULFILLED = Symbol('fulfilled')
const REJECTED = Symbol('rejected')

// Promise 形式的异步方法定义
var promiseAsyncFunc = function() {
  var status = PENDING
  var fulfillCallback
  var rejectCallback

  return {
    fulfill: function(value) {
      if (status !== PENDING) return
      if (typeof fulfillCallback === 'function') {
        fulfillCallback(value)
        status = FULFILLED
      }
    },
    reject(error) {
      if (status !== PENDING) return
      if (typeof rejectCallback === 'function') {
        rejectCallback(error)
        status = REJECTED
      }
    },
    promise: {
      then: function(_fulfillCallback, _rejectCallback) {
        fulfillCallback = _fulfillCallback
        rejectCallback = _rejectCallback
      }
    }
  }
}

let ownPromise = function(asyncCall) {
  let defer = promiseAsyncFunc()
  asyncCall(defer.fulfill, defer.reject)
  return defer.promise
}

// Promise 形式的异步方法调用
ownPromise(function(fulfill, reject) {
  setTimeout(() => {
    var randomNumber = Math.random()
    if (randomNumber > 0.5) fulfill(randomNumber)
    else reject(randomNumber)
  }, 1000)
}).then(data => console.log(data), err => console.log(err))
```

</pre>
</details>

## Promise.all()
promise.all 是解决并发问题的，多个异步并发获取最终的结果（如果有一个失败则失败）。

```javascript
Promise.all = function(values) {
  if (!Array.isArray(values)) {
    const type = typeof values;
    return new TypeError(`TypeError: ${type} ${values} is not iterable`)
  }
  
  return new Promise((resolve, reject) => {
    let resultArr = [];
    let orderIndex = 0;
    const processResultByKey = (value, index) => {
      resultArr[index] = value;
      if (++orderIndex === values.length) {
          resolve(resultArr)
      }
    }
    for (let i = 0; i < values.length; i++) {
      let value = values[i];
      if (value && typeof value.then === 'function') {
        value.then((value) => {
          processResultByKey(value, i);
        }, reject);
      } else {
        processResultByKey(value, i);
      }
    }
  });
}

```
## Promise.race()

Promise.race 用来处理多个请求，采用最快的（谁先完成用谁的）。

```
Promise.race = function(promises) {
  return new Promise((resolve, reject) => {
    // 一起执行就是for循环
    for (let i = 0; i < promises.length; i++) {
      let val = promises[i];
      if (val && typeof val.then === 'function') {
        val.then(resolve, reject);
      } else { // 普通值
        resolve(val)
      }
    }
  });
}

```


## 防抖和节流

防抖：在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。适用于：输入框联想，防止多次点击提交按钮。
节流：规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。适用于：浏览器resize、onscroll。

```javascript
// 防抖函数
const debounce = (fn, delay) => {
  let timer = null;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
};


// 节流函数
const throttle = (fn, delay = 500) => {
  let flag = true;
  return (...args) => {
    if (!flag) return;
    flag = false;
    setTimeout(() => {
      fn.apply(this, args);
      flag = true;
    }, delay);
  };
};

```

防抖节流演示：

<iframe src="https://codesandbox.io/embed/static-ce05g?fontsize=14" title="static" allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media" style="width:100%; height:700px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

## 实现new操作符

```javascript
/**
 * 模拟实现 new 操作符
 * @param  {Function} ctor [构造函数]
 * @return {Object|Function|Regex|Date|Error}      [返回结果]
 */
function newOperator(ctor){
    if(typeof ctor !== 'function'){
      throw 'newOperator function the first param must be a function';
    }
    // ES6 new.target 是指向构造函数
    newOperator.target = ctor;
    // 1.创建一个全新的对象，
    // 2.并且执行[[Prototype]]链接
    // 4.通过`new`创建的每个对象将最终被`[[Prototype]]`链接到这个函数的`prototype`对象上。
    var newObj = Object.create(ctor.prototype);
    // ES5 arguments转成数组 当然也可以用ES6 [...arguments], Aarry.from(arguments);
    // 除去ctor构造函数的其余参数
    var argsArr = [].slice.call(arguments, 1);
    // 3.生成的新对象会绑定到函数调用的`this`。
    // 获取到ctor函数返回结果
    var ctorReturnResult = ctor.apply(newObj, argsArr);
    // 小结4 中这些类型中合并起来只有Object和Function两种类型 typeof null 也是'object'所以要不等于null，排除null
    var isObject = typeof ctorReturnResult === 'object' && ctorReturnResult !== null;
    var isFunction = typeof ctorReturnResult === 'function';
    if(isObject || isFunction){
        return ctorReturnResult;
    }
    // 5.如果函数没有返回对象类型`Object`(包含`Functoin`, `Array`, `Date`, `RegExg`, `Error`)，那么`new`表达式中的函数调用会自动返回这个新的对象。
    return newObj;
}
```

## 实现bind

**简单版本**：

```javascript
Function.prototype.myBind = function(thisArg) {
  if (typeof this !== 'function') {
    return;
  }
  let _self = this;
  let args = Array.prototype.slice.call(arguments, 1)
  let fnBound = function () {
    // 检测 New
    // 如果当前函数的this指向的是构造函数中的this 则判定为new 操作
    let _this = this instanceof _self ? this : thisArg;
    return _self.apply(_this, args.concat(Array.prototype.slice.call(arguments)));
  }
  // 为了完成 new操作 需要原型链接 
  fnBound.prototype = this.prototype;
  return fnBound;
}


```

<details>
<summary>【点击展开】更高级的实现bind()的方法</summary>
<pre>

**bind高级版本：**

```javascript
// 第三版 实现new调用
Function.prototype.bindFn = function bind(thisArg){
    if(typeof this !== 'function'){
        throw new TypeError(this + ' must be a function');
    }
    // 存储调用bind的函数本身
    var self = this;
    // 去除thisArg的其他参数 转成数组
    var args = [].slice.call(arguments, 1);
    var bound = function(){
        // bind返回的函数 的参数转成数组
        var boundArgs = [].slice.call(arguments);
        var finalArgs = args.concat(boundArgs);
        // new 调用时，其实this instanceof bound判断也不是很准确。es6 new.target就是解决这一问题的。
        if(this instanceof bound){
            // 这里是实现上文描述的 new 的第 1, 2, 4 步
            // 1.创建一个全新的对象
            // 2.并且执行[[Prototype]]链接
            // 4.通过`new`创建的每个对象将最终被`[[Prototype]]`链接到这个函数的`prototype`对象上。
            // self可能是ES6的箭头函数，没有prototype，所以就没必要再指向做prototype操作。
            if(self.prototype){
                // ES5 提供的方案 Object.create()
                // bound.prototype = Object.create(self.prototype);
                // 但 既然是模拟ES5的bind，那浏览器也基本没有实现Object.create()
                // 所以采用 MDN ployfill方案 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create
                function Empty(){}
                Empty.prototype = self.prototype;
                bound.prototype = new Empty();
            }
            // 这里是实现上文描述的 new 的第 3 步
            // 3.生成的新对象会绑定到函数调用的`this`。
            var result = self.apply(this, finalArgs);
            // 这里是实现上文描述的 new 的第 5 步
            // 5.如果函数没有返回对象类型`Object`(包含`Functoin`, `Array`, `Date`, `RegExg`, `Error`)，
            // 那么`new`表达式中的函数调用会自动返回这个新的对象。
            var isObject = typeof result === 'object' && result !== null;
            var isFunction = typeof result === 'function';
            if(isObject || isFunction){
                return result;
            }
            return this;
        }
        else{
            // apply修改this指向，把两个函数的参数合并传给self函数，并执行self函数，返回执行结果
            return self.apply(thisArg, finalArgs);
        }
    };
    return bound;
}
```

</pre>
</details>


## 实现apply和call方法

**简单的ES6实现call**

```javascript
/**
 * @description 使用ES6函数的rest参数和数组的扩展运算符实现call方法
 * @param {Object} context call方法一个指定的this值
 * @param {Object, String, Number, Boolean} context call方法一个指定的this值
 * @returns {Object, String, Number, Boolean} 返回调用函数的值
 */
Function.prototype.call = function(context, ...args) {
  // 使用ES6函数的rest参数(形式为...变量名),args是数组
  // context为null的时候，context为window
  var context = context || window
  // 获取调用call的函数
  context.fn = this
  // 使用ES6扩展运算符（...）执行函数，返回结果
  var result = context.fn(...args)
  // 删除fn属性
  delete context.fn
  // 返回结果
  return result;
}
```

**使用ES3实现call**：

```javascript

/**
 * @description 使用ES3实现call方法
 * @param {Object} context call方法一个指定的this值
 * @returns {Object, String, Number, Boolean} 返回调用函数的值
 */
Function.prototype.call = function (context) {
    // context为null的时候，context为window
    var context = context || window
    // 获取调用call的函数
    context.fn = this
    // 获取call方法的不定长参数
    var args = []
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']')
    }
    // 运行fn函数并返回结果，
    // eval(string)通过计算string得到的值
    var result = eval('context.fn(' + args +')')
    // 删除fn属性
    delete context.fn
    // 返回结果
    return result;
}
/**
 * @description 测试call方法
 * @param {Number} c,d 函数的参宿
 * @returns {Number} 返回add函数的计算结果
 */
function add(c, d){  
  return this.a + this.b + c + d  
}  
var o = {a:1, b:3} 
add.call(o, 5, 7) // 16

```

**ES6实现apply**：

```javascript
/**
 * @description 使用ES6数组的扩展运算符实现apply方法
 * @param {Object} context apply方法一个指定的this值
 * @param {Array} arr apply方法传递给调用函数的参数
 * @returns {Object, String, Number, Boolean} 返回调用函数的值
 */
Function.prototype.apply = function(context, arr) {
  // context为null的时候，context为window
  var context = context || window
  // 获取调用apply的函数
  context.fn = this
  // 使用ES6扩展运算符（...）执行函数，返回结果
  var result = context.fn(...arr)
  // 删除fn属性
  delete context.fn
  // 返回结果
  return result
}
```

**ES3实现apply**：

```javascript
/**
 * @description 使用ES3实现apply方法
 * @param {Object} context apply方法一个指定的this值
 * @param {Array} arr apply方法传递给调用函数的参数
 * @returns {Object, String, Number, Boolean} 返回调用函数的值
 */
Function.prototype.apply= function (context, arr) {
    // context为null的时候，context为window
    var context = context || window
    // 获取调用apply的函数
    context.fn = this
    var result
    // 判断apply是否只有一个参数
    if (!arr) {
      // 执行函数
      result = context.fn();
    } else {
      // 获取参数
      var args = [];
      for (var i = 0, len = arr.length; i < len; i++) {
        args.push('arr[' + i + ']');
      }
      // 执行函数
      result = eval('context.fn(' + args + ')')
    }
    // 删除fn属性
    delete context.fn
    // 返回结果
    return result;
}
/**
 * @description 测试apply方法
 * @param {Number} c,d 函数的参宿
 * @returns {Number} 返回add函数的计算结果
 */
function add(c, d){  
  return this.a + this.b + c + d  
}  
var o = {a:1, b:3} 
add.apply(o, [5, 7]) // 16
```

<details>
<summary>【点击展开】更高级的实现apply()和call()的方法</summary>
<pre>

**apply**

```javascript
// 浏览器环境 非严格模式
function getGlobalObject(){
    return this;
}
function generateFunctionCode(argsArrayLength){
    var code = 'return arguments[0][arguments[1]](';
    for(var i = 0; i < argsArrayLength; i++){
        if(i > 0){
            code += ',';
        }
        code += 'arguments[2][' + i + ']';
    }
    code += ')';
    // return arguments[0][arguments[1]](arg1, arg2, arg3...)
    return code;
}
Function.prototype.applyFn = function apply(thisArg, argsArray){ // `apply` 方法的 `length` 属性是 `2`。
    // 1.如果 `IsCallable(func)` 是 `false`, 则抛出一个 `TypeError` 异常。
    if(typeof this !== 'function'){
        throw new TypeError(this + ' is not a function');
    }
    // 2.如果 argArray 是 null 或 undefined, 则
    // 返回提供 thisArg 作为 this 值并以空参数列表调用 func 的 [[Call]] 内部方法的结果。
    if(typeof argsArray === 'undefined' || argsArray === null){
        argsArray = [];
    }
    // 3.如果 Type(argArray) 不是 Object, 则抛出一个 TypeError 异常 .
    if(argsArray !== new Object(argsArray)){
        throw new TypeError('CreateListFromArrayLike called on non-object');
    }
    if(typeof thisArg === 'undefined' || thisArg === null){
        // 在外面传入的 thisArg 值会修改并成为 this 值。
        // ES3: thisArg 是 undefined 或 null 时它会被替换成全局对象 浏览器里是window
        thisArg = getGlobalObject();
    }
    // ES3: 所有其他值会被应用 ToObject 并将结果作为 this 值，这是第三版引入的更改。
    thisArg = new Object(thisArg);
    var __fn = '__' + new Date().getTime();
    // 万一还是有 先存储一份，删除后，再恢复该值
    var originalVal = thisArg[__fn];
    // 是否有原始值
    var hasOriginalVal = thisArg.hasOwnProperty(__fn);
    thisArg[__fn] = this;
    // 9.提供 `thisArg` 作为 `this` 值并以 `argList` 作为参数列表，调用 `func` 的 `[[Call]]` 内部方法，返回结果。
    // ES6版
    // var result = thisArg[__fn](...args);
    var code = generateFunctionCode(argsArray.length);
    var result = (new Function(code))(thisArg, __fn, argsArray);
    delete thisArg[__fn];
    if(hasOriginalVal){
        thisArg[__fn] = originalVal;
    }
    return result;
};
```

**call**


```javascript
Function.prototype.callFn = function call(thisArg){
    var argsArray = [];
    var argumentsLength = arguments.length;
    for(var i = 0; i < argumentsLength - 1; i++){
        // argsArray.push(arguments[i + 1]);
        argsArray[i] = arguments[i + 1];
    }
    console.log('argsArray:', argsArray);
    return this.applyFn(thisArg, argsArray);
}
```

</pre>
</details>

## 大数相加

```javascript
function addBigNum(a,b){
  let res = ''
      loc = 0
  a = a.split('')
  b = b.split('')
  while(a.length || b.length || loc){
    //~~把字符串转换为数字，用~~而不用parseInt，是因为~~可以将undefined转换为0，当a或b数组超限，不用再判断undefined
    //注意这里的+=，每次都加了loc本身，loc为true，相当于加1，loc为false，相当于加0
    loc += ~~a.pop() + ~~b.pop()
    //字符串连接，将个位加到res头部
    res = (loc % 10) + res
    //当个位数和大于9，产生进位，需要往res头部继续加1，此时loc变为true，true + 任何数字，true会被转换为1
    loc = loc > 9
  }
  return res.replace(/^0+/,'')
}

```
## 大数相乘

时间复杂度O(n^2)

```
const multiply = (num1, num2) => {
  const len1 = num1.length;
  const len2 = num2.length;
  const pos = new Array(len1 + len2).fill(0);

  for (let i = len1 - 1; i >= 0; i--) {
    const n1 = +num1[i];
    for (let j = len2 - 1; j >= 0; j--) {
      const n2 = +num2[j];
      const multi = n1 * n2;             
      const sum = pos[i + j + 1] + multi; 

      pos[i + j + 1] = sum % 10;
      pos[i + j] += sum / 10 | 0;
    }
  }
  while (pos[0] == 0) {
    pos.shift();
  }
  return pos.length ? pos.join('') : '0';
};

```

## 函数链式调用/自调用

实现:
- add(1)(2)(3).print()//6
- add(1)(2)(3)//6
- add(1)(2)//3


思路：

创建闭包，将变量保存在内存中，如果直接输出，则重写toString()返回结果;如果通过print()则写成函数属性

```javascript
function add(num){
  let total = num

  function _add (n) {
    total +=  n
    return _add
  }

  // 直接返回最终结果
  _add.toString=function () {
    return total
  }

  // 通过print()返回最终结果
  _add.print = function () {
    return total
  }

  return _add
}

console.log(add(1)(2)(3).print())//6
console.log(add(1)(2)(3))//6
console.log(add(1)(2))//3
```
## 实现一个axios

**思路：**
axios还是属于 XMLHttpRequest， 因此需要实现一个ajax。或者基于http 。还需要一个promise对象来对结果进行处理。

`myAxios.js`

```javascript
class Axios {
    constructor() {

    }

    request(config) {
        return new Promise(resolve => {
            const {url = '', method = 'get', data = {}} = config;
            // 发送ajax请求
            const xhr = new XMLHttpRequest();
            xhr.open(method, url, true);
            xhr.onload = function() {
                console.log(xhr.responseText)
                resolve(xhr.responseText);
            }
            xhr.send(data);
        })
    }
}

// 最终导出axios的方法，即实例的request方法
function CreateAxiosFn() {
    let axios = new Axios();
    let req = axios.request.bind(axios);
    return req;
}

// 得到最后的全局变量axios
let axios = CreateAxiosFn();

```

然后可以在html上进行测试

```html
//index.html
<script type="text/javascript" src="./myAxios.js"></script>
<body>
<button class="btn">点我发送请求</button>
<script>
    document.querySelector('.btn').onclick = function() {
        // 分别使用以下方法调用，查看myaxios的效果
        axios({
          method: 'get',
          url: 'http://localhost:5000/getTest'
        }).then(res => {
          console.log('getAxios 成功响应', res);
        })
    }
</script>
</body>
```


## 观察者模式

观察者直接订阅目标，当目标触发事件时，通知观察者进行更新

简单实现

```javascript
class Observer {
  constructor(name) {
    this.name = name;
  }

  update() {
    console.log(`${this.name} update`)
  }
}

class subject {
  constructor() {
    this.subs = [];
  }

  add(observer) {
    this.subs.push(observer);
  }

  notify() {
    this.subs.forEach(item => {
      item.update();
    });
  }
}

const sub = new subject();
const ob1 = new Observer('ob1');
const ob2 = new Observer('ob2');

// 观察者订阅目标
sub.add(ob1);
sub.add(ob2);

// 目标触发事件
sub.notify();
```
## 手写发布者-订阅者模式

发布订阅模式通过一个调度中心进行处理，使得订阅者和发布者分离开来，互不干扰。

简单实现：

```javascript
class Event {
  constructor() {
    this.lists = new Map();
  }

  on(type, fn) {
    if (!this.lists.get(type)) {
      this.lists.set(type, []);
    }

    this.lists.get(type).push(fn);
  }

  emit(type, ...args) {
    const arr = this.lists.get(type);
    arr && arr.forEach(fn => {
      fn.apply(null, args);
    });
  }
}

const ev = new Event();

// 订阅
ev.on('msg', (msg) => console.log(msg));

// 发布
ev.emit('msg', '发布');
```

另一种实现：

```javascript
// 事件对象
let eventEmitter = {};

// 缓存列表，存放 event 及 fn
eventEmitter.list = {};

// 订阅
eventEmitter.on = function (event, fn) {
  let _this = this;
  // 如果对象中没有对应的 event 值，也就是说明没有订阅过，就给 event 创建个缓存列表
  // 如有对象中有相应的 event 值，把 fn 添加到对应 event 的缓存列表里
  (_this.list[event] || (_this.list[event] = [])).push(fn);
  return _this;
};

// 发布
eventEmitter.emit = function () {
  let _this = this;
  // 第一个参数是对应的 event 值，直接用数组的 shift 方法取出
  let event = [].shift.call(arguments),
    fns = [..._this.list[event]];
  // 如果缓存列表里没有 fn 就返回 false
  if (!fns || fns.length === 0) {
    return false;
  }
  // 遍历 event 值对应的缓存列表，依次执行 fn
  fns.forEach(fn => {
    fn.apply(_this, arguments);
  });
  return _this;
};

function user1 (content) {
  console.log('用户1订阅了:', content);
};

function user2 (content) {
  console.log('用户2订阅了:', content);
};

// 订阅
eventEmitter.on('article', user1);
eventEmitter.on('article', user2);

// 发布
eventEmitter.emit('article', 'Javascript 发布-订阅模式');

/*
    用户1订阅了: Javascript 发布-订阅模式
    用户2订阅了: Javascript 发布-订阅模式
*/
```

## 手撸一个事件机制

**关键词：发布-订阅模式**

其实核心就是维护一个对象，对象的 key 存的是事件 type，对应的 value 为触发相应 type 的回调函数，即 listeners，然后 trigger 时遍历通知，即 forEach 进行回调执行。

```javascript
class EventTarget {
  constructor() {
    this.listeners = {}; // 储存事件的对象
  }

// 注册事件的回调函数
  on(type, callback) {
    if (!this.listeners[type]) this.listeners[type] = []; // 如果是第一次监听该事件，则初始化数组
      this.listeners[type].push(callback);
  }
 // 注册事件的回调函数，只执行一次
  once(type, callback) {
   if (!this.listeners[type]) this.listeners[type] = [];
      callback._once = true; // once 只触发一次，触发后 off 即可
      this.listeners[type].push(callback);
  }
// 删除一个回调函数
  off(type, callback) {
    const listeners = this.listeners[type];
    if (Array.isArray(listeners)) {
      // filter 返回新的数组，会每次对 this.listeners[type] 分配新的空间
      // this.listeners[type] = listeners.filter(l => l !== callback);
      const index = listeners.indexOf(callback); // 根据 type 取消对应的回调
      this.listeners[type].splice(index, 1); // 用 splice 要好些，直接操作原数组

      if (this.listeners[type].length === 0) delete this.listeners[type]; // 如果回调为空，删除对该事件的监听
    }
  }
 // 触发注册的事件回调函数执行
  trigger(event) {
    const { type } = event; // type 为必传属性
    if (!type) throw new Error('没有要触发的事件！');

    const listeners = this.listeners[type]; // 判断是否之前对该事件进行监听了
    if (!listeners) throw new Error(`没有对象监听 ${type} 事件！`);

    if (!event.target) event.target = this;

    listeners.forEach(l => {
      l(event);
      if (l._once) this.off(type, l); // 如果通过 once 监听，执行一次后取消
    });
  }
}

// 测试
function handleMessage(event) { 
console.log(`message received: ${ event.message }`); 
}

function handleMessage2(event) {
console.log(`message2 received: ${ event.message }`); 
 }

const target = new EventTarget();

target.on('message', handleMessage);
target.on('message', handleMessage2);
target.trigger({ type: 'message', message: 'hello custom event' }); // 打印 message，message2

target.off('message', handleMessage);
target.trigger({ type: 'message', message: 'off the event' }); // 只打印 message2

target.once('words', handleMessage);
target.trigger({ type: 'words', message: 'hello2 once event' }); // 打印 words
target.trigger({ type: 'words', message: 'hello2 once event' }); // 报错：没有对象监听 words 事件！
```

## 实现一个sleep函数

```javascript
//Promise
const sleep = time => {
  return new Promise(resolve => setTimeout(resolve,time))
}
sleep(1000).then(()=>{
  console.log(1)
})

//Generator
function* sleepGenerator(time) {
  yield new Promise(function(resolve,reject){
    setTimeout(resolve,time);
  })
}
sleepGenerator(1000).next().value.then(()=>{console.log(1)})

//async
function sleep(time) {
  return new Promise(resolve => setTimeout(resolve,time))
}
async function output() {
  let out = await sleep(1000);
  console.log(1);
  return out;
}
output();

//ES5
function sleep(callback,time) {
  if(typeof callback === 'function')
    setTimeout(callback,time)
}

function output(){
  console.log(1);
}
sleep(output,1000);
```

## 实现add(1)(2,3)(1)//6函数

```javascript
function add(){
	let args = [...arguments];
	let addfun = function(){
		args.push(...arguments);
		return addfun;
	}
	addfun.toString = function(){
		return args.reduce((a,b)=>{
			return a + b;
		});
	}
	return addfun;
}

add(1); 	// 1
add(1)(2);  	// 3
add(1)(2)(3)；  // 6
add(1)(2, 3);   // 6
add(1, 2)(3);   // 6
add(1, 2, 3);   // 6
```

## 控制最大(请求)并发数量

假设最大并发数是n，那就通过`n--`发n个异步请求，同时将任务数组arr传进去，并通过shift()取arr首元素，虽然异步调用的顺序不同，但操作的是同一个数组，然后将元素进行处理之后，判断当前数组是否为空，否则按照上面的方法进行递归，知道数组为空为止。

```javascript
/**
 * @params list {Array} - 要迭代的数组
 * @params limit {Number} - 并发数量控制数
 * @params asyncHandle {Function} - 对`list`的每一个项的处理函数，参数为当前处理项，必须 return 一个Promise来确定是否继续进行迭代
 * @return {Promise} - 返回一个 Promise 值来确认所有数据是否迭代完成
 */
let mapLimit = (list, limit, asyncHandle) => {
  let recursion = (arr) => {
    return asyncHandle(arr.shift()).then(() => {
      if (arr.length !== 0) return recursion(arr)   // 数组还未迭代完，递归继续进行迭代
      else return 'finish'
    })
  }

  let listCopy = [].concat(list)
  let asyncList = [] // 正在进行的所有并发异步操作
  while (limit--) {
    asyncList.push(recursion(listCopy))
  }
  return Promise.all(asyncList)  // 所有并发异步操作都完成后，本次并发控制迭代完成
}
```

以下是手动测试

```
let list = [1, 2, 3, 4, 5,4,333,222,122,23,54,64]
let limit = 3//限制的并发数量
let count = 0//记数当前并发量

let callback = (curItem) => {
  return new Promise(resolve => {
    count++
    setTimeout(() => {
      console.log(curItem, '当前并发量:', count--)
      resolve()
    }, Math.random() * 5000)
  })
}

mapLimit(list, limit, callback)
//完成之后
  .then(()=>{
    console.log('所有并发执行完成')
  })
```

## 实现instanceof

instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。

```javascript
//instanceof的使用方法
function Car() {}
const benz = new Car();
console.log(benz instanceof Car);//true
console.log(auto instanceof Object);//true
```

实现instance_of(L,R)

```javascript
function instance_of(L, R) {//L 表示左表达式，R 表示右表达式 
    var O = R.prototype;   // 取 R 的显示原型 
    L = L.__proto__;  // 取 L 的隐式原型
    while (true) {    
        if (L === null)      
             return false;   
        if (O === L)  // 当 O 显式原型 严格等于 L隐式原型 时，返回true
             return true;   
        L = L.__proto__;  
    }
}
```

## 手写一个jsonp

```javascript
//根据指定的URL发送一个JSONP请求
//然后把解析得到的响应数据传递给回调函数
//在URL中添加一个名为jsonp的查询参数，用于指定该请求的回调函数的名称
function getJSONP(url, callback) { //为本次请求创建一个唯一的回调函数名称
    var cbnum = "cb" + getJSONP.counter++; //每次自增计数器
    var cbname = "getJSONP." + cbnum; //作为JSONP函数的属性
    //将回调函数名称以表单编码的形式添加到URL的查询部分中
    //使用jsonp作为参数名，一些支持JSONP的服务
    //可能使用其他的参数名，比如callback
    if (url.indexOf("?") === -1) //URL没有查询部分
        url += "?jsonp=" + cbname; //作为查询部分添加参数
    else //否则
        url += "＆jsonp=" + cbname; //作为新的参数添加它
    //创建script元素用于发送请求
    var script = document.createElement("script"); //定义将被脚本执行的回调函数
    getJSONP[cbnum] = function (response) {
        try {
            callback(response); //处理响应数据
        } finally { //即使回调函数或响应抛出错误
            delete getJSONP[cbnum]; //删除该函数
            script.parentNode.removeChild(script); //移除script元素
        }
    }; //立即触发HTTP请求
    script.src = url; //设置脚本的URL
    document.body.appendChild(script); //把它添加到文档中
}
getJSONP.counter = 0; //用于创建唯一回调函数名称的计数器
```
