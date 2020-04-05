

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


## 实现生物上的S型增长曲线

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



