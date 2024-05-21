---
title: 20240508-array-double-pointer
date: 2024-05-09 13:57:12
tags:
---

## 双指针

## 在处理数组和链表相关问题时，双指针技巧是经常用到的，双指针技巧主要分为两类:左右指针和快慢指针

- 所谓左右指针，就是两个指针相向而行或者相背而行;而所谓快慢指针，就是两个指针同向而行，一快一慢

## 回文字串 #5

思路： 
- 这道题是找最大的回文字串，双指针是从中间向两端扩展，因为回文字串有可能是奇数也可能是偶数，奇数就是有一个中心点，偶数有两个中心点
- 如果是单纯判断是否是回文，双指针从两边向中间
- 这道题还是挺有意思的，隐藏了很多小细节

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:

        res = ""
        for i in range(len(s)):
            p1 = self.palindromic(s, i, i)
            p2 = self.palindromic(s, i, i+1) # 这里为什么不用判断s[i],s[i+1]是否相等呢？i，i+1作为两个中心点，判断有没有回文，因为在子函数里有判断
            
            if len(p2)> len(res):
                res = p2
            if len(p1)>len(res):
                res = p1
        return res

    def palindromic(self,s, i,j):
        while i >= 0 and j <len(s) and s[i]==s[j]: # 这里一进来就判断了s[i],s[i+1]是不是相等，如果相等才往下判断是否是有回文，不然直接返回，而返回的时候，会返s[i+1:j],也就是空
            i-=1
            j+=1
        # 因为如果跳出while循环的条件，就是i可能是-1，j可能是len(s),所以回文字串其实是范围应该是i+1,j-1
        return s[i+1:j] # 为什么这里用j而不是j-1，因为python是左闭右开的规则，[i,j），是不包含j
```

## 滑动窗口

思路：
- 维护一个窗口，右面不断扩大，在一个条件下左面不断缩小，然后更新答案

模版
```python
    left, right = 0, 0
    while right < len(s):
        # increase the window
        window.add(s[right])
        right += 1
        # shink the window from left
        while condition: window needs to shink:
            windown.remove(s[left])
            left += 1
```

## 76 Minimun Window substring

思路：
- 这道题是无序的包含子串，所以不能用回溯，要用hash map，因为字串可能包括同一个字母很多次，要用hash记录下每个字母出现的次数。
- 如果是暴力解法，时间复杂度是N^2，滑动窗后的时间复杂度是N。
- 如果用hash map比较的时候，就要用到valid了，因为即使判断hash长度相等，也无法保证，每一个key的值相等，所以，只有当值相等时valid+1，然后判断valid的长度等于，hash才算是包含全部的字串
- 细节上设计的窗口是左闭右开，这样[0,0)没有元素，因为窗口的左右下标都从0开始，往右移动一个1，才有值
- 我本来是想用res存的是字串的长度，每次比较长度，存最小的长度，但是这样增加了空间复杂度，所以好的办法是存下标，或者长度。
- 是否包含子串这种类型之后会用回溯，但是字串要顺序不能边的情况

```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        need ={}
        m = {}
        valid = 0

        left = 0
        # w = []
        # res = s+"a"
        l = float('inf')
        start = 0
        # 初始化
        for i in t:
            need[i] = need.get(i,0)+1
        # print(need)
        # 把i当成右边界
        for i in range(len(s)):
            c = s[i] 
            
            m[c] = m.get(c,0) + 1
            # print(m[c])
            if c in need and m[c] == need[c]:
                valid += 1

            while valid == len(need) and left < len(s):
                # record result
                if i-left+1 < l:
                    l = i-left+1
                    start = left
                m[s[left]] -= 1
                if s[left] in need and m[s[left]] < need[s[left]]:
                    valid -= 1
                left += 1
        print(left,l)
        if l == float('inf'):
            return ""
        
        return s[start:l+start]
```

## 3 Longest substring without repeating characters

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        
        left = 0
        res = 0
        hash_m = {}
        for i in range(len(s)):
            hash_m[s[i]] = hash_m.get(s[i],0)+1
            while hash_m[s[i]]>1:
                # if s[left] == s[i] :
                hash_m[s[left]] -= 1
                left += 1

            res = max(res, i-left+1)
        return res
```

## 438 Find All Anagrams in a String

思路：
- 也是判断子串里面是无序的，并包含有字串，所以还是要用hash map记录window，并且，如何判断window里面正好等于字串的长度呢？也需要一个need map，为什么？
- 字串里面的字母有可能是有多个组成，不能光判断len(window)==need(c)，那么需要比较window[c]==need[c],如果每次都从map里比较，比较麻烦，此时需要valid，每次循环到单个字母的时候就可以判断valid+1，直到valid = len(need)
- valid不符合规定值的时候，指针向右移动，符合规定值的时候，left向右移动，直到里面的条件还满足len(p)，记录结果，left继续向右移动，直到window的valid不再符合条件

```python
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        res = []
        left = 0
        need = {} # 
        window = {} # 为什么要用window，因为考虑到p里面有重复的元素，不能用len来比较，也就是如何判断valid是否加1
        valid = 0
        for i in p:
            need[i] = need.get(i,0) + 1
        # print(need)
        for i in range(len(s)):
            if s[i] in p: #这里只记录了符合条件的字母
                window[s[i]] = window.get(s[i],0)+1
                if window[s[i]] == need[s[i]]:
                    valid += 1
                
            while valid == len(need):
                if i - left + 1 == len(p): # 这里很关键，什么时候记录结果，如果没有这个条件，那么记录的结果就是包含字串也可能有别的字母
                    res.append(left)
                if s[left] in need: # 这里为什么要有这个判断呢？因为对应于forloop的第一行
                    window[s[left]] -= 1
                    if window[s[left]] < need[s[left]]: #这里也容易出错，
                        valid -= 1
                left += 1

        return res
```

## 567 Permutation in String

思路：
- 和之前的题是一摸一样的

```python
class Solution:
    def checkInclusion(self, s1: str, s2: str) -> bool:

        left = 0
        window = {}
        need = {}
        valid = 0
        for x in s1:
            need[x] = need.get(x,0) + 1

        for i in range(len(s2)):
            if s2[i] in need:
                window[s2[i]] = window.get(s2[i],0) + 1
                if window[s2[i]] == need[s2[i]]:
                    valid += 1
            while valid == len(need):
                # 取结果
                if i-left + 1 == len(s1):
                    return True
                if s2[left] in window:
                    window[s2[left]] -= 1
                    if window[s2[left]] < need[s2[left]]:
                        valid -= 1
                left += 1
        return False
```
直接比较两个map，就可以省略valid
```python
class Solution:
    def checkInclusion(self, s1: str, s2: str) -> bool:
        # a = {'a':1,'b':1}
        # b = {'b':1,'a':1}
        # print(a==b)
        left = 0
        window = {}
        need = {}
        # valid = 0
        for x in s1:
            need[x] = need.get(x,0) + 1

        for i in range(len(s2)):
            if s2[i] in need:
                window[s2[i]] = window.get(s2[i],0) + 1
                # if window[s2[i]] == need[s2[i]]:
                #     valid += 1
            if i-left+1 == len(s1):
                # 取结果
                if window == need:
                    return True
                if s2[left] in window:
                    window[s2[left]] -= 1
                    # if window[s2[left]] < need[s2[left]]:
                    #     valid -= 1
                left += 1
        return False
```