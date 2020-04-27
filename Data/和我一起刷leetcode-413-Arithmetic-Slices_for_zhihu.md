---
title: 和我一起刷leetcode-413. Arithmetic Slices
date: 2020-04-27 00:41:06
tags:
---



大家好，昨天有同学留言希望我做一下leetcode的413题目，所以我们一起做一下[Leetcode第413题](https://leetcode.com/problems/arithmetic-slices/)。这是一道难度为Medium的题目，也是一道动态规划的题目。

## 题目描述

题目大意如下所描述：

```markdown
原文就不贴出来了，balabala了一大堆，其实一大堆废话。

首先大家都知道什么是等差数列吧，就是1，3，5，7，9....这样的数列。这个不知道建议重新翻一下高中数学必修5.

现在我们有一个数组，数组的切片(slice)大家都知道吧, 现在这个题目要我们输出的，就是给你一个数组，他有多少个切片可以形成等差数列。


示例：
输入：A = [1, 2, 3, 4]
输出：3
解释：这个数组有三个子等差数组:[1, 2, 3], [2, 3, 4]和[1, 2, 3, 4]
```



## 动态规划

我们可以观察到数组的切片中等差数列的个数只和这个区间中的元素有关。因此，这个问题可以用动态规划来解决。如果不太熟悉动态规划的同学，可以翻一下公众号的前几篇文章，都是从基础讲起的动态规划。

### 开数组

定义一个一维dp数组，其中dp[i]表示到i位置为止子等差数组的的个数。最后输出的结果，是dp数组中所有元素的总和。

### 状态转移方程

对于第i个元素，判断这个元素跟前一个元素的差值是否和等差数列中的差值相等。如果相等，那么新区间中等差数列的个数即为 1+dp[i-1]。sum同时也要加上这个值来更新全局的等差数列总数。不太理解这个过程的同学，请看下面的图片解释：

![](https://ae01.alicdn.com/kf/H1eb1000038b140de856484a91095be8bp.jpg)

![](https://ae01.alicdn.com/kf/H3f4bcb8e2d5f47628d6a282f8d62f52cf.jpg)

![](https://ae01.alicdn.com/kf/Hf7eb6ec43eb241d9ae5515dda5d9492cp.jpg)

![](https://ae01.alicdn.com/kf/H3e8e9637630746728cb16126da310e87x.jpg)

![](https://ae01.alicdn.com/kf/H29e76385cc7145749d0435d3a9a44730y.jpg)

![](https://ae01.alicdn.com/kf/Hd9329e8d383e4cfe9073248103b601dfL.jpg)

![](https://ae01.alicdn.com/kf/H6b2676b292ef4211b400d7134825d9aeK.jpg)

![](https://ae01.alicdn.com/kf/Hf784535b143b46ef899cfb89a73e3d1aA.jpg)

```python
dp[i] = 1 + dp[i - 1]
```



## 代码实现

```python
class Solution:
    def numberOfArithmeticSlices(self, A: List[int]) -> int:
        n = len(A)
        if n<=2:
            return 0
        dp = [0]*n
        for i in range(2,n):
            if A[i]+A[i-2]==2*A[i-1]:
                dp[i] = dp[i-1] + 1
        return sum(dp)
```

## 优化

这一题的动态规划是可以优化的， 通过状态转移方程可以看出计算dp[i]时只需要用到dp[i - 1]，因此空间复杂度可以进一步优化，只用两个变量current和result就可以代替dp数组了。优化后，时间复杂度仍然为O(n),但是空间复杂度从O(n)降低到了O(1)。

## 优化版本的实现

可以直接在leetcode上提交的代码：

```python
class Solution:
    def numberOfArithmeticSlices(self, A: List[int]) -> int:
        n = len(A)
        if n<=2:
            return 0
        result, current = 0, 0
        for i in range(2,n):
            if A[i]+A[i-2]==2*A[i-1]:
                current += 1
                result += current
            else:
                current = 0
        return result

```

## 总结

这一题也是比较常见的动态规划题，最后做完了不要忘记简单的优化一下。如果您有更好的解法，欢迎评论或在公众号留言。


## 支持

如果喜欢本文的话，欢迎关注我的微信公众号：老方刷题。谢谢您的支持！


![公众号二维码](https://pic.images.ac.cn/image/5ea28014bf1cf.html)