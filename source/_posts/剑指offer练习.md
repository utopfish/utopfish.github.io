---
title: 剑指offer练习
date: 2020-09-28 15:12:27
categories:
- 编程学习
tags:
- 剑指offer
- 代码练习

---

### 剑指 Offer 03. 数组中重复的数字
#### 标签 
简单，hash
#### 描述
找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

#### 示例
>输入：
>[2, 3, 1, 0, 2, 5, 3]
>输出：2 或 3 

#### 限制

限制：2 <= n <= 100000

#### 代码
使用hash，并且随时判断，当前是否有重复
```python
class Solution:
    def findRepeatNumber(self, nums: List[int]) -> int:
        hash={}
        for i in nums:
            if i in hash.keys():
                return i
            hash[i]=hash.get(i,0)+1
```
#### 执行结果
执行用时：44 ms, 在所有 Python3 提交中击败了96.51% 的用户
内存消耗：22.6 MB, 在所有 Python3 提交中击败了42.41% 的用户

###  剑指 Offer 04. 二维数组中的查找
#### 标签 
简单 查找
#### 描述

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。


#### 示例
```
现有矩阵 matrix 如下：
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

给定 target = 5，返回 true。给定 target = 20，返回 false。

```

#### 限制
0 <= n <= 1000
0 <= m <= 1000
#### 代码
从左下开始判断
```python
class Solution:
    def findNumberIn2DArray(self, matrix: List[List[int]], target: int) -> bool:
        i, j = len(matrix) - 1, 0
        while i >= 0 and j < len(matrix[0]):
            if matrix[i][j] > target: i -= 1
            elif matrix[i][j] < target: j += 1
            else: return True
        return False
```
#### 执行结果

执行用时：40 ms, 在所有 Python3 提交中击败了94.46% 的用户
内存消耗：17.4 MB, 在所有 Python3 提交中击败了57.61% 的用户






###  剑指 剑指 Offer 59 - I. 滑动窗口的最大值
#### 标签 
简单 滑动 窗口

#### 描述

给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。


#### 示例
```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7

```

#### 限制


#### 代码

最简单的暴力法，提前加入判断，处理k=0的情况

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        res=[]
        if k==0:
            return nums
        for i in range(len(nums)-k+1):
            res.append(max(nums[i:i+k]))
        return res

```
#### 执行结果

执行用时：504 ms, 在所有 Python3 提交中击败了42.93% 的用户
内存消耗：16.5 MB, 在所有 Python3 提交中击败了98.54% 的用户



### 剑指 Offer 60. n个骰子的点数
#### 标签 
简单 
#### 描述
把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

 

你需要用一个浮点数数组返回答案，其中第 i 个元素代表这 n 个骰子所能掷出的点数集合中第 i 小的那个的概率。



#### 示例
```

输入: 1
输出: [0.16667,0.16667,0.16667,0.16667,0.16667,0.16667]

输入: 2
输出: [0.02778,0.05556,0.08333,0.11111,0.13889,0.16667,0.13889,0.11111,0.08333,0.05556,0.02778]


```

#### 限制


1 <= n <= 11
#### 代码
1.投掷一个色子，一共6个数字，每个数字的概率都为1/6;
2.投掷两个色子，一共出现2--12十一个数字，每个数字出现的概率是在上一轮投一次色子6个数字出现概率情况下，与第二次投掷色子出现6个数字的组合。
3.投掷n个色子，一共出现n--6n一共5n+1个数字，每个数字出现的概率是在上一轮投n-1次色子情况下，与第n次投掷色子出现6个数字的组合。

最好加上对边界情况的处理即可

```python

import numpy as np
class Solution:
    def twoSum(self, n: int) -> List[float]:
        pre=[1/6,1/6,1/6,1/6,1/6,1/6]
        if n==1:
            return pre
        for i in range(1,n):
            temp=np.zeros((i+1)*5+1)
            for j in range(len(pre)):
                for k in range(6):
                    temp[j+k]+=pre[j]/6
            pre=temp
        return pre


```
#### 执行结果


执行用时：112 ms, 在所有 Python3 提交中击败了7.37% 的用户
内存消耗：28.6 MB, 在所有 Python3 提交中击败了5.17% 的用户


### 剑指 Offer 10- I. 斐波那契数列
#### 标签 
简单 斐波那契数
#### 描述

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.

斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。


#### 示例
```

输入：n = 2
输出：1


输入：n = 5
输出：5

```

#### 限制

0 <= n <= 100


#### 代码

注意取余，% 1000000007 为了防止溢出

```python
class Solution:
    def fib(self, n: int) -> int:
        if n<=1:
            return n
        a,b=0,1
        for i in range(1,n):
            a,b=b,(a+b)
        return b%1000000007
```
#### 执行结果

执行用时：40 ms, 在所有 Python3 提交中击败了69.76% 的用户
内存消耗：13.4 MB, 在所有 Python3 提交中击败了38.90% 的用户



### 剑指 Offer 25. 合并两个排序的链表
#### 标签 
简单 链表
#### 描述

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。


#### 示例
```

输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4

```

#### 限制

0 <= 链表长度 <= 1000


#### 代码 #### 

1.最暴力直观的方法：
a)当l1,l2都没有走完时，谁的值更小，谁就加入到合并链表中，并走一步
b)当某一个链表走完后，将另一个链表接到合并链表的尾部


```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        p=ListNode(0)
        begin=p
        while l1 and l2:
            if l1.val <l2.val:
                p.next=l1
                p=p.next
                l1=l1.next
            else:
                p.next = l2
                p = p.next
                l2=l2.next
        if l1:
            p.next=l1
        else:
            p.next=l2
        return begin.next
```


代码简化,缩短代码行数，效果不变
```python
class Solution:
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        p=begin=ListNode(0)
        while l1 and l2:
            if l1.val <l2.val:
                p.next,l1=l1,l1.next
            else:
                p.next ,l2= l2,l2.next
            p = p.next
        p.next=l1 if l1 else l2
        return begin.next
```
#### 执行结果

执行用时：64 ms, 在所有 Python3 提交中击败了69.84% 的用户
内存消耗：13.8 MB, 在所有 Python3 提交中击败了96.86% 的用户
