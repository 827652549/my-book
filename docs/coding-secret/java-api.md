使用Java进行算法编程常用到的API；
### 基础知识
#### 创建数组

```java
int[] i = new int[arr.length];
```
#### ASCII
|符号|十进制|
|-|-|
|数字0|48|
|A|65|
|a|97|
|空格|32|

**A+" "=a**
#### 判断是否除尽
num%i==0 除尽
#### 判断十位数和个位数的数字
十位数：num/10(利用int/int向下取整的特点)

个位数：num%10
#### 栈
```java
Stack<Integer> stack = new Stack<>();//创建一个栈，存储Integer型
stack.push(2);//添加2入栈中
stack.pop();//取出一个数
stack.isEmpty();//栈空则返回true
```

### API技巧
- 使用StringBuilder进行字符串反转,再转String

```java
String str = new StringBuilder("123");//转化为StringBuilder
str.reverse();//字符串反转
String strResult = str + "";//StringBuilder转String
```

- 使用split(String 正则)进行字符串分割,返回String[]。注意做好测试，因为分割后末尾端的“”不会被添加到数组中。如果需要去掉首位空格，请用trim();


```java
String str = "1!2!3";
str.split("!");
```

- 使用toCharArray
> 注意处理单字符使用，不然“10”会被搞成“1”，“0”


- 使用subString(beginIndex，endIndex)

注意
beginIndex -- 起始索引（包括）, 索引从 0 开始。

endIndex -- 结束索引（不包括）。


- 十进制转二进制
Integer.toBinaryString(value)

传入一个十进制数value，返回一个表示二进制的tring

- int转char
(char)(number+'a'-97)
