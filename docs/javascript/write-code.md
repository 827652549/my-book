
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
