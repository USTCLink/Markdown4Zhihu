---
title: 秒杀面试中的股票问题，看这一篇就够了
date: 2020-05-02 22:53:32
tags:
---



大家好，今天这片文章总结一下leetcode中所有的股票问题的通用解法。股票问题是动态规划的一类题目，在面试中非常常见，而且变化很多，着实让人头疼。今天这篇文章归纳出股票问题的模版，之后遇到不同的股票问题，就可以用这套模版秒杀了。

## 为什么单独提出来股票问题

我们看一下[leetcode的123题：Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)，这是一道典型的股票问题的题目，难度为hard，点赞次数超过1k，非常热门，需要掌握。

### 题目描述

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

### 示例

输入: [3,3,5,0,0,3,1,4]
输出: 6
解释: 在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。

输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。

输入: [7,6,4,3,1] 
输出: 0 
解释: 在这个情况下, 没有交易完成, 所以最大利润为 0。

### leetcode常见的解答

```python
class Solution(object):
    def maxProfit(self, prices):
        fb, fs, sb, ss = float('inf'), 0, float('inf'), 0
        for price in prices:
            fb = min(fb, price)  
            fs = max(fs, price-fb)  
            sb = min(sb, price-fs) 
            ss = max(ss, price-sb)  
        return ss
```

这个解答是leetcode上点赞次数很高的一个解答，效率比较高。能看懂吧？会做吧？

不存在的，这什么啊？哪里冒出来的无穷大inf？这个fb、sb又特么是什么东西？看到别人写出的这么优秀牛逼的解法，只能深深的感叹自己是一个sb。然后默默的背下别人的解答，结果面试的时候又忘记了......

所以面对股票问题，我们**需要一套通用的行之有效的模版**，而不是**诡异充满各种技巧的高效解法**，才能笑着秒掉各种变式。

接下来，我们看一下股票问题都有哪几道题，都有哪些共性：

## 一揽子股票问题

股票问题，一共有这么六道题，不太熟悉，或者已经忘了这些题的同学，或者是根本就没做过这些题的同学，务必去leetcode上读一下这些题的题目：

