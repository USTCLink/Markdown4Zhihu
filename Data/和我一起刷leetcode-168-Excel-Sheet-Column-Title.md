---
title: 和我一起刷leetcode-168. Excel Sheet Column Title
date: 2020-04-17 21:09:51
tags:
---

今天我们一起做[Leetcode第168题](https://leetcode.com/problems/excel-sheet-column-title/) ，这是一道比较简单的题目，考察的内容主要是Math，难度分级为Easy.

## 题目描述

```markdown
Given a positive integer, return its corresponding column title as appear in an Excel sheet.

For example:

	1 -> A
	2 -> B
	3 -> C
	...
	26 -> Z
	27 -> AA
	28 -> AB 
	...
Example 1:

Input: 1
Output: "A"
Example 2:

Input: 28
Output: "AB"
Example 3:

Input: 701
Output: "ZY"
```

题目大意是这样的：大家肯定都用过excel。 Excel的栏的计数方式是这样的，从A到Z依次代表1到26。如果遇见大于26的数，例如27，那么就进一位记做AA。也就是说， AA = A * 26 + A = 27。 现在我们需要写一个函数，输入一个数字n，给出这个数字所对应的excel记法下的字符串。基本上可以看成是一个略微修改过的进制转换的问题。

## 算法分析

```markdown
A   1     AA    26+ 1     BA  2×26+ 1     ...     ZA  26×26+ 1     AAA  1×26²+1×26+ 1
B   2     AB    26+ 2     BB  2×26+ 2     ...     ZB  26×26+ 2     AAB  1×26²+1×26+ 2
.   .     ..    .....     ..  .......     ...     ..  ........     ...  .............   
.   .     ..    .....     ..  .......     ...     ..  ........     ...  .............
.   .     ..    .....     ..  .......     ...     ..  ........     ...  .............
Z  26     AZ    26+26     BZ  2×26+26     ...     ZZ  26×26+26     AAZ  1×26²+1×26+26
```
可以看到，思路和进制转换问题基本上一样。例如一个四位串ABCD, 其对应的数字为

```markdown
ABCD＝A×26³＋B×26²＋C×26¹＋D＝1×26³＋2×26²＋3×26¹＋4
```

和进制转换问题不一样的地方在于，通过观察可以发现，由于数字代表的值的范围是1-26，所以直接模26会导致Z消失：

```markdown
ZZZZ＝Z×26³＋Z×26²＋Z×26¹＋Z＝26×26³＋26×26²＋26×26¹＋26
```

因此可以先-1再模26，然后就可以把得到的0-25的数字映射到A-Z的范围内了。


## 实现

在python里面，要把数字转换为单个字符，使用chr()函数。那么与之对应的，要把字符转换为数字，需要使用ord()函数。 这应该是本题唯一值得注意的地方了。

```python
def convertToTitle(self, n: int) -> str:
	char = [chr(x) for x in range(ord('A'), ord('Z')+1)]
	result = ""
	while n > 0:
		result += char[(n-1)%26]
		n = (n-1) // 26
	return result[::-1] 
```

## 另外一种写法：

实际上，上面的char数组是没有必要使用的。我们可以直接使用char()和ord()函数的组合。不过由于这个数组是常数大小，空间复杂度为O(1)。因此这个无关紧要，改不改都可以。

```python
def convertToTitle(self, n: int) -> str:
	result = ""
	while n > 0:
		result += chr((n-1)%26+ord('A'))
		n = (n-1) // 26

	return result[::-1]
```

## 支持

如果喜欢本文的话，欢迎在Blog首页各社交平台上关注我。谢谢您的支持！