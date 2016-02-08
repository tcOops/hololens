---
title: LeetCode题解（python版本）
time: 2016.02.06 21:47:00
layout: post
tags:
- 算法竞赛
excerpt: NIC Teaming是Linux内核的一个功能，能在多个物理接口之上虚拟出一个网络接口，达到提高带宽、增强稳定性的目的。类似硬盘的RAID，NIC Teaming也分为多种模式，比如round-robin、broadcast、active-backup等等。其中round-robin模式顾名思义，就是将发送的包按顺序分别从几个物理接口上发送出去，理论上能达到成倍的带宽（类似RAID 0），于是很自然的就想到用这个模式。
---

<section class="bg-greys post-content" id="blogs">
 
 <div style="text-align:center">
 	<h2><strong>{{page.title}}</strong></h2>
 	<br>
 </div>

leetcode链接： [leetcode](https://leetcode.com/problemset/algorithms/)

先用python刷一遍leetcode， 请期待我第二版用C++刷。

* Two Sum
给定一个target与一个列表， 求列表中两个数的和为target， 返回两个数下标。
可以用一个字典做一个简单的hash映射， 写的还是挺pythonic的。

```
class Solution:
    # @return a tuple, (index1, index2)
    def twoSum(self, num, target):
        res = {}
        for i in range(len(num)):
            if res.get(target - num[i], None) == None:
                res[num[i]] = i + 1
            else :
                return (res[target - num[i]], i + 1)
```
<br/><br/>

* Add Two Numbers 

模拟两个数的加法过程， 普通的大数加法做法就可以。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
# @return a ListNode
    def addTwoNumbers(self, l1, l2):
        nHead, flag = ListNode(0), 0
        head = nHead
        while flag or l1 or l2:
            node = ListNode(flag)
            if l1:
                node.val += l1.val
                l1 = l1.next
            if l2:
                node.val += l2.val
                l2 = l2.next
            flag = node.val // 10
            node.val %= 10
            head.next, head = node, node
        return nHead.next
  ```
  <br/> <br/>
  
* Longest Substring Without Repeating Characters
  
求一个字符串中最长的子串， 该子串满足里面没有重复的字符。
可以考虑维护两个指针， 分别指向子串的开头与结尾， 可以分析到：当两个指针指向的字符内容相同时， 必然要向后移动开头的指针， 一直移动到与结尾指针所指向的字符不同的位置为止。
  
  ```
  class Solution:
    # @return an integer
    def lengthOfLongestSubstring(self, s):
        ans = 1
        beg, end = 0, 1
        if len(s) == 0:#be care with the Null!
            return 0
        HashTable = set([ord(s[0])])

        while end < len(s):
            k = ord(s[end])
           
            if k in HashTable:
                while s[beg] != s[end]:
                    HashTable.remove(ord(s[beg]))
                    beg += 1
                beg += 1
                end += 1
            else:
                HashTable.add(ord(s[end]))
                end += 1
                if end - beg > ans:
                    ans = end - beg
        return ans
  ```
<br/> <br/>

* Median of Two Sorted Arrays

给定两个升序序列， 求两个序列合并之后的中位数（合并之后， 重复元素不考虑去重）

最简单的方法就是先求出合并序列

```
class Solution:
    # @return a float
    def findMedianSortedArrays(self, A, B):
        m = len(A)
        n = len(B)
        c = []
        i, j = 0, 0
    
        while i < m and j < n:
            if A[i] < B[j]:
                c.append(A[i])
                i += 1
            else:
                c.append(B[j])
                j += 1
        if i < m:
            while i < m:
                c.append(A[i])
                i += 1
        if j < n:
            while j < n:
                c.append(B[j])
                j += 1
        
        if (m + n) & 1:
            return c[(i + j) // 2]
        else:
            return (c[(i + j) // 2 - 1] + c[(i + j) // 2]) / 2.0
```

或者用分治来解决：

```
# This solution convert this prob to find the Kth number of two string A and B
class Solution:
    # @return a float
    def findMedianSortedArrays(self, A, B):
        l = len(A) + len(B)
        if l & 1:
            return self.GetKthNumber(A, B, (l + 1) / 2)
        else:
            return (self.GetKthNumber(A, B, (l / 2)) + self.GetKthNumber(A, B, (l / 2) + 1)) / 2.0
    
    def GetKthNumber(self, A, B, K):
        la, lb = len(A), len(B)
        pa = min(K / 2, la)
        pb = K - pa
        if la > lb:
            return self.GetKthNumber(B, A, K)
        if la == 0:
            return B[K - 1]
        if K == 1:
            return min(A[0], B[0])
        if A[pa - 1] < B[pb - 1]:
            return self.GetKthNumber(A[pa:], B, K - pa)
        if A[pa - 1] > B[pb - 1]:
            return self.GetKthNumber(A, B[pb:], K - pb)
        if A[pa - 1] == B[pb - 1]:
            return A[pa - 1]
```
<br/><br/>

* Longest Palindromic Substring

求一个字符串里面的最长回文子串
比较朴素的做法： 枚举一个展开点， 从这一点往两边展开判断是否回文。
可以参考leetcode给出的比较炫的做法： [blingbling]("http://articles.leetcode.com/2011/11/longest-palindromic-substring-part-ii.html")

```
class Solution:
    # @return a string
    def longestPalindrome(self, s):
        ans = ''
        for i in range(len(s)):
            l = self.fuckTheWorld(s, i, i)
            if len(l) > len(ans):
                ans = l
            l = self.fuckTheWorld(s, i, i + 1)
            if len(l) > len(ans):
                ans = l
        return ans
    
    def fuckTheWorld(self, s, l, r):
        n = len(s)
        while l >= 0 and r < n and s[l] == s[r]:
            l -= 1
            r += 1
        return s[l + 1 : r]
```
<br/> <br/>

* ZigZag Conversion 

给出一个字符串， 这个字符串是按照NN这种（所谓的ZigZag）排列的， 现在需要输出他按行输出之后的序列。

一道模拟题， 找到规律分析出对应关系就可以了。

```
class Solution:
    # @return a string
    def convert(self, s, nRows):
        if nRows <= 1 or len(s) == 0:
            return s
        ans = ''
        for i in range(nRows):
            k, j = 1, i
            while j < len(s):
                ans += s[j]
                if i == 0 or i == nRows - 1:
                    step = (nRows << 1) - 2
                elif k & 1:
                    step = (nRows << 1) - (i << 1) - 2
                else:
                    step = i << 1
                j += step
                k += 1
                    
        return ans
```

 </section>