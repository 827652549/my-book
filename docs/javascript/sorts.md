
## 介绍
&emsp;&emsp;排序算法是《数据结构与算法》中最基本的算法之一。

> tips：如果你是第一次学习算法，大可不必顾虑本教程是使用JavaScript语言进行编码，因为算法只是一种思想逻辑，你当然可以用你喜欢的语言来实现算法。

>所以，**学习算法的关键**是能有一篇浅显易懂的讲解教程，是骡子是马，看我拉出来溜溜。

当然，我建议你先了解关于排序算法的一些基础知识：

### 什么是排序算法？
&emsp;&emsp;排序算法可以分为内部排序和外部排序，内部排序是数据记录在内存中进行排序，而外部排序是因排序的数据很大，一次不能容纳全部的排序记录，在排序过程中需要访问外存。常见的内部排序算法有：插入排序、希尔排序、选择排序、冒泡排序、归并排序、快速排序、堆排序、基数排序等。用一张图概括：

![十大排序算法汇总](https://www.runoob.com/wp-content/uploads/2019/03/sort.png)

名词解释：

- n：数据规模

- k："桶"的个数

- In-place：占用常数内存，不占用额外内存

- Out-place：占用额外内存

- 稳定性：排序后 2 个相等键值的顺序和排序之前它们的顺序相同

> [In-place和Out-place是什么意思？](https://blog.csdn.net/HuoYiHengYuan/article/details/104479754)


### 什么是时间复杂度？
这里我们着重讨论**时间复杂度**，至于空间复杂度请自行Google。

&emsp;&emsp;计算时间复杂度注意：

 1. 去掉系数，再去掉低阶项，再去掉常数项
 2. 如果是常数，直接为O(1)
 3. n代表一种数据级别，我们在讨论算法快不快的时候默认n取得是正无穷

&emsp;&emsp;我们接下来只讨论**平均时间**和**最坏情况**复杂度，因为最好情况的复杂度一般没有参考意义，除非你能保证数据能够达成最好情况的要求。

### 什么是算法稳定性？
&emsp;&emsp;如果两个具有相同键的对象在排序后的输出中以相同的顺序出现，则它们出现在要排序的输入数组中。有些排序算法本质上是稳定的，如插入排序、合并排序、气泡排序等，而有些排序算法则不稳定，如堆排序、快速排序等。

背景：

&emsp;&emsp;“稳定”排序算法使具有相同排序键的项保持顺序。假设我们有一个由5个字母组成的单词列表：

- peach
- straw
- apple
- spork

&emsp;&emsp;如果我们只按每个单词的第一个字母对列表进行排序，那么稳定的排序就会产生：

- apple
- peach
- straw
- spork


&emsp;&emsp;在不稳定排序算法，straw或spork可能是互换的，但在稳定的情况下，它们保持在相同的相对位置上(也就是说，因为straw出现在前面spork在输入中，它也出现在前面。spork在输出中)。

&emsp;&emsp;我们可以使用该算法对单词列表进行排序：按列5、4、3、2、1进行稳定排序，最后将其正确排序。让自己相信这一点。(顺便说一句，该算法称为基排序)

&emsp;&emsp;假设我们有一个名字和姓氏的列表。我们被要求“按姓氏，然后按第一名”排序。我们可以先根据名字进行排序(稳定的或不稳定的)，然后根据姓氏进行稳定的排序。在这些排序之后，列表主要按照姓氏进行排序。但是，在姓氏相同的地方，名字是排序的。

&emsp;&emsp;你不能以同样的方式堆叠不稳定的种类。
## 算法
&emsp;&emsp;以下排序算法皆是用JavaScript实现。
### 冒泡排序
&emsp;&emsp;冒泡排序是一种简单直观的排序算法，它重复性地访问要排序的数列。通过在访问过程中不断地判断和交换相邻数列，从而使最大/小的数往末端转移，最终得到有序数列。

#### 思路

1. 从前两个元素`arr[0]`,`arr[1]`开始，按照顺序两两比较相邻的元素，如果顺序错误就交换。
2. 当第一遍全部比较一遍之后，整个数列中最大的数就会被交换到最后到位置`arr[length-1]`。
3. 第二遍我们重复步骤1的交换，比较到倒数第二个元素位置，因为最末元素已经确定是最终结果了，所以没必要再进行比较处理。
4. 像步骤3一样，接下来的每一次都重复比较，每次比较都会少比较一次。
5. 当完成最后两个数的比较之后，冒泡排序结束。

#### 动画演示
![冒泡排序动画](https://www.runoob.com/wp-content/uploads/2019/03/bubbleSort.gif)

![“冒泡”示意图](https://img-blog.csdnimg.cn/20190102171133188.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pob3U5MjA3ODYzMTI=,size_16,color_FFFFFF,t_70)
#### 代码实现
```javascript
/**
 * 冒泡排序
 * @param {Array} arr 传入一个数组,按照从小到大排序
 * @returns {Array} 返回排序后的数组
 */
let bubbleSort = (arr) => {
    let len = arr.length;
    let temp;
    for (let i = 0; i < len - 1; i++) {
        for (let j = 0; j < len - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
    new Array(arr.length);
    return arr;
};
```

#### 时间复杂度O(n^2)
for-i循环代码执行了n次,所以在for-i循环的每一次中，分别执行for-j循环`n-1`、`n-2`、`n-3`、……、`1`次

代码执行次数 =（n-1）+（n-2）+……+2+1= n(n-1)/ 2=`(1/2)n^2 + (1/2)n`

所以时间复杂度O(n^2)
> if语句内的内容与n无关，时间复杂度为**O(1)**。

虽然我们不讨论最好情况，但细心的你可能会思考到，为什么最好情况的时间复杂度是O(n)？其实上述代码的最好情况时间复杂度其实也是(n^2),至于O（n）则需要[对代码进行改进](https://www.cnblogs.com/xfcao/p/10282522.html)。
#### 稳定性：稳定
&emsp;&emsp;冒泡排序就是把小的元素往前调或者把大的元素往后调。比较是相邻的两个元素比较，交换也发生在这两个元素之间。所以，如果两个元素相等，是不会再交换的；如果两个相等的元素没有相邻，那么即使通过前面的两两交换把两个相邻起来，这时候也不会交换，所以相同元素的前后顺序并没有改变，所以冒泡排序是一种稳定排序算法。


### 选择排序
&emsp;&emsp;选择排序也是一种简单直观的排序算法，它从左往右依次将每个元素作为“基准数”，对于每个基准数都会依次比对后面的元素，找到最小的数来交换，使每次剩余数列里最小的数不断安置到正确的位置，最终得到有序数列。
#### 思路
（本例我们按照从小到大排列）

1. 标识数列的第一个元素`arr[0]`中的“0”为当前基准数下标，然后利用循环语句开始对**剩下**的数列进行遍历。
2. 遍历过程中如果比对出一个元素`arr[n]`<`arr[0]`则将基准数下标设为n，然后接着在本次循环中比较。
3. 本次循环结束后，将`arr[n]`（此时是当前循环中的最小数）与初始基准数`arr[0]`交换。此时当初始基准数的位置上即是正确的数值。
4. 然后开始第二个元素重复1-4步骤。
5. 当完成最后一次循环的时候，最后两个数比对交换，得到最终排序后的数列。

#### 动画演示
![选择排序](https://www.runoob.com/wp-content/uploads/2019/03/selectionSort.gif)

#### 代码实现
```javascript
/**
 *  选择排序
 * @param {Array} arr 传入一个数组,按照从小到大排序
 * @returns {Array} 返回排序后的数组
 */
let selectionSort=(arr)=>{
    let len = arr.length;
    //minIndex是最小值第下标
    let minIndex,
        tempNum;
    //需要以len-1个数为基准来判断剩下的数是否需要交换
    for (let i = 0; i <len-1; i++) {
        //当前基准数
        minIndex = i;
        //循环之后，可以将最小值下标标记出来
        for (let j = i+1; j <len ; j++) {
            //寻找最小的数
            if (arr[minIndex]>arr[j]){
                //将最小的数的下标保存
                minIndex=j;
            }
        }
        //将最小值与基准值交换
        tempNum = arr[minIndex];
        arr[minIndex] = arr[i];
        arr[i] = tempNum;
    }
  return arr;
};
```

#### 时间复杂度O(n^2)
&emsp;&emsp;从动画上我们能很轻易地看出，循环运行次数和问题规模的关系是：

循环执行次数 =（n-1）+（n-2）+……+2+1= n(n-1)/ 2=`(1/2)n^2 + (1/2)n`

所以复杂度O(n^2)

#### 稳定性：不稳定
&emsp;&emsp;举个例子，序列5 8 5 2 9，我们知道第一遍选择第1个元素5会和2交换，那么原序列中两个5的相对前后顺序就被破坏了，所以选择排序是一个不稳定的排序算法。 

### 插入排序
&emsp;&emsp;插入排序虽然没有冒泡和选择那么简单粗暴，但是也是比较好理解的一种排序方式了。他就像我们平常打扑克时“整理起手牌”的动作。顺序遍历每一个数，将当前数和之前的数依次比较，并插入到之前到正确位置。
#### 思路
1. 标识当前判断的元素。
2. 遍历数组中每一个数，并执行步骤3
3. 对于每一个数，都比较和前一个相邻值的大小，如果源值小，则不是当前插入的位置，继续向前比较，一直比较到源值大的那个位置，插入进去。如果一直比对到arr[0]的位置仍是源值小，则直接插入到最前面。
#### 动画演示
![插入排序](https://www.runoob.com/wp-content/uploads/2019/03/insertionSort.gif)
#### 代码实现
```javascript
/**
 * 插入排序
 * tip:就想玩扑克牌的时候整理顺序一样
 * @param {Array} arr 传入一个数组,按照从小到大排序
 * @returns {Array} 返回排序后的数组
 */
let insertionSort=(arr)=>{
    let length = arr.length;
    //current为本次循环中源位置的值
    let current,preIndex;
    for (let i = 1; i < length; i++) {
        //preIndex为相邻的前一个下标
        preIndex = i-1;
        current=arr[i];
        //元素向后移动
        while (preIndex>=0&&arr[preIndex]>current){
            arr[preIndex+1] = arr[preIndex];
            preIndex--;
        }
        //插入
        arr[preIndex+1] = current;
    }
    return arr;
};
```
#### 时间复杂度O(n^2)
&emsp;&emsp;最坏情况是，原数组是降序排列，则`次数=n(n-1)/2`；对于最好情况来说本身就是正序，也就不必专门向前插入了，省了一层循环，那直接O(n)就行了。

&emsp;&emsp;但是还是最坏情况比较有参考意义，所以还是O(n^2)。因而，插入排序不适合对于数据量比较大的排序应用。但是，如果需要排序的数据量很小，例如，量级小于千，那么插入排序还是一个不错的选择。

#### 稳定性：稳定
&emsp;&emsp;每次插入到前面的小值之后，就算遇到相等的值，也是放到之后，所以相等元素的前后顺序没有改变，所以插入排序是稳定的。

### 希尔排序
&emsp;&emsp;希尔排序是插入排序的升级版，先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录"基本有序"时，再对全体记录进行依次直接插入排序（最后一次插入排序即是正常的直接插入排序，但此时因为已经基本有序，排序会很快）。

&emsp;&emsp;希尔排序优点是：间隔大的时候移动次数少，间隔小的时候移动距离短，所以总体来说比插入排序要优良，美中不足的是该排序是不稳定的。

> 大家可以先看看这个，大概了解一下什么是希尔排序：
> 
> [希尔排序真人版视频](https://v.youku.com/v_show/id_XNTA3NDUxMjcy.html)


#### 思路
1. 设置增量gap，每次循环递减到之前到一半
2. 基于每层循环的gap值，每次遍历都进行一次插入排序_insert()
3. 执行每层都_insert,进行分组插入排序
4. 直到gap==1时，即为常规插入排序，也即是最后一轮循环
#### 动画演示
![希尔排序动画](https://oscimg.oschina.net/oscnet/fdb4e6a26640101db6eb74ed9901114b049.jpg)
#### 代码实现
```javascript
/**
 * 希尔排序
 * tip:插入排序的升级版
 * @param {Array} arr 传入一个数组,按照从小到大排序
 * @returns {Array} 返回排序后的数组
 */
let shellSort = (arr) => {
    let N = arr.length;
    //开始分组，初始状态，增量(gap)为数组长度的一半,当递减到增量为1时，即是最后一次排序
    for (let gap = Math.floor(N / 2); gap >= 1; gap = Math.floor(gap / 2)) {
        //对各个分组进行插入排序
        for (let i = gap; i < N; i++) {
            //将arr[i]插入到正确对位置上
            _insert(arr, gap, i);
        }
    }
    return arr;
};
/**
 * 希尔排序中将arr[i]插入到正确到位置上
 *（即插入排序，带有增量）
 * arr[i]所在到分组是：...arr[i-2*gap],arr[i-gap],arr[i],arr[i+gap],arr[i+2*gap]...当
 * @param arr 需要进行插入排序对数组
 * @param gap 增量
 * @param i 指定位置的下标
 */
let _insert = (arr, gap, i) => {
    let inserted = arr[i], j;
    //插入的时候分组插入（组内元素两两相隔gap）
    for (j = i - gap; j >= 0 && inserted < arr[j]; j -= gap) {
        arr[j + gap] = arr[j];
    }
    arr[j + gap] = inserted;
};
```
#### 时间复杂度O(nlog^2 n)
&emsp;&emsp;步长的选择是希尔排序的重要部分。只要最终步长为1任何步长序列都可以工作。算法最开始以一定的步长进行排序。然后会继续以一定步长进行排序，最终算法以步长为1进行排序。当步长为1时，算法变为普通插入排序，这就保证了数据一定会被排序。

&emsp;&emsp;Donald Shell最初建议步长选择为n/2,并且对步长取半直到步长达到1。虽然这样取可以比O(n^2)类的算法（插入排序）更好，但这样仍然有减少平均时间和最差时间的余地。

![希尔排序三种布长的时间复杂度](https://upload-images.jianshu.io/upload_images/17652162-02b6bbc195bae9a2.png)

&emsp;&emsp;已知的最好步长序列是由Sedgewick提出的(1, 5, 19, 41, 109,...)，他的这研究也表明“比较在希尔排序中是最主要的操作，而不是交换。”用这样步长序列的希尔排序比插入排序要快，甚至在小数组中比快速排序和堆排序还快，但是在涉及大量数据时希尔排序还是比快速排序慢。

&emsp;&emsp;另一个在大数组中表现优异的步长序列是（斐波那契数列除去0和1将剩余的数以黄金分割比的两倍的幂进行运算得到的数列）：(1, 9, 34, 182, 836, 4025, 19001, 90358, 428481, 2034035, 9651787, 45806244, 217378076, 1031612713,…)

参考链接：[维基百科](https://zh.wikipedia.org/wiki/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F)

#### 稳定性：不稳定
&emsp;&emsp;由于多次插入排序，我们知道一次插入排序是稳定的，不会改变相同元素的相对顺序，但在不同的插入排序过程中，相同的元素可能在各自的插入排序中移动，最后其稳定性就会被打乱，所以shell排序是不稳定的。

### 归并排序
&emsp;&emsp;归并排序是建立在归并操作上的一种有效的排序算法，是分治法的一个经典应用。

&emsp;&emsp;所谓**分治**，将问题分(divide)成一些小的问题然后递归求解，而治(conquer)的阶段则将分的阶段得到的各答案"修补"在一起，即分而治之。

![分治法](https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161218163120151-452283750.png)

#### 思路
1. 申请空间，使其为两个已经排序序列之和，该空间用来存放合并后的序列；
2. 设定两个指针，最初位置分别为两个已经排序的序列的起始位置；
3. 比较两个指针所指向的元素，选择相对较小的放到合并空间，并移动指针到下一位置；
4. 重复3步骤直到某一指针达到序列尾；
5. 将另一序列剩下的元素直接复制到合并序列尾；
#### 动画演示

![归并排序](https://www.runoob.com/wp-content/uploads/2019/03/mergeSort.gif)

![归并排序2](https://upload-images.jianshu.io/upload_images/17652162-e4f9b3ae3542b599.gif?imageMogr2/auto-orient/strip|imageView2/2/w/300/format/webp)

#### 代码实现
```javascript
/**
 * 归并排序
 *
 * @param {Array} arr 传入一个数组,按照从小到大排序
 * @returns {Array} 返回排序后的数组
 */
let mergeSort = (arr) => {
    let len = arr.length;
    if (len < 2) {
        return arr;
    }
    let middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);

    return _merge(mergeSort(left), mergeSort(right));
};

/**
 * 合并（归并）两个数组
 * @param left
 * @param right
 * @returns {[]}
 * @private
 */
let _merge = (left, right) => {
    //合并空间
    let result = [];
    while (left.length && right.length) {
        if (left[0] <= right[0]) {
            //移除left的第一个元素，并且添加到合并空间中
            result.push(left.shift());
        }else{
            //移除right到第一个元素，并且添加到合并空间中
            result.push(right.shift());
        }
    }
    //假如左数组还有剩余，则把左数组增加到合并空间中
    while (left.length){
        result.push(left.shift());
    }
    //假如右数组还有剩余，则把右数组增加到合并空间中
    while (right.length){
        result.push(right.shift());
    }
    return result;
};
```
#### 时间复杂度O(nlogn)
&emsp;&emsp;归并的时间复杂度分析：主要是考虑两个函数的时间花销，一、数组划分函数mergeSort()；二、有序数组归并函数_merge()；

&emsp;&emsp;简单的分析下元素长度为n的归并排序所消耗的时间 T[n]：调用mergeSort()函数划分两部分，那每一小部分排序好所花时间则为T[n/2]，而最后把这两部分有序的数组合并成一个有序的数组_merge()函数所花的时间为  O(n)；

&emsp;&emsp;公式：`T[n]  =  2T[n/2] + O(n)`

&emsp;&emsp;公式就不仔细推导了，可以[参考链接](http://blog.csdn.net/yuzhihui_no1/article/details/44198701#t2)

&emsp;&emsp;所以得出的结果为：`T[n] = O( nlogn )`

&emsp;&emsp;从另一个角度思考，假设解决最后的子问题用时为常数c，则对于n个待排序记录来说整个问题的规模为cn。

![归并排序复杂度](https://img-blog.csdn.net/20170909101802866?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2luYXRfMzYzMDY0NzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;从这个递归树可以看出，第一层时间代价为cn，第二层时间代价为cn/2+cn/2=cn…..每一层代价都是cn，总共有logn层。所以总的时间代价为cn*(logn).时间复杂度是O(nlogn)。


#### 稳定性：稳定
&emsp;&emsp;归并排序是稳定的排序.即相等的元素的顺序不会改变。即是原数组有相等数字，在排序成新数组后，也会保持着之前到相等数字的前后位置。


---
**更新中ing**


### 快速排序
1. 算法步骤
- 从数列中挑出一个元素，称为 "基准"（pivot）;

- 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；

- 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序；

```javascript
function quickSort(arr, left, right) {
    var len = arr.length,
        partitionIndex,
        left = typeof left != 'number' ? 0 : left,
        right = typeof right != 'number' ? len - 1 : right;

    if (left < right) {
        partitionIndex = partition(arr, left, right);
        quickSort(arr, left, partitionIndex-1);
        quickSort(arr, partitionIndex+1, right);
    }
    return arr;
}

function partition(arr, left ,right) {     // 分区操作
    var pivot = left,                      // 设定基准值（pivot）
        index = pivot + 1;
    for (var i = index; i <= right; i++) {
        if (arr[i] < arr[pivot]) {
            swap(arr, i, index);
            index++;
        }
    }
    swap(arr, pivot, index - 1);
    return index-1;
}

function swap(arr, i, j) {
    var temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

let arr = [2,43,2,32,55,1,-6,-77,5,434];
console.log(quickSort(arr,0,arr.length-1));

```


### 堆排序
### 计数排序
### 桶排序
### 基数排序
