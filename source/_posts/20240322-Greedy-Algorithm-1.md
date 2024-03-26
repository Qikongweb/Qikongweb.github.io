---
title: 20240322 Greedy Algorithm-1
date: 2024-03-21 10:56:55
tags:
---

## 贪心算法 

### 1. 理论基础

- 贪心的本质是选择每一阶段的局部最优，从而达到全局最优。
- 说实话贪心算法并没有固定的套路。所以唯一的难点就是如何通过局部最优，推出整体最优。
- 感觉贪心算法更像考脑力，思路很难想到，需要脑力大开，也就是所谓的常识，只要有思路，解题到不难
- 每一道贪心算法的思路都很重要，要背下来


### 2. 分发饼干 #455

```python
class Solution:
    def findContentChildren(self, g: List[int], s: List[int]) -> int:
        g.sort()
        s.sort()
        # 指针
        index = len(s)-1
        res = 0

        for i in range(len(g)-1,-1,-1):
            if index >= 0 and s[index] >= g[i]:
                res += 1
                index -= 1
        # print(res)
        return res
```

思路：
- 大尺寸的饼干既可以满足胃口大的孩子也可以满足胃口小的孩子，那么就应该优先满足胃口大的。

- 这里的局部最优就是大饼干喂给胃口大的，充分利用饼干尺寸喂饱一个，全局最优就是喂饱尽可能多的小孩。

- 可以尝试使用贪心策略，先将饼干数组和小孩数组排序。

- 然后从后向前遍历小孩数组，用大饼干优先满足胃口大的，并统计满足小孩数量。

### 3. 摆动序列 #376

思路：
- 局部最优：删除单调坡度上的节点（不包括单调坡度两端的节点），那么这个坡度就可以有两个局部峰值。

- 整体最优：整个序列有最多的局部峰值，从而达到最长摆动序列。


以下是我的思路：我用的是双指针的思路，定义了一个空数组，把所有满足结果的数放进去，**总体思路是找拐点**，一个指针是快指针，循环整个数组，另一个指针是慢指针，指向最后一个拐点，如果遇到持续下降或者持续上升，或者平坡的时候，我的慢指针要往前移动，也就是我的res数组最后一个数要替换最新的数。
```python
class Solution:
    def wiggleMaxLength(self, nums: List[int]) -> int:
        res = []
        index = 0
        
        while(index < len(nums)):
            if len(res) == 0:
                res.append(nums[index])
            elif len(res) == 1 and nums[index] != res[-1]:
                res.append(nums[index])
            else:
                if nums[index] > res[-1] and res[-1] < res[-2]:
                    res.append(nums[index])
                elif nums[index] < res[-1] and res[-1] > res[-2]:
                    res.append(nums[index])
                else:
                    res[-1] = nums[index]
            index += 1
        return len(res)

        
```

卡哥思路效率高：

有三种情况
情况一：上下坡中有平坡, 判断preDiff == 0可以有0的时候
情况二：数组首尾两端，在res加上1，代表末端，prediff也是从0开始，代表前面有一个平坡，可以记录加1
情况三：单调坡中有平坡：只有在遇到拐点的时候才更新preDiff


- 用斜率的正负表示上坡还是下坡，因为比较的是斜率，要注意把收尾两端的点加上

```python
class Solution:
    def wiggleMaxLength(self, nums: List[int]) -> int:
        if len(nums)<=1:
            return len(nums)
        curDiff = 0; # 当前一对差值
        preDiff = 0; # 前一对差值
        result = 1;  # 记录峰值个数，序列默认序列最右边有一个峰值
        for i in range(0, len(nums)-1):
            curDiff = nums[i + 1] - nums[i]
            # 出现峰值
            if ((preDiff <= 0 and curDiff > 0) or (preDiff >= 0 and curDiff < 0)) :
                result += 1
            
                preDiff = curDiff #注意这里，只在摆动变化的时候更新prediff
        
        return result

```

### 4. 最大子序和 #53

思路：
- 这道题思路很难想，程序很简单
- 局部最优：当前“连续和”为负数的时候立刻放弃，从下一个元素重新计算“连续和”，因为负数加上下一个元素 “连续和”只会越来越小。

全局最优：选取最大“连续和”

以下是解题的关键
- 连续和如果是负数，那就是从当前开始归0，从新计数，如果是正数就继续累加，因为负数只会拖累总和更小，每次超过最大值都记录下来。

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        count = 0
        res = float('-inf')

        for i in range(0, len(nums)):
            count += nums[i]
            if count > res:
                res = count
            if count < 0:
                count = 0

        return res
```
