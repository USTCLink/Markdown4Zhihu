---
title: 和我一起刷leetcode-322. Coin Change
date: 2020-04-18 22:17:45
tags:
---

今天我们一起做[Leetcode第322题](https://leetcode.com/problems/coin-change/)。零钱问题， 这是一道非常非常经典的动态规划题目，基本上是每一个学习动态规划的同学必做的题，难度分级为Medium。 今天我们来详细说一下这个零钱问题，然后再来看leetcode的这个问题。

## 零钱问题

设想你手头有一些硬币，例如[1,5,10,25	]这样的面额。不要和我杠为什么会有25块钱的硬币。这时候来了一个顾客，买了51块的东西，你需要找钱给他49块。那么怎样找钱，可以使得你用最少的硬币的个数就可以正好找给他49块呢？

### 一个错误解法

那么一个非常直接的想法是使用贪心算法。 直观的来说，就是每一次你都是用最大面额的硬币来找钱给他：比如49你先找给他25，剩下24；你再找给他20，剩下4块，你再找给他4个1块。

看上去似乎有道理，我们写程序验证一下。

```python
def greedy(coins, amount):
	coins.sort()
	coins.reverse()
	numcoins = 0
	for c in coins:
		# add in as many coins as possible of the next largest value
		numcoins += amount // c
		amount = amount % c
	
	return numcoins


print(greedyMC([1,5,10,25], 63))
print(greedyMC([1, 21, 25], 63))
print(greedyMC([1, 5, 21, 25], 63))
```

运行结果为：

```markdown
6 
15 
7
```
那么很明显，这种方法行不通。 第二个测试例子返回了15，第三个返回了7. 但是实际上，这两个例子应该返回3. 所以贪心的策略在这个例子上行不通。

### 递归解法

使用递归的解法，基线条件是：如果amount的值在coins里，那么直接返回1就好了。

如果amount不在，则对coins中的每一个面值做一次循环，然后找到其中所用硬币最少的那个。

```python
def rec(coins, amount):
	# we use up all the $1 coins 
	mincoins = amount
	
	if amount in coins:
		return 1
		
	else:
		for i in [c for c in coins if c <= amount]:
			numcoins = 1 + rec(coins, amount-i)
			if numcoins < mincoins:
				mincoins = numcoins
				
	return mincoins
	
print(rec([1, 21, 25],63))
print(rec([1, 5, 21, 25],63))
```

结果为:
```markdown
3
3
```

我们可以看到，运行结果是对的。但是运行速度实在过于感人。我建议你不要尝试运行上面的程序。

原因，你懂的，递归嘛。 傻子才写个递归不优化就直接交上去。

### Memorize 

大家都知道，递归之所以慢，最大的原因是重复计算太多次了。那么一个直观的想法就是把之前运算的结果储存下来，这样就可以不用重复计算了。

```python
def memo(coins, amount, result)
	mincoins = amount
	if amount in coins:
		result[amount] = 1
		return 1
	else:
		for i in [c for c in coins if c <= amount]:
			numcoins = 1 + memo(coins, amount-i, result)
			if numcoins < mincoins:
				mincoins = numcoins
				result[amount] = mincoins
	return mincoins
	
print(memo([1,5,10,25],63, {}))
print(memo([1, 5, 10, 21, 25], 63, {})) 

```

结果为：

```markdown
6
3
```

这种方法的运行速度比递归不知道快到哪里去了。唯一的缺点是每次都需要传一个字典进去来储存值。

### 动态规划

动态规划啥都不了解的同学或者对动态规划三部曲不了解的同学，可以看我的[这篇文章](https://ustclink.github.io/2020/04/16/%E5%92%8C%E6%88%91%E4%B8%80%E8%B5%B7%E5%88%B7leetcode-375-Guess-Number-Higher-or-Lower-II/)

我们就来一下动态规划的三部曲：

#### 开数组

dp[i]的含义是用coins来找零i块，最少需要多少个硬币

```python
dp = [None]*(amount + 1)
```

#### 找初始值

这里我们假设每一次都把所有的1都用掉，这是用硬币最多的一种方案。

```python
# Assume at first that all 1's are used.
dp[i] = i
```

#### 状态转移方程

如果当前的钱数i，比coins中的一些硬币大的话，那么就看一下mincoins[i]和mincoins[i-c]+1哪一个更大，取其中的最小值。

```python
# Check if any coin leads to a better solution to our current best.
for c in coins:
	if i >= c:
		dp[i] = min(dp[i], dp[i ‐ c] + 1)
```

有了以上三部曲，从0到amount做一个循环，答案唾手可得：

```python
def dpMC(coins, amount):
	dp = [None] * (amount+1)
	
	for i in range(amount+1):
		dp[i] = i
		
		for c in coins:
			if cents >= c:
				dp[i] = min(dp[i], dp[i-c] + 1)
	return dp[amount]
	
print(dpMC([1,5,10,21,25], 63))
print(dpMC([1,5,10,21,25], 64))	
```

结果为：

```python
3
4
```

现在用了这几种方法彻底搞懂了背包问题和动态规划，我们来看一下leetcode这道题：


## leetcode题目描述

```markdown

You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

Example 1:

Input: coins = [1, 2, 5], amount = 11
Output: 3 
Explanation: 11 = 5 + 5 + 1
Example 2:

Input: coins = [2], amount = 3
Output: -1
Note:
You may assume that you have an infinite number of each kind of coin.
```

这个题和我们刚刚说的零钱问题，基本上完全一样。但是这里多了一个点，有可能找钱找不净。刚刚的零钱问题，所有的钱都可以用1块来找，一定会找净的。这里有可能出现：我只有面值2块的零钱，你却来搞事情，要找三块钱，那么我一定没发找给你。

## 算法分析

这里的点，我们需要做一个标记，最后输出结果的时候，看一下如果这个标记还在，就说明自底向上的动态规划没有成功运行到dp[amount]这里，那么amount面额的零钱就没法找开了。

那我们用什么来做个标记呢，这里有一个小技巧：

在初始化dp数组的时候，将里面的元素全部初始化为amount+1。我们知道，amount面额的零钱，最多最多最多也就需要amount个硬币来换开（全部用一块钱）。 最后输出的时候，判断一下dp[amount]也就是dp[-1]是不是amount+1，如果是的话，说明在动态规划的运行过程中dp[amount]没有被更新，也就是没发换开了。


## 可以直接运行的代码

leetcode的testcase有一个为amount为0的时候，所以在代码的开头加了一个判断。 吐槽一下leetcode怎么有这么多诡异的testcase。

```python
class Solution:
	def coinChange(self, coins: List[int], amount: int) -> int:
		if amount == 0:
			return 0

		dp = [amount+1] * (amount+1)
		dp[0] = 0
		
		for i in range(1, amount+1):
			for c in coins:
				if i >= c:
					dp[i] = min(dp[i], dp[i-c] + 1)
			
		return dp[-1] if dp[-1] != amount+1 else -1
	
```



## 支持

如果喜欢本文的话，欢迎在转发此文，也欢迎关注我的微信公众号：老方刷题。谢谢您的支持！