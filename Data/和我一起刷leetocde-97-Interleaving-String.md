---
title: 和我一起刷leetocde-97. Interleaving String
date: 2020-04-20 22:50:25
tags:
---



大家好，今天我们一起做[Leetcode第97题](https://leetcode.com/problems/interleaving-string/) ，这也是一道难度为Hard的题目。这一题和我们昨天说的[Leetcode第72题](https://leetcode.com/problems/edit-distance/) 非常非常的像。用昨天的方法来分析，很快就能做出来。

不要看到难度是hard就怕啦，hard里面也有很多套路题。在看解析前先自己动手做一做吧！

## 题目描述

```markdown
Given s1, s2, s3, find whether s3 is formed by the interleaving of s1 and s2.

Example 1:

Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
Output: true
Example 2:

Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
Output: false
```

首先解释一下"interleaving"这个单词，是“交织"的意思。 那么是单词的交织呢，我们这里举一个简单的例子：有两个字符串"ab"和"cd", 那么字符串"acbd"或者字符串"abcd"都可以看作是由"ab"和"cd"交织出来的。但是字符串"adcb"或者"abdc"就不是，因为你把它拆出来两个字符串的话，第二个字符串就变成"dc"了。

Leetcode这道题的题意是：给定两个字符串s1和s2，判断第三个字符串s3是不是s1和s2的交织。

## 动态规划三部曲

昨天的文章里说到：“大部分的字符串题都可以用动态规划解决，而且绝大多数能用动态规划解决的字符串题都采用二维数组。” 今天这一题又是一个典型的用二维数组解决字符串问题的例子。再一次验证了我昨天这个论断的正确性。

那我们还是老一套，动态规划三部曲，冲冲冲🐛：

### 开数组

在开数组之前，这一题我们要做一个基本的判断。如果s1的长度与s2的长度之和不等于s3的长度，那么我们直接输出False，不需要做任何处理。以下我们的讨论都是基于s1的长度与s2的长度之和等于s3的长度这个大前提的。

动态规划第一步，开数组并且清晰的定义数组的含义。

这一题需要我们输出的应该是一个bool值，也就是True或者Fasle。 我们定义一个二维数组dp，明确数组元素dp[i][j]的含义：当字符串s1的长度为i,字符串s2的长度为j时，字符串s3的长度为i+j时候，此时s3是不是s1和s2的交织。

有同学要问了：这里我们可以不可以定义一个三维数组dp[i][j][k]呢？ 其实是可以的，但是没必要。因为我们之前说过，s1和s2的长度之和一定要等于s3，所以用s3的长度必为i+j。开三维数组会浪费空间，而且空增代码的复杂度。 

根据这个数组定义,我们最后输出的结果应该是dp[n1][n2]，其中n1和n2分别为s1和s2的长度。 老样子，我们在开数组的时候，就应该预留多一个空位，否则数组下标会越界。



```python
n1 = len(s1)
n2 = len(s2)
n3 = len(s3)
if n1 + n2 != n3:
    return False

dp = [[True]*(n2+1) for _ in range(n1+1)]
```

### 状态转移方程

动态规划的核心就是状态转移方程，这一题的状态转移方程，和昨天的编辑距离那题很类似，但其实比昨天那一题还要简单。



假设现在我们处理到了s1[i],s2[j],我们煮个栗子s1此时是"abc",s2此时是"de"，会有如下两种操作：

1. 情况1：假如dp[i-1][j]为True，也就是说，"ab"和"de"可以交织成为s3的前4个字符，那么这个时候还差一个字符"c“,我们需要s3的第五个字符为"c"才能让"abc"和"de"交织成s3. 也就是说:
```markdown
dp[i][j] = dp[i-1][j] and s3[i+j-1] == s1[i-1]
```

2. 情况2：假如dp[i][j-1]为True，也就是说，"abc"和"d"可以交织成为s3的前4个字符，那么这个时候还差一个字符"e“,我们需要s3的第五个字符为"e"才能让"abc"和"de"交织成s3.
```markdown
dp[i][j] = dp[i][j-1] and s3[i+j-1] == s2[j-1]
```

所以，总结一下，状态转移方程是：

```python
#deal with dp[i][j]
# case 1
case1 = dp[i-1][j] and s3[i+j-1] == s1[i-1]
# case 2
case2 = dp[i][j-1] and s3[i+j-1] == s2[j-1]

dp[i][j] =  case1 or case2
```

### 找初始值

我们看到上面的状态转移方程，要求i，j都要大于0，不然的话就会出错误。 所以我们考虑一下i或者j为0的情况。这一段和昨天的题目也非常类似。

i或j为0的时候，初始值非常的简单，因为这代表着这俩字符串中有一个为空串，假设为s1，此时i=0，那么此时字符串s3一定等于s2才可以，不然就不是一个交织。我们对s1和s2做一个遍历，形成初始值：

```python
for i in range(1,n2+1):
    dp[0][i] = dp[0][i-1] and s3[i-1] == s2[i-1]
                
for i in range(1,n1+1):
    dp[i][0] = dp[i-1][0] and s3[i-1] == s1[i-1]

```


## 实现

将以上三步结合起来就可以轻松的编完这个程序了。 唯一要注意的是下标的问题，我们dp[i][j]和s1[i]、s2[j]之间差一个1。所以在判断的时候，要把dp[i][j]的[i][j]减1作为字符串的index，否则会出错。同时，在计算s3的index的时候，也要相应的调整。

可以直接在leetcode上提交的代码：

```python
class Solution:
    def isInterleave(self, s1: str, s2: str, s3: str) -> bool:
        n1 = len(s1)
        n2 = len(s2)
        n3 = len(s3)
        if n1 + n2 != n3:
            return False

        dp = [[True]*(n2+1) for _ in range(n1+1)]
        
        for i in range(1,n2+1):
            dp[0][i] = dp[0][i-1] and s3[i-1] == s2[i-1]
            
        for i in range(1,n1+1):
            dp[i][0] = dp[i-1][0] and s3[i-1] == s1[i-1]
        
        for i in range(1,n1+1):
            for j in range(1,n2+1):
                # case 1
                case1 = dp[i-1][j] and s3[i+j-1] == s1[i-1]
                # case 2
                case2 = dp[i][j-1] and s3[i+j-1] == s2[j-1]
                dp[i][j] =  case1 or case2
        return dp[n1][n2]
```


## 总结

可以看到，这一题和昨天的编辑距离那道题非常的相似，相信经过这两道题的练习，大家对动态规划处理字符串的问题应该加深了理解。从leetcode的分类上来看，这些都是hard的题目，但是老老实实的走完动态规划三部曲，这些题都可迎刃而解。

明天会继续趁热打铁，给大家带来新的动态规划的题目。希望可以和大家一起攻克动态规划这块硬骨头！



## 支持

如果喜欢本文的话，欢迎关注我的微信公众号：老方刷题。谢谢您的支持！