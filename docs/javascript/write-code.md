
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

/**
 * 数组乱序
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
