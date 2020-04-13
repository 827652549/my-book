

由于带上题解文件篇幅过长，所以通过detail标签将部分答案隐藏在折叠区，需要的同学可以展开查看答案：

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