- [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

- [122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

- [123. Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/)

- [188. Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)

- [309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

- [714. Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

这六道题呢，其实背景都是一样的，其中**第四道题是最泛化的形式**，其他的问题都可以由第四道题改动得来，我们看一下第四道题的题目：

### 题目描述

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

### 示例

输入: [2,4,1], k = 2
输出: 2
解释: 在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。

输入: [3,2,6,5,0,3], k = 2
输出: 7
解释: 在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。
     随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。

发现了没？第三题其实就是**k=2的一个特殊结果**。同样的，第一题只是做一次交易，所以就是**k=1的简单情况**。第二题不限制交易次数，所以**k=inf**。剩下的五六两题，都不限制次数，但是加上了cooldown和transaction fee的限制，也就是第二题的变种。


## 动态规划模版

之前的很多文章，讲到了动态规划，以及动态规划三部曲。我们都知道，动态规划的核心问题，是**定义动态规划数组dp**和**写出状态转移方程**。

## 定义dp数组

在写出状态转移方程的时候，我们要看看每个**状态**下，都有哪几种**选择**，分别对应哪几种最优子结构。

这段话可能有点儿难以理解，拿最简单的青蛙跳台阶的题目举个例子，比方青蛙处在i级台阶这个状态，那么它可以往上跳一个台阶，也可以跳两个台阶，这两种跳法，就是两个选择。

那我们来看股票问题，我们的**状态**有三个维度，第一个是**当前天数**，第二个是**到当前所能允许交易的最大次数**，第三个是**当前手头股票的持有状态**，我们用0代表当前手头没有股票，1代表当前手头已经持有股票。

我们每天都有三种选择：**买入股票，卖出股票，和继续持有现有的股票不做操作**，我们用buy、sell和rest分别对应这三种选择。

这个时候，我们就可以搭建出一个粗糙的框架了：

```
dp[i][j][k] 为三维动态规划数组，储存所获的利润
0 <= i <= n-1， i代表当天天数，n代表总的天数
1 <= k <= K, k代表当前天数下所能允许交易的最大次数，K代表总最大允许交易次数
j = 0 or 1， 0代表手头不持有股票，1代表手头持有股票

例子:dp[2][3][1]代表今天是第二题，现在我手上持有股票，至今为止最多进行3次交易。

for i:
    for k:
        for j:
            dp[i][k][j] = max(buy, sell, rest)

return dp[n-1][K][0] 
最后返回的是最后一天，最多允许大K次交易，手头股票都卖完了能得到的多少利润
```

### 状态转移方程

下面又到了动态规划最重要的部分了，状态转移方程。

我们按照手头有没有持有股票两种情况来分析一下：

- 假如我今天手头没有持有股票，现在的状态就是dp[i][k][0]，那么有两种可能，一种是我昨天手头就没有股票，今天选了rest，所以我今天手头还是没有股票,今天的利润和昨天是一样的，是dp[i-1][k][0]；还有一种可能，是我昨天持有股票，但是今天我把它sell了，所以我今天的利润是dp[i-1][k][1] + prices[i]。最后我们需要在这二者里面选出能带给我们更高利润的一个方案，所以状态转移方程是：
   
    ```python
    dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
    ```

- 假如我今天手头持有股票，那么现在的状态是dp[i][k][1]，同样的，有两种可能，一种是昨天我手头有股票，今天选择了rest，另外一种是我昨天没有持有股票，今天我选择了buy，所以我现在就持有股票了。我们可以同样的写出状态转移方程，值得一提的是，在选择buy的时候，k要减去1（你选择在上面sell的时候减1也是一样的）：

    ```python
    dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
    ```
    

### 找初始值

股票问题dp[i][k][j]的初始值，有一下几条：

- i是从0开始的，所以dp[-1][k][0] = 0,这个时候没开始，利润当然是0

- dp[-1][kk][1] = -inf, 因为还没开始的时候，是不可能持有股票的，我们用负无穷来表示这种不可能

- dp[i][0][0] = 0, 这个很简单，因为k=0意味着根本不允许交易，所以不可能有利润的

- dp[i][0][1] = -inf, 这又是一种不可能的情况，不允许交易的时候，是不可能持有股票的

大概理解一下以上的四个初始值的时候，我们就可以用这个框架来秒杀股票问题的所有题目了：

## 使用模版秒杀第三题

这里为什么我们要先秒杀第三题呢？因为第三题就是模版的**直接应用**，是一个k=2的情形，我们只需要开数组，初始化数组，然后循环一遍就可以了。如果看不懂一下代码的同学，可以回去看一下上面的讲解：

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if prices == []:
            return 0
        
        n = len(prices)
        dp = [[[0]*2 for _ in range(3)] for _ in range(n)]
        
        for k in range(3):
            dp[-1][k][0] = 0 
            dp[-1][k][1] = -float('inf')
        for i in range(n):
            dp[i][0][0] = 0
            dp[i][0][1] = -float('inf')
            
        for i in range(n):
            for k in range(2, 0, -1):
                dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
                dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
                
        return dp[n-1][2][0]
```

大家还记得文章的最开头，列出来的这个解法吗：

```python
class Solution(object):
    def maxProfit(self, prices):
        fb, fs, sb, ss = float('inf'), 0, float('inf'), 0
        for price in prices:
            fb = min(fb, price)  
            fs = max(fs, price-fb)  
            sb = min(sb, price-fs) 
            ss = max(ss, price-sb)  
        return ss
```

考虑到，这里 k 取值范围比较小，所以可以不用 for 循环，直接把 k = 1 和 2 的情况全部列举出来也可以：

```python
dp[i][2][0] = max(dp[i-1][2][0], dp[i-1][2][1] + prices[i])
dp[i][2][1] = max(dp[i-1][2][1], dp[i-1][1][0] - prices[i])
dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
dp[i][1][1] = max(dp[i-1][1][1], -prices[i])
```

发现了没？符号变一变就是上面这个sb解法啦。 我们把原先的dp数组，优化成四个变量，得到优化后的解法：

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        
        firstBuy, secondBuy = float("-inf"), float("-inf")
        firstSell, secondSell = 0, 0
        for price in prices:
            firstBuy = max(firstBuy, -price)
            firstSell = max(firstSell, price + firstBuy)
            secondBuy = max(secondBuy, firstSell - price)
            secondSell = max(secondSell, price + secondBuy )
            
        return secondSell
```

怎么样？有了上面的模版一切是不是都很自然，不用拍自己脑袋怪自己为什么想不出这样巧妙的解法了。下面我们接着来秒杀题

## 秒杀第一题

第一题，就是K=1的情况，我们把K=1带入状态转移方程中：

```python
dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
dp[i][1][1] = max(dp[i-1][1][1], dp[i-1][0][0] - prices[i]) 
            = max(dp[i-1][1][1], -prices[i])


现在发现 k 都是 1，不会改变，即 k 对状态转移已经没有影响了。
可以化简去掉k：
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], -prices[i])

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if prices ==[]:
            return 0
        n = len(prices)
        dp = [[0]*2 for _ in range(n)]
        dp[0][0] = 0
        dp[0][1] = -prices[0]
        # 解释：
        #   dp[i][0] 
        # = max(dp[-1][0], dp[-1][1] + prices[i])
        # = max(0, -infinity + prices[i]) = 0
        
        #   dp[i][1] 
        # = max(dp[-1][1], dp[-1][0] - prices[i])
        # = max(-infinity, 0 - prices[i]) 
        # = -prices[i]
        
        for i in range(1,n):
            dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
            dp[i][1] = max(dp[i-1][1], -prices[i])
            
        return dp[n-1][0]
```

而且注意一下状态转移方程，新状态只和相邻的一个状态有关，其实不用整个 dp 数组，只需要一个变量储存相邻的那个状态就足够了，这样可以把空间复杂度降到 O(1):

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        max_profit, min_price = 0, float('inf')
        for price in prices:
            min_price = min(price, min_price)
            max_profit = max(max_profit, price - min_price)
        return max_profit
```

这一题我们就秒杀完了，很容易就能优化到最后的漂亮做法。

## 秒杀第二题

第二题，k为正无穷，可以做无限制次交易，那么这个时候，可以认为k和k-1是没有区别的，我们可以把状态转移方程改写成如下形式：

```python
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
            = max(dp[i-1][k][1], dp[i-1][k][0] - prices[i])

我们发现数组中的 k 已经不会改变了，也就是说不需要记录 k 这个状态了：
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
```

同样的，我们发现i只和i-1有关，所以可以优化，只用两个变量来做：

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:  
        n = len(prices)
        if n <= 1:
            return 0
        dp_i_0 = 0
        dp_i_1 = float('-inf')
        
        for i in range(n):
            dp_i_0, dp_i_1 = max(dp_i_0, dp_i_1 + prices[i]), max(dp_i_1, dp_i_0 - prices[i])
        return dp_i_0
```

## 秒杀第四题

第四题，k为任何整数，直接套用模版就可以了。

不过，会出错误，为什么呢？原来是传入的 k 值会非常大，dp 数组太大了。交易次数 k 最多有多大呢？
一次交易由买入和卖出构成，至少需要两天。所以说有效的限制 k 应该不超过 n/2，如果超过，就没有约束作用了，相当于 k = inf。这种情况是之前解决过的。我们可以用之前的代码解决这个子问题：

```python
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        n = len(prices)
        if n <= 1:
            return 0
        if k >= n/2:
            return self.maxProfit_inf(prices)
        dp = [[[0]*2 for _ in range(k+1)] for _ in range(n)]
        for ki in range(k+1):
            dp[-1][ki][0] = 0 
            dp[-1][ki][1] = -float('inf')
        for i in range(n):
            dp[i][0][0] = 0
            dp[i][0][1] = -float('inf')

        for i in range(n):
            for ki in range(k, 0, -1):
                dp[i][ki][0] = max(dp[i-1][ki][0], dp[i-1][ki][1] + prices[i])
                dp[i][ki][1] = max(dp[i-1][ki][1], dp[i-1][ki-1][0] - prices[i])

        return dp[n-1][k][0]   
        
    def maxProfit_inf(self, prices: List[int]) -> int:  
        n = len(prices)
        if n <= 1:
            return 0
        dp_i_0 = 0
        dp_i_1 = float('-inf')
        
        for i in range(n):
            dp_i_0, dp_i_1 = max(dp_i_0, dp_i_1 + prices[i]), max(dp_i_1, dp_i_0 - prices[i])
        return dp_i_0
 ```

 ## 秒杀第五题

第五题也是k为inf的情况，只不过每次 sell 之后要等一天才能继续交易.第 i 天选择 buy 的时候，要从 i-2 的状态转移，而不是 i-1。所以要把状态转移方程改成如下形式即可：

```python
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])

```

简单的修改一下边界初始值，得到解法：

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n <= 1:
            return 0
        
        dp = [[0]*2 for _ in range(n)]

        for i in range(-2,n):
            dp[i][0] = 0
            dp[i][1] = -float('inf')
            
        for i in range(n):
            for k in range(2, 0, -1):
                dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
                dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])
                
        return dp[n-1][0]
```

## 秒杀第六题

第六题也是k为inf的情况，但是每一次交易都有了手续费，只要把手续费从利润中减去即可。改写方程：

```python
dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i] - fee)
```

code和上一题基本上一样，修改状态转移方程就可以了：

```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        n = len(prices)
        if n <= 1:
            return 0
        
        dp = [[0]*2 for _ in range(n)]

        for i in range(-1,n):
            dp[i][0] = 0
            dp[i][1] = -float('inf')
            
        for i in range(n):
            for k in range(2, 0, -1):
                dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
                dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i] - fee)
                
        return dp[n-1][0]
```

## 总结

股票问题题也是比较常见的动态规划题，今天用框架一口气秒掉了股票问题的所有六道题，其中个别题可以说是动态规划比较难的题了，现在看看用模版做是不是也没有那么难？如果您有更好的解法，欢迎评论或在公众号留言。

这篇文章从写代码，优化，总结框架大概花了我十个小时，所以这次更新的比较迟。Final周累成狗码字不易，如果喜欢本文或者本文对您有帮助，还请您多多宣传一下本公众号，谢谢您的支持。

## 支持

如果喜欢本文的话，欢迎关注我的微信公众号：老方刷题。谢谢您的支持！


![公众号二维码](https://pic.images.ac.cn/image/5ea28014bf1cf.html)