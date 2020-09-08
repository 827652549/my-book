

由于带上题解文件篇幅过长，所以通过detail标签将部分答案隐藏在折叠区，需要的同学可以展开查看答案：

?> 这一部分的内容主要还是以理解和掌握为主，面试官更多还是考察你的基础知识的运用。

## 循环输出setTimeout
```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function (i) {
        console.log(i);
    },1000)
}
//5 5 5 5 5
```


<details>
<summary>【点击展开】怎么实现按顺序输出怎么输出？</summary>
<pre>



将var改为let

```javascript
for (let i = 0; i < 5; i++) {
    setTimeout(function (i) {
        console.log(i);
    },1000)
}
```

立即执行函数

```javascript
for (var i = 0; i < 5; i++) {
    (function (i) {
        setTimeout(function () {
            console.log(i);
        },1000)
    })(i)
}
```

bind

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function (i) {
        console.log(i);
    }.bind(this,i),1000)
}
```

call（bind返回的是函数，而call、apply是立即调用，所以写法要变化）

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function (i) {
        return function () {
            console.log(i);
        }
    }.call(this,i),1000)
}
```

apply

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function (i) {
        return function () {
            console.log(i);
        }
    }.apply(this,[i]),1000)
}
```

闭包

```javascript
for (var i = 0; i < 5; i++) {
    function f(){
        var j = i;
        return function () {
            setTimeout(function () {
                console.log(j);
            },1000)
        }
    }
    f()();
}
```

setTimeout第三个参数

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function (i) {
        console.log(i);
    }, 1000,i)
}
```



</pre>
</details>


## 连等赋值a=b=c

```
var a = {n:1};
a.x = a = {n:2};
console.log(a.x); // 为什么输出undefined？
```

首先可以确定，连续赋值是从右至左永远只取等号右边的表达式结果赋值到等号左侧。

- 1、在执行前，会先将a和a.x中的a的引用地址都取出来，此值他们都指向{n:1}
- 2、在内存中创建一个新对象{n:2}
- 3、执行a={n:2}，将a的引用从指向{n:1}改为指向新的{n:2}
- 4、执行a.x=a，此时a已经指向了新对象，而a.x因为在执行前保留了原引用，所以a.x的a依然指向原先的{n:1}对象，所以给原对象新增一个属性x，内容为{n:2}也就是现在a
- 5、语句执行结束，原对象由{n:1}变成{n:1,x:{n:2}}，而原对象因为无人再引用他，所以被GC回收，当前a指向新对象{n:2}
- 6、再执行a.x，自然就是undefined了

**‌尽量不要使用JS的连续赋值操作**

## 代码实现浏览器事件循环event-loop

思路：

- 主线程=>微任务=>宏任务
- 遇setTimeout、setInterval放入到宏任务中
- new Promise构造函数的内容作为主线程，遇到resolve()则把then的内容放入到微任务中，构造函数中resolve()后的内容继续执行
- async关键字将函数封装成Promise，函数里遇到await则相当于对应的异步函数转化为new Promise构造函数，依据上面的规则判断

```javascript
async function async1(){
    console.log('async1 start')
    await async2()
    console.log('async1 end')
}

async function async2(){
    console.log('async2')
}

console.log('script start')

setTimeout(function(){
    console.log('setTimeOut')
}, 0)

async1()

new Promise(function(resolve){
    console.log('promise1') 
    resolve()
}).then(function(){
    console.log('promise2') 
})

console.log('script end')

//script start
//async1 start
//async2
//promise1
//script end
//async1 end
//promise2
//setTimeOut
```
