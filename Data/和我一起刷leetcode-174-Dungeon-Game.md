---
title: 和我一起刷leetcode-174. Dungeon Game
date: 2020-04-21 23:29:36
tags:
---

大家好，今天我们一起做[Leetcode第174题](https://leetcode.com/problems/dungeon-game/)Dungeon Game，地牢游戏。怎么听起来有点怪怪的。 这是一道难度为Hard的题目，和前几天的几道hard题相比，这一题难度比较大。


## 题目描述

太长不看：请往下拉到“太长不看版”

```markdown
The demons had captured the princess (P) and imprisoned her in the bottom-right corner of a dungeon. The dungeon consists of M x N rooms laid out in a 2D grid. Our valiant knight (K) was initially positioned in the top-left room and must fight his way through the dungeon to rescue the princess.

The knight has an initial health point represented by a positive integer. If at any point his health point drops to 0 or below, he dies immediately.

Some of the rooms are guarded by demons, so the knight loses health (negative integers) upon entering these rooms; other rooms are either empty (0's) or contain magic orbs that increase the knight's health (positive integers).

In order to reach the princess as quickly as possible, the knight decides to move only rightward or downward in each step.

 

Write a function to determine the knight's minimum initial health so that he is able to rescue the princess.

For example, given the dungeon below, the initial health of the knight must be at least 7 if he follows the optimal path RIGHT-> RIGHT -> DOWN -> DOWN.
-2（K) -3      3
-5     -3     -3
10     30     -5(P)   

Note:

The knight's health has no upper bound.
Any room can contain threats or power-ups, even the first room the knight enters and the bottom-right room where the princess is imprisoned.
```

这题题目有点长啊，还讲了一个故事，什么地牢什么囚禁的，一看就不正经题目。肯定有小朋友看到大段英文就不想看了，翻译一下：


```markdown
恶魔抓住了公主（P）并将她关在了地下城的右下角。地下城是由 M x N 个房间组成的二维网格。我们英勇的骑士（K）最初被安置在左上角的房间里，他必须穿过地下城并通过对抗恶魔来拯救公主。

骑士的初始健康点数为一个正整数。如果他的健康点数在某一时刻降至 0 或以下，他会立即死亡。

有些房间由恶魔守卫，因此骑士在进入这些房间时会失去健康点数（若房间里的值为负整数，则表示骑士将损失健康点数）；其他房间要么是空的（房间里的值为 0），要么包含增加骑士健康点数的魔法球（若房间里的值为正整数，则表示骑士将增加健康点数）。

为了尽快到达公主，骑士决定每次只向右或向下移动一步。

编写一个函数来计算确保骑士能够拯救到公主所需的最低初始健康点数。

例如，考虑到如下布局的地下城，如果骑士遵循最佳路径 右 -> 右 -> 下 -> 下，则骑士的初始健康点数至少为 7。

-2（K) -3      3
-5     -3     -3
10     30     -5(P)  

说明:

骑士的健康点数没有上限。
任何房间都可能对骑士的健康点数造成威胁，也可能增加骑士的健康点数，包括骑士进入的左上角房间以及公主被监禁的右下角房间。
```

## 太长不看

这道题balabala描述了一大堆，其实简单来说，就是从二位数组的左上角出发，每次只能向右或者向下移动一步，每移动一次，生命值就加上当前格子中的数字，该数字可能为正或负，也可能为0，当走到右下角时，保证生命值至少要大于等于1。在这个条件下，起始生命值最少要设置成多少。但要注意一点，这道题不仅要保证在最后一步是生命值要大于0，而且在中间任何一步都要满足此条件才可以。

## 动态规划三部曲

什么是动态规划三部曲，如果还不清楚的同学，请看一下本公众号前几篇文章，有比较简单的动态规划的例子来说明什么是动态规划三部曲。

### 开数组

动态规划第一步，开数组并且清晰的定义数组的含义。

那么这里遇到了本题第一个难点，如何定义这个数组dp[i][j]?

很多同学习惯性的，作出如下定义：dp[i][j]是指从左上角出发点开始，走到[i][j]位置，所需要的最少生命数。那么在这种顶一下，我们最后要输出的是dp[-1][-1]，也就是将公主的坐标带入dp数组中。

但是这个思路，会非常的复杂，简单的来说，就是我们在使用动态规划的时候，需要知道初始状态，但是这一题恰恰求的就是初始状态。这样正向考虑问题，会非常的复杂。我们这里需要一点逆向思维，我们知道最终状态，如果公主所在的格子[-1][-1]里面的数是正数，那么我们只要保证在到公主所在的格子时有1点生命值就可以了。如果公主所在的格子[-1][-1]里面的数是一个负数，比如-2，那么我们就需要在到达公主所在的格子时，勇士有3点生命值。

这样我们知道了末状态，就可以倒推到最左上角[0][0]格子，知道勇士最初需要多少生命值才可以保证救出公主了。

所以，此时我们开一个二维数组dp，dp[i][j]的含义是**[i][j]位置开始走到公主所在的格子，你所需要的最少生命值**。这样的话，最后我们需要输出的就是**dp[0][0]**

```python
n = len(dungeon)
m = len(dungeon[0])

#dp[i][j] means start from i,j the min health you need to rescue the princess
dp = [[0]*m for _ in range(n)]
```

### 找初始值

这一道题我们先说初始值，因为这个情况比较简单，对我们后面寻找状态转移方程有启发。

初始值的第一点，是dp[-1][-1]的值，我们上面分析过：如果公主所在的格子[-1][-1]里面的数是正数，那么我们只要保证在到公主所在的格子时有1点生命值就可以了。如果公主所在的格子[-1][-1]里面的数是一个负数，比如-2，那么我们就需要在到达公主所在的格子时，勇士有3点生命值。用python写出来的话,是：

```python
dp[-1][-1] = max(1, 1 - dungeon[-1][-1]) 
```

如果有同学看不懂的话，把dungeon[-1][-1]换成一个具体的数，比如-2，再看一看。

我们接着看题目中给的例子：

```markdown
-2（K) -3      3
-5     -3     -3
10     30     -5(P)  
```

因为勇士只能往下走或者往右走，所以当勇士在最底下或者最右边的时候，它只有一种走法，我们就来研究一下这两行，以最后一行为例子：

```markdown

10     30     -5(P)  
```
现在我们知道，公主所在的位置的数值为-5，所以勇士到达这个位置的的时候，必须要有6点生命值。那么倒退一步，当勇士在30这一格的时候，它只要有1点生命值就够了，因为30比6大。但是假如是下面的情况呢；

```markdown

10     -2     -5(P)  
```

或者

```markdown

10     3     -5(P)  
```

这个时候，如果勇士所在的位置的值从30变成了-2，勇士需要有8点生命值，才可以走到最后一格时，余下6点生命值。同样的，如果值从30变成了2，那么勇士需要3点生命值，才足够苟到救公主的时候。所以我们有如下的关系式:

```python
for i in range(n-2,-1,-1):
    dp[i][-1] = max(1, dp[i+1][-1] - dungeon[i][-1])
```

如果不太能理解意思的同学，可以动手画一画举个例子，就很好理解了。


竖着的最后一列的情况也是一样的，我们有如下关系式：

```python        
for i in range(m-2,-1,-1):
    dp[-1][i] = max(1, dp[-1][i+1] - dungeon[-1][i])
```

### 状态转移方程

有了上面的初始值的讨论，我们来看状态转移方程。

假设现在在求dp[i][j]，那么它是从dp[i+1][j]或者dp[i][j+1]走过来的，只会有这两种情况。我们看几个例子：


```markdwon
5 6血
4血
```

假如这个时候dungeon[i][j]是5，i+1的位置需要最少4点血，j+1的位置最少需要6点血，那么我们应该选择4点血的那条路。因为这个时候只要我们走到[i][j]的位置上，就会加5点血，所以只要有1滴血，就够了。所以此刻dp[i][j]=1

```markdwon
-5 6血
4血
```

假如这个时候dungeon[i][j]是-5，i+1的位置需要最少4点血，j+1的位置最少需要6点血，那么我们应该选择4点血的那条路。因为这个时候只要我们走到[i][j]的位置上，就会再掉5点血，所以需要4-(-5)=9滴血，才够。所以此刻dp[i][j]=9

```markdwon
3 4血
2血
```

假如这个时候dungeon[i][j]是3，i+1的位置需要最少2点血，j+1的位置最少需要4点血，那么我们应该选择2点血的那条路。此刻dp[i][j]=1。和第一种情况是一样的

所以我们需要将[i+1][j],[i][j+1]中选一个需要血量少的，减去dungeon[i][j]，再和1比较大小。

所以，总结一下，状态转移方程是：

```python
dp[i][j] = max(1, min(dp[i+1][j], dp[i][j+1])-dungeon[i][j])
```



## 实现

又到了把以上三步结合起来的时间，只不过这一次，我们需要从下标由大到小遍历数组，注意一下range()函数的使用：range(start, end, step)，其中end的值达不到，所以我们要用-1。

可以直接在leetcode上提交的代码：

```python
class Solution:
    def calculateMinimumHP(self, dungeon: List[List[int]]) -> int:
        n = len(dungeon)
        m = len(dungeon[0])
        dp = [[0]*m for _ in range(n)]
    
        dp[-1][-1] = max(1, 1 - dungeon[-1][-1]) 
        for i in range(n-2,-1,-1):
            dp[i][-1] = max(1, dp[i+1][-1] - dungeon[i][-1])
        for i in range(m-2,-1,-1):
            dp[-1][i] = max(1, dp[-1][i+1] - dungeon[-1][i])
            
        for i in range(n-2,-1,-1):
            for j in range(m-2,-1,-1):
                dp[i][j] = max(1, min(dp[i+1][j], dp[i][j+1])-dungeon[i][j])
        return dp[0][0]
```


## 总结

这一题和Unique Paths, Minimum Path Sum等题很相似，但难度大了不少，一个是如何反向思考定义这个dp数组，还有一个是状态转移方程情况会比较多，需要思考一下。之后会接着给大家带来用二维数组动态规划的题目。


## 支持

如果喜欢本文的话，欢迎关注我的微信公众号：老方刷题。谢谢您的支持！