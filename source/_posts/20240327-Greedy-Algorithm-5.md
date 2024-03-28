---
title: 20240327-Greedy-Algorithm-5
date: 2024-03-27 11:58:02
tags:
---

## 贪心算法 

### 1. 无重叠区间 #435

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        if len(intervals) == 0:
            return 0
        # 排序把区间左边界
        intervals = sorted(intervals, key=lambda x: x[0])
        res = 0
        # print(intervals)
        for i in range(1,len(intervals)):
            if intervals[i][0] < intervals[i-1][1]:
                res += 1
                intervals[i][1] = min(intervals[i-1][1], intervals[i][1])

        return res
```

思路：
- 根据上一个题，射气球的，找重叠区间的思路
- 首先也是按左边界排序
- 判断左边界是不是小于上一个区间的右边界，如果有重叠，res加1，并且更新这个区间的右边界为最小值

### 2. 划分字母区间 #736

```python
class Solution:
    def partitionLabels(self, s: str) -> List[int]:
        m = {}
        for i in range(len(s)):
            m[s[i]] = i # 存储每个字符最后出现的位置
        res = [0]
        ll = 0
        left = 0

        for j in range(len(s)):
            ll = max(m[s[j]],ll)   # 找到当前字符出现的最远位置
            if j == ll:
                # res.append(j-sum(res)+1)
                res.append(j-left+1)# 如果当前位置是最远位置，表示可以分割出一个区间
                left = j + 1

        return res[1:]
```

思路：
- 在遍历的过程中相当于是要找每一个字母的边界，如果找到之前遍历过的所有字母的最远边界，说明这个边界就是分割点了。此时前面出现过所有字母，最远也就到这个边界了。
可以分为如下两步：

统计每一个字符最后出现的位置
从头遍历字符，并更新字符的最远出现下标，如果找到字符最远出现位置下标和当前下标相等了，则找到了分割点

- 具体解法和跳跃游戏II#45，很像，随时更新最远距离


### 3. 合并区间 #56

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        if len(intervals) == 0:
            return []
        # 排序把区间左边界
        intervals = sorted(intervals, key=lambda x: x[0])
        res = [intervals[0]]
        # print(intervals)
        for i in range(1,len(intervals)):
            if intervals[i][0] <= res[-1][1]:
                res[-1][1] = max(intervals[i][1], res[-1][1])
            else:
                res.append(intervals[i])

        return res
```

思路：
- 这道题的思路和#435，#452完全一样
- 先排序，找重叠的，区间，不同的是，我这里把结果保存到res里，拿res的最后一个区间来和当前的区间比较，看是不是要扩大res里最后一个区间
- 所以一样的套路，先排序，让所有的相邻区间尽可能的重叠在一起，按左边界，或者右边界排序都可以，处理逻辑稍有不同。