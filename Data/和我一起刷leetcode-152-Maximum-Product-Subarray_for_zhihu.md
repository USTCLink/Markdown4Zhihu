---
title: 和我一起刷leetcode-152. Maximum Product Subarray
date: 2020-04-26 01:57:28
tags:
---



大家好，今天我们一起做[Leetcode第152题](https://leetcode.com/problems/maximum-product-subarray/)。这是一道难度为Medium的题目。这一题是一道经典的subarray的题目，在leetcode上点赞数超过了3000，很值得掌握。

## 题目描述

题目大意如下所描述：

```markdown
给定一个整数数组nums，找到有最大乘积的连续子数组，并且返回子数组乘积的最大值。


示例：
输入：[2,3,-2,4]
输出：6
解释：
[2,3]这个子数组有最大的乘积6。

示例：
输入：[-2,0,-1]
输出：0
解释：输出的结果不可以是2，因为[-2,-1]不是一个子数组。
```



## 动态规划

这一题和leetcode上的求连续最大子序列和的题目很像，唯一的区别是这一题求的是最大积，所以比较自然的想到动态规划的做法。如果不太熟悉动态规划的同学，可以翻一下公众号的前几篇文章，都是从基础讲起的动态规划。动态规划的做法，还是老规矩，动态规划三部曲：

### 开数组

如果还是和连续最大子序列和那题一样的话，我们应该需要开一个一维的动态规划数组dp,其中dp[i]代表以nums[i]结尾的子序列的最大积。那么初始值就是:dp[0] = nums[0]. 状态转移方程就是dp[i] = max(dp[i-1]*nums[i], nums[i]）。咦？这样是不是就ok了？这题我们做出来了嘛？

如果这样的话，就掉进了这道题的陷阱里了。因为求积和求和不同，求积可以负负得正。因为数列中会有复苏，所以我们只记录最大值是不够滴！举个例子，比如我们现在有一个数组[3,-1,-100]，正确答案是100，但是用我们上面说的方法会得到2，原因是因为中间计算时可能产生负值，而且，我们无法判断当前的负值会不会与后面的负值相乘得到更大的结果。

所以，我们应该同时记录最大积和最小积，我们开一个二维数组dp，用dp[i][0]代表以nums[i]结尾的子序列的最小积，dp[i][1]表示以nums[i]结尾的子序列的最大积。


### 状态转移方程

因为可能存在负数，所以要在三个数中做判断，我们每一次更新都找到当前子序列最新的最小值和最大值，状态转移方程：

```python
dp[i][0] = min(dp[i-1][0] *nums[i], dp[i-1][1]*nums[i], nums[i])
dp[i][1] = max(dp[i-1][0] *nums[i], dp[i-1][1]*nums[i], nums[i])
```

我们再用一个变量result记录结果，每次计算出最大积时就更新一下result，最后返回result。

### 找初始值

初始值很简单，直接用数组第一个元素赋值给最大值和最小值就可以了：

```python
dp[0][0] = nums[0]
dp[0][1] = nums[0]
```

## 代码实现

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        
        n = len(nums)
        result = nums[0]
        dp = [[0]*2 for _ in range(n)]
        # dp[i][0] min, dp[i][1] max
        dp[0][0] = nums[0]
        dp[0][1] = nums[0]
        
        for i in range(1,n):
            dp[i][0] = min(dp[i-1][0] *nums[i], dp[i-1][1]*nums[i], nums[i])
            dp[i][1] = max(dp[i-1][0] *nums[i], dp[i-1][1]*nums[i], nums[i])
            if dp[i][1] > result:
                result = dp[i][1]
        return result
```

## 优化

这一题的动态规划是可以优化的，关于动态规划的优化，以后有机会再写一篇文章谈一谈。

通过状态转移方程可以看出计算dp[i][]时只需要用到dp[i - 1][]，与dp[i - 2][]及前面的结果没有关系，因此空间复杂度可以进一步优化，只用两个变量minProd和maxProd存储前一个位置的最大积和最小积。优化后，时间复杂度仍然为O(n),但是空间复杂度从O(n)降低到了O(1)。这一个简单的优化还是值得一做的。


## 优化版本的实现

可以直接在leetcode上提交的代码：

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        result, maxProd, minProd = nums[0], nums[0], nums[0]
        for i in range(1,len(nums)):
            minProd, maxProd = min(minProd*nums[i], maxProd*nums[i], nums[i]), max(minProd*nums[i], maxProd*nums[i], nums[i])
            result = max(maxProd, result)
        return result

```



## 总结

这道题是一道比较经典的子序列题目，关键是弄清楚结果的产生情况，既可能是由正数相乘得到，也有可能是由负数得到，所以必须存储最大积和最小积。

如果您有更好的解法， 欢迎评论或在公众号留言。


## 支持

如果喜欢本文的话，欢迎关注我的微信公众号：老方刷题。谢谢您的支持！


![公众号二维码](https://pic.images.ac.cn/image/5ea28014bf1cf.html)