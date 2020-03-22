
## 循环输出setTimeout
```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function (i) {
        console.log(i);
    },1000)
}
//5 5 5 5 5
```

如果想让按顺序输出怎么输出？

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
