

正文是讲JavaScript的八大继承、但我仍想和Java对比，最终我获得了一些启示
> &emsp;&emsp;其实这篇文章不是真的要比较出什么，只是我想从java和js设计思想上找到些什么。或许是理念，或许是一种思维，不管如何，可能真正体会到那种“境界”，还需要在未来不断地学习和理解语言，深入浅出，方臻此境。
> 如果你已经理解了JavaScript的继承，可以直接看文章底部的心得。



## 为什么要将JavaScript的继承和Java的继承做比较？
&emsp;&emsp;Java作为一个面向对象的语言，继承机制浑然天成。而JavaScript的继承，更多情况下是在想尽办法“弥补”没有实现“面向对象”的遗憾。
> 详可参见[Javascript继承机制的设计思想](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)


## Java是如何实现继承的？
通过一个`extends`关键字，很直观地表现出继承关系。

```java
class Father {
    void work() {
        System.out.println("刷碗");
    }
}

class Son extends Father {

}

class Main {
    public static void main(String[] args) {
        Father father = new Father();
        Son son = new Son();

        father.work();//刷碗
        son.work();//刷碗

    }
}
```
&emsp;&emsp;下面即将介绍的所有JavaScript继承的写法，都拿来和Java作一次比较和体会。
&emsp;&emsp;关于对比，我主要考虑三个方面，“简洁性”、“通俗性”和“全面性”。简洁和通俗的继承能让人直观理解，更有利于代码逻辑的书写，不影响构思；而全面性，是能真正把继承的优势给发挥出来。
## JavaScript是如何实现继承的？

