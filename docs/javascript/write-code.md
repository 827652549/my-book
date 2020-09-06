
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

//使用递归的方式实现数组、对象的深拷贝
function deepClone1(obj) {
  //判断拷贝的要进行深拷贝的是数组还是对象，是数组的话进行数组拷贝，对象的话进行对象拷贝
  var objClone = Array.isArray(obj) ? [] : {};
  //进行深拷贝的不能为空，并且是对象或者是
  if (obj && typeof obj === "object") {
    for (key in obj) {
        if (obj[key] && typeof obj[key] === "object") {
          objClone[key] = deepClone1(obj[key]);
        } else {
          objClone[key] = obj[key];
        }
    }
  }
  return objClone;
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

