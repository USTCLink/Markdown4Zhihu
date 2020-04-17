---
title: 和我一起刷leetcode-375 Guess Number Higher or Lower II
date: 2020-04-16 16:40:36
tags:
---

今天我们一起做[Leetcode第375题](https://leetcode.com/problems/guess-number-higher-or-lower-ii/) ，这是一道Dynamic Programming的题目，难度分级为Medium.

## 题目描述

```markdown
We are playing the Guess Game. The game is as follows:

I pick a number from 1 to n. You have to guess which number I picked.

Every time you guess wrong, I'll tell you whether the number I picked is higher or lower.

However, when you guess a particular number x, and you guess wrong, you pay $x. You win the game when you guess the number I picked.

Example:

n = 10, I pick 8.

First round:  You guess 5, I tell you that it's higher. You pay $5.
Second round: You guess 7, I tell you that it's higher. You pay $7.
Third round:  You guess 9, I tell you that it's lower. You pay $9.

Game over. 8 is the number I picked.

You end up paying  <img src="https://www.zhihu.com/equation?tex=5 + " alt="5 + " class="ee_img tr_noresize" eeimg="1"> 7 +  <img src="https://www.zhihu.com/equation?tex=9 = " alt="9 = " class="ee_img tr_noresize" eeimg="1"> 21.
Given a particular n ≥ 1, find out how much money you need to have to guarantee a win.
```

题目大意是这样的：现在从1到n中选择一个数字，你要来猜这个数字。简单的猜数字的题很显然应该用二分法来处理。但这题问的不是猜数字，而是在带有惩罚的情况下，对于一个给定的n，你最少需要多少钱才可以赢。每当你猜错一次数字，你就会被罚和这个数字等值的钱。例如假如我选的是9，你猜的是8，你会被罚$8。注意题目中虽然说的是"how much money you need to have to guarantee a win", 但这里实际上需要求的是一个最小值，不然的话，你每次带一万亿总是够用的（狗头）。

## 算法分析

这里我们的思维方式是[MiniMax算法](https://en.wikipedia.org/wiki/Minimax). 我们首先需要计算出一个maximum value，然后再从中遍历选出这个maximun value的minimum value。有一点绕口，如果有机会以后再写一篇blog谈谈这个算法。

从古至今， 动态规划最难的点都是定义动态规划数组和写出状态转移方程。

我们用money([1,n])来代表从[1,n]要准备的最少的钱的数量。 假定我们在[1,n]中选择了一个错误的数字x, 那么我们知道接下来把x这个数排除出去，在[1,x-1]和[x+1,n]这个范围内寻找。根据动态规划的思想，假设我们已经解决了这个问题的字问题money([1,x-1])和money([x+1,n])，那么这个[1，n]更大数组要准备的钱最少应该是x+max(money[1,x-1],money[x+1,n])。 我们可以写出状态转移方程：

```python
money([1,n]) = x + max(money[1,x-1],money[x+1,n])
```

有了状态转移方程， 我们将x从1到n遍历，取其中使得money([1,n])的最小值的x，就是初始时应该选择哪个数字，对应的money值，就是所要准备的最少的钱。

## 实现

以上分析完了， 可以轻松的写出递归程序来解决这个题目了。不幸的是leetcode大部分dp题用递归解决一定会超时，而且面试时写一个递归的做法上去不优化优化也显得自己水平太低了。这里我们用自底向上的动态规划来实现这个算法。

引用[一篇我觉得入门动态规划很不错的文章](https://mp.weixin.qq.com/s/pg-IJ8rA1duIzt5hW1Cycw):

> 第一步骤：定义数组元素的含义，上面说了，我们会用一个数组，来保存历史数组，假设用一维数组 dp[] 吧。这个时候有一个非常非常重要的点，就是规定你这个数组元素的含义，例如你的 dp[i] 是代表什么意思？

> 第二步骤：找出数组元素之间的关系式，我觉得动态规划，还是有一点类似于我们高中学习时的归纳法的，当我们要计算 dp[n] 时，是可以利用 dp[n-1]，dp[n-2]…..dp[1]，来推出 dp[n] 的，也就是可以利用历史数据来推出新的元素值，所以我们要找出数组元素之间的关系式，例如 dp[n] = dp[n-1] + dp[n-2]，这个就是他们的关系式了。而这一步，也是最难的一步，后面我会讲几种类型的题来说。

> 学过动态规划的可能都经常听到最优子结构，把大的问题拆分成小的问题，说时候，最开始的时候，我是对最优子结构一梦懵逼的。估计你们也听多了，所以这一次，我将换一种形式来讲，不再是各种子问题，各种最优子结构。所以大佬可别喷我再乱讲，因为我说了，这是我自己平时做题的套路。

> 第三步骤：找出初始值。学过数学归纳法的都知道，虽然我们知道了数组元素之间的关系式，例如 dp[n] = dp[n-1] + dp[n-2]，我们可以通过 dp[n-1] 和 dp[n-2] 来计算 dp[n]，但是，我们得知道初始值啊，例如一直推下去的话，会由 dp[3] = dp[2] + dp[1]。而 dp[2] 和 dp[1] 是不能再分解的了，所以我们必须要能够直接获得 dp[2] 和 dp[1] 的值，而这，就是所谓的初始值。

> 由了初始值，并且有了数组元素之间的关系式，那么我们就可以得到 dp[n] 的值了，而 dp[n] 的含义是由你来定义的，你想求什么，就定义它是什么，这样，这道题也就解出来了。

我们也来一下这套动态规划三部曲：

### 建数组

这里我们需要创建一个二维数组。我们用dp[i][j]来代表[i,j]这个子序列最少要准备多少钱才足够用。将这个二维数组全部初始化为0。二维数组的初始化推荐以下写法：

```python
dp = [[0] * (n+1) for _ in range(n+1)]
```

### 状态转移方程

根据上面的算法分析，我们遍历[i,j]中所有的可能，将这些字结构中最小的值存入到数组中：

```python
dp[i][j] = min(x + max(dp[i][x-1], dp[x+1][j]) for x in range(i,j))
```

### 找初始值

这个很简单，首先dp数组在初始化的时候已经全部为0了。考虑最基本的情况[i,i+1]：这个时候我们一定猜i的，最多也就损失i刀；如果猜i+1的话，则有可能损失i+1刀，需要带更多的钱。

```python
for i in range(1,n):
	dp[i][i+1] = i
```

有了以上三个步骤，这题已经做完了。剩下的就是写循环跑一下。

## 可以在leetcode上直接运行的Python代码：

```python
class Solution:
	def getMoneyAmount(self, n: int) -> int:
		dp = [[0] * (n+1) for _ in range(n+1)]
		for i in range(1,n):
			dp[i][i+1] = i
		for low in range(n-1, 0 ,-1):
			for high in range(low+1, n+1):
				dp[low][high] = min(x + max(dp[low][x-1], dp[x+1][high]) for x in range(low,high))	
		return dp[1][n]
```

## 支持

如果喜欢本文的话，欢迎在Blog首页各社交平台上关注我。谢谢您的支持！