### 原型链继承
&emsp;&emsp;js中new实例的过程就是通过prototype的构造函数来创建实例对象，所以继承的本质就是**重写原型对象，代之以一个新类型的实例**。
> 如果不理解，请参阅[什么是原型链、protetype、constructor？](https://blog.csdn.net/cc18868876837/article/details/81211729)

```javascript
//父亲的构造函数
function Father() {
}

//给父添加方法
Father.prototype.work = function () {
    console.log('刷碗');
};

//儿子的构造函数
function Son() {
}

//将父亲的实例覆盖儿子的原型对象，这样儿子的实例就继承了父亲
Son.prototype = new Father();

new Father().work();//刷碗
new Son().work();//刷碗
```

&emsp;&emsp;总体来说，和java相比，这种方式是比较松散的，看起来也比较乱。和Java继承在某种程度上比较，原型链继承中JS把方法的定义抽出来，然后利用原型对象机制连接父和子。
### 借用构造函数来继承(经典继承)
&emsp;&emsp;使用父类的构造函数来增强子类实例，等同于复制父类的实例给子类（不使用原型）

```javascript
//父亲的构造函数
function Father() {
    this.hobby = ['吃饭','睡觉'];
}
//儿子的构造函数
function Son() {
    Father.call(this);
}

console.log(new Father().hobby);
console.log(new Son().hobby);
```

&emsp;&emsp;核心代码是`Father.call(this)`，创建子类实例时调用`Father`构造函数，于是`Son`的每个实例都会将`Father`中的属性复制一份。换种方式理解，通过call将Father的实例里面的this指向Son。不过这种只能继承父类**实例**的属性和方法，不能继承原型的属性和方法。这里和java没什么关系，就不再对比。
### 组合继承
&emsp;&emsp;组合上述两种方法就是组合继承。用原型链实现对**原型**属性和方法的继承，用借用构造函数技术来实现**实例**属性的继承。
```javascript
//父亲的构造函数
function Father() {
    this.hobby = ['吃饭','睡觉'];
}

//给父添加方法
Father.prototype.work = function () {
    console.log('刷碗');
};

//儿子的构造函数
function Son() {
    Father.call(this);
}

Son.prototype = new Father();
Son.prototype.constructor = Son;

let father = new Father();
let son = new Son();
console.log(father.hobby);
console.log(son.hobby);
father.work();//刷碗
son.work();//刷碗
```

&emsp;&emsp;组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为`JavaScript`中最常用的继承模式。但是我个人觉得，即使这样，对于代码来说仍然显得松散，不能像Java继承一样让人直观理解。
### 原型式继承
&emsp;&emsp;利用一个空对象作为中介，将某个对象直接赋值给空对象构造函数的原型。

```javascript
function Object(obj) {
    function Son() {}
    Son.prototype = obj;
    return new Son();
}

function Father() {
    this.hobby = ['吃饭','睡觉'];
}

let father = new Father();
let son = Object(new Father());

console.log(father.hobby);
console.log(son.hobby);
```

&emsp;&emsp;`object()`对传入其中的对象执行了一次`浅复制`，将构造函数Son的原型直接指向传入的对象。另外，ES5中存在`Object.create()`的方法，能够代替上面的`object`方法。

&emsp;&emsp;和其他的继承方式相比，代码明显简洁了很多，但从理解上仍然不是那么直观，所以我们接着往下看。

### 寄生式继承
&emsp;&emsp;核心：在原型式继承的基础上，增强对象，返回构造函数。主要作用就是为构造函数新增属性和方法，以**增强函数**。
```javascript
function createAnother(original){
    var clone = Object(original); // 通过调用 object() 函数创建一个新对象
    clone.sayHi = function(){  // 以某种方式来增强对象
        console.log("hi");
    };
    return clone; // 返回这个对象
}


function Object(obj) {
    function Son() {}
    Son.prototype = obj;
    return new Son();
}

function Father() {
    this.hobby = ['吃饭','睡觉'];
}

let father = new Father();
let son = createAnother(new Father());

console.log(father.hobby);
console.log(son.hobby);
son.sayHi();
```

&emsp;&emsp;说白了，就是原型式继承的升级版，的确达到了增强函数的目的，但是代码显得仍然有点复杂，不够直观，并且无法传递参数。
### 寄生组合式继承
&emsp;&emsp;这是才是大哥。寄生组合式继承是借用构造函数传递参数和寄生模式实现继承。几乎是融合了以上所有的JS继承的优点。

```javascript
function inheritPrototype(son, father){
    let prototype = Object.create(father.prototype); // 创建对象，创建父类原型的一个副本
    prototype.constructor = son;                    // 增强对象，弥补因重写原型而失去的默认的constructor 属性
    son.prototype = prototype;                      // 指定对象，将新创建的对象赋值给子类的原型
}

// 父类初始化实例属性和原型属性
function father(name){
    this.name = name;
    this.hobby = ["吃饭", "睡觉"];
}
father.prototype.sayName = function(){
    console.log(this.name);
};

// 借用构造函数传递增强子类实例属性（支持传参和避免篡改）
function son(name, age){
    father.call(this, name);
    this.age = age;
}

// 将父类原型指向子类
inheritPrototype(son, father);

// 新增子类原型属性
son.prototype.sayAge = function(){
    console.log(this.age);
};

let instance1 = new son("儿子1", 23);
let instance2 = new son("儿子2", 23);

instance1.hobby.push("打豆豆");

console.log(instance1.hobby);// ["吃饭", "睡觉"，"打豆豆"]
console.log(instance2.hobby);// ["吃饭", "睡觉"]
```
&emsp;&emsp;这是最成熟的方法，也是现在很多库实现的方法。这位老大哥继承方式高效、灵活。但是缺点也一目了然，尤其是对于初学者来说，很容易望而却步。

### 混入方式实现多继承
```javascript
function MyClass() {
     SuperClass.call(this);
     OtherSuperClass.call(this);
}

// 继承一个类
MyClass.prototype = Object.create(SuperClass.prototype);
// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);
// 重新指定constructor
MyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
     // do something
};
```

&emsp;&emsp;`Object.assign`会把 `OtherSuperClass`原型上的函数拷贝到 `MyClass`原型上，使 `MyClass` 的所有实例都可用 `OtherSuperClass` 的方法。
&emsp;&emsp;不得不说，JS的的确确实现了继承：“让子类对象具有父类的属性和方法”。然而，却都不能睥睨Java的继承实现，做到简洁、直观、通俗、全面。
&emsp;&emsp;难道JS中真的没有一种能和面向对象的Java的继承睥睨打方法吗？
&emsp;&emsp;这时，ES6带着class来了。

### ES6中class和extends
&emsp;&emsp;extends关键字主要用于类声明或者类表达式中，以创建一个类，该类是另一个类的子类。其中constructor表示构造函数，一个类中只能有一个构造函数，有多个会报出SyntaxError错误,如果没有显式指定构造方法，则会添加默认的 constructor方法。

> 这段定义几乎和java一模一样，稍有区别的是，js中`constructor()`是构造函数，java中`类名()`就是构造函数。

```javascript
class Father{
    constructor(name){
        this.name = name;
    }

    work(){
        console.log(this.name+'刷碗');
    }
}

class Son extends Father{
}

new Son('儿子').work();
```
&emsp;&emsp;如果你是第一次见到class和extends来实现继承，你可能会和我一样惊讶。这和Java几乎吻合了，通过这种语法，成功地让JavaScript好像拥有"类"、“面向对象”的真实概念了。也做到了Java一样简洁、直观、全面。
&emsp;&emsp;extends的核心代码，实际上就是包装了老大哥（寄生组合式继承），巧妙的实现了这种通俗易懂的逻辑。

## 心得
&emsp;&emsp;从JavaScript问世以来，好像后人一直在“补坑”中，实现继承也好，安全漏洞也好，至少从结果来看，这门语言在全世界的努力下，并没有没落下去，反而因为它的“松散”慢慢发展了更多的分支。随着编程世界的发展，出现了Node、出现了npm、出现了TypeScript等等，让JavaScript从当年的一个“小毛孩”，变成一个“大男孩”了。

&emsp;&emsp;返璞归真，一门语言是为了需求而服务的，能生存下去，也是让自己不断更新来顺应新的需求。就拿js的继承来说，从"原型链继承"到成长为"寄生组合式继承"，最后到"extends继承"，这是时代的产物。不难想象，JavaScript在未来的发展方向只有一个，**更方便、更灵活、更人性化来实现需求**。

&emsp;&emsp;相信看这篇文章的朋友都是和我一样,在走这条路，那么对于我们个人来说，能有什么**启示**呢？

&emsp;&emsp;对于我来说，如果我们也想在今后的路中游刃有余，也不能止于现状，要顺应时代的发展而自我发展。举个简单的例子，我们要根据自己的职业和方向来构建自己的知识体系、打造方法论，甚至写出一套方便工作的“框架”。如果你真的要输出1-10，你要考虑写个循环而不是switch，用更短、更简洁、更人性化的代码来打造这个世界。

&emsp;&emsp;这里突然想到产品的交互式设计，也跟大家分享一下：功能手机正在淘汰，因为它不如智能机的功能更加方便人的生活；老式电视遥控器正在被新遥控器淘汰，老式功能机功能按键又多又复杂，新遥控器仅有的几个按键刚好满足了人们95%的需求，剩下5%在电视设置里也能找到。**功能不在多少，合适就好**。对我们人来说，可以让生活和工作都变得“刚好合适”，对于JavaScript语言来说，不同的分支也能各自发展出“刚好合适”的地步。

&emsp;&emsp;不固执历史、不停止当下、不畏惧发展。

&emsp;&emsp;Come on！World！

&emsp;&emsp;Come on！Me！
