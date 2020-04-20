---
title: 和我一起刷leetcode-72. Edit Distance
date: 2020-04-19 21:35:26
tags:
---


大家好，今天我们一起做[Leetcode第72题](https://leetcode.com/problems/edit-distance/) ，这又是一道经典的动态规划题目，是一道难度为Hard的题目。但是这一题用我们之前说过的动态规划三部曲来做，其实很简单。

## 题目描述

```markdown
Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

You have the following 3 operations permitted on a word:

Insert a character
Delete a character
Replace a character
Example 1:

Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
Example 2:

Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```
这个题的背景知识是这样的： 

编辑距离（Minimum Edit Distance，MED），由俄罗斯科学家 Vladimir Levenshtein 在1965年提出，也因此而得名 Levenshtein Distance。在信息论、语言学和计算机科学领域，Levenshtein Distance 是用来度量两个序列相似程度的指标。通俗地来讲，编辑距离指的是在两个单词word1和word2之间，由其中一个单词word1转换为另一个单词word2所需要的最少单字符编辑操作次数。

在这里定义的单字符编辑操作有且仅有三种：

- 插入（Insertion）
- 删除（Deletion）
- 替换（Substitution）

比如说现在你是个编辑在用打字机打字，需要打ros这个单词，但是你手残，打成了horse，你的打字机只支持三种操作：插入、删除、替换。那么你怎么用最少的操作把horse变成ros呢？尝试几次后，可以发现，用如下的操作，步骤最少：

第一步，把h用r来替换，horse->rorse

第二步， 把r从rorse中删除，rorse->rose

第三把，把e从rose中删除，rose->ros

经过这样三个操作，我们就把horse这个单词变成了ros。因此horse与ros之间的编辑距离为3

Leetcode这道题的题意是：给定两个字符串word1和word2，求这两个字符串之间的编辑距离。

## 动态规划三部曲

关于什么是动态规划三部曲，如果还不清楚的同学，可以读一读我的这两篇文章[leetcode375](https://ustclink.github.io/2020/04/16/%E5%92%8C%E6%88%91%E4%B8%80%E8%B5%B7%E5%88%B7leetcode-375-Guess-Number-Higher-or-Lower-II/)和[leetcode322](https://ustclink.github.io/2020/04/18/%E5%92%8C%E6%88%91%E4%B8%80%E8%B5%B7%E5%88%B7leetcode-322-Coin-Change-1/)。

可能有同学要问了，为什么这里要想到用动态规划来做这题。首先最重要的一点，就是leetcode的分类标签已经给这题分好类了，是动态规划题。

好吧以上理由有点扯淡，而且实际面试做题的时候，也不会有人上来就告诉你：“小朋友，这题你要用动态规划来做哦。“ 真正的理由是，字符串题做多了，就会发现大部分的字符串题都可以用动态规划解决，而且绝大多数能用动态规划解决的字符串题都采用二维数组。

那么就开始我们的动态规划三部曲吧：

### 开数组

动态规划第一步，开数组。 开数组的时候我们需要定义清楚数组元素的含义。这一点非常的重要，也是动态规划问题的难点之一。

我们最后需要输出的，是将word1转换成word2所使用的最少操作数。这里我们定义一个二维数组dp，dp的元素dp[i][j]的含义为：当字符串word1的长度为i，字符串word2的长度为j时，此时将word1转换为word2的最少操作数为dp[i][j]. 

根据这个数组定义,我们最后输出的结果应该是dp[n1][n2]，其中n1和n2分别为word1和word2的长度。 那么我们在开数组的时候，就应该预留多一个空位，否则数组下标会越界。

一个注意的点就是二维数组开的时候最好用如下写法，以及n1和n2的位置不要弄反了。

```python
n1 = len(word1)
n2 = len(word2)
dp = [[0]*(n2+1) for _ in range(n1+1)]
```

### 状态转移方程

动态规划的核心就是状态转移方程，这一题的状态转移方程相比那些机器人走路问题什么的要难一点。

我们固定住word2，对word1进行操作，操作一共有三种：插入、删除、替换一个字符。

假设现在我们处理到了word1[i],word2[j],会有如下两种操作：

1. 简单的情况：word1[i]和word2[j]相等。 这个时候什么操作都不用做。dp[i][j]代表着当字符串word1的长度为i，字符串word2的长度为j时，此时将word1转换为word2的最少操作数为dp[i][j]。这个时候对于word1[i]和word2[j]这两个元素来说，我们不用动他，只要把前i-1和j-1个字符弄成一样就可以了，不需要增加新的操作。所以此时dp[i][j] = dp[i-1][j-1]

2. 难一点的情况：word1[i]和word2[j]不相等。 这个时候我们就需要对这两个字符进行调整，调整有三种操作，我们需要选择使得操作数最少的调整方法：

- 替换word1[i]变成word2[j]：此时dp[i][j] = dp[i-1][j-1] + 1

- 删除word1[i]：这个时候，dp[i][j] = dp[i-1][j] + 1。 为什么是这个关系式？回想一下dp数组的定义。

- 在word1[i]后一位插入word2[j]: dp[i][j] = dp[i][j-1] + 1

所以，总结一下，状态转移方程是：

```python
dp[i][j] = min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j]) + 1
```

### 找初始值

我们看到上面的状态转移方程，要求i，j都要大于0，不然的话就会出错误。 所以我们考虑一下i或者j为0的情况。

i或j为0的时候，初始值非常的简单，因为这代表着这俩字符串中有一个为空串，那么让另一个字符串变为空串，所需操作数最少的操作，就是一直插入或者删除，所需次数为此时非空字符串的长度。

```python
 for i in range(n1+1):
	dp[i][0] = i
		
for i in range(n2+1):
	dp[0][i] = i

```


## 实现

将以上三步结合起来就可以轻松的编玩这个程序了。 唯一要注意的是下标的问题，我们dp[i][j]和word1[i]、word2[j]之间差一个1。所以在判断的时候，要把dp[i][j]的[i][j]减1作为字符串的index，否则会出错。

可以直接在leetcode上提交的代码：

```python
class Solution:
	def minDistance(self, word1: str, word2: str) -> int:
		
		n1 = len(word1)
		n2 = len(word2)
		dp = [[0]*(n2+1) for _ in range(n1+1)]
		
		for i in range(n1+1):
			dp[i][0] = i	
		for i in range(n2+1):
			dp[0][i] = i
		
		for i in range(1,n1+1):
			for j in range(1,n2+1):
				if word1[i-1] == word2[j-1]:
					dp[i][j] = dp[i-1][j-1]
				else:
					dp[i][j] = min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1]) + 1
		
		return dp[n1][n2]
```


## 闲扯两句

上面的解法并不是最优秀的，在submission中也没有击败所有的解法，但我个人觉得这是最容易理解的一种解法。虽然它的效率并不够高，但是思路很清晰，按照动态规划三部曲老老实实一步一步的来都可以解答出来。

我个人最开始刷leetcode的时候，很多时候看大神各种击败99%的做法，充满了各种奇怪的技巧，看的一头雾水，也不给注释。这样的代码读多了，初学算法的时候真的很痛苦。所以这个公众号发的文章大概率不会一味的追求最快最好的做法，而是最容易最好理解的做法。希望您能理解。如果您有更好的更优秀的做法，也欢迎在公众号后台留言，我也会更新文章。

## 支持

如果喜欢本文的话，欢迎关注我的微信公众号：老方刷题。谢谢您的支持！