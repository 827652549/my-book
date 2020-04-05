

### 题目特点

动态规划的题，往往都是求一个固定值。

1. 计数（return 一个数）

- 有多少种方式走到右下角
- 有多少种方式选出k个数和为sum

2. 求最大值和最小值（return 一个数）

- 从左上角到右下角路径到最大数字和
- 最长上升子序列长度

3. 求存在性（return true/false）

- 取石子游戏，先手是否必胜
- 能不能选出k个数，使和为sum


### 动态规划5步骤
1. 确定状态
2. 转移方程
3. 初始条件与边界情况
4. 确定计算顺序
5. 消除冗余，优化算法

### 经典题型

#### 零钱兑换
来源：[力扣（LeetCode）在线判题](https://leetcode-cn.com/problems/coin-change)

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

**示例 1:**

```javascript
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1
```

**示例 2:**

```javascript
输入: coins = [2], amount = 3
输出: -1
```

**说明:**
你可以认为每种硬币的数量是无限的。


<details>
<summary>展开答案</summary>

```javascript
/**
 * @param {number[]} coins
 * @param {number} amount
 * @return {number}
 */
var coinChange = function(coins, amount) {
    let f = new Array(amount+1);
    f[0] = 0;
    for(let i = 1 ; i < f.length;i++){
        f[i] = Number.MAX_VALUE;
        for(let j = 0 ; j < coins.length ; j++){
            if(i-coins[j]>=0&&f[i-coins[j]]!==Number.MAX_VALUE){
             //获取到最小值
            f[i] = Math.min(f[i-coins[j]]+1,f[i]);
            }
        }
    }

    if(f[amount]!==Number.MAX_VALUE){
        return f[amount];
    }else{
        return -1;
    }

};
```

</details>

#### 不同路径

来源：[力扣（LeetCode）在线判题](https://leetcode-cn.com/problems/unique-paths)

![机器人走网格不同路径例图](https://s1.ax1x.com/2020/04/05/GDGXzF.png)

例如，上图是一个7 x 3 的网格。有多少可能的路径？

**示例 1:**

```javascript
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右
```

**示例 2:**

```javascript
输入: m = 7, n = 3
输出: 28
```

**提示：**

- 1 <= m, n <= 100
- 题目数据保证答案小于等于 2 * 10 ^ 9

<details>
<summary>展开答案</summary>

```javascript
待补充
```

</details>

