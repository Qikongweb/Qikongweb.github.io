---
title: 20240323 backtracking-Greedy Algorithm-2
date: 2024-03-22 15:00:26
tags:
---

## 贪心算法 

### 1. 买卖股票的最佳时机II  #122

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        res = 0

        for i in range(1, len(prices)):
            diff = prices[i] - prices[i-1]
            if diff > 0:
                res += diff

        return res
```

思路：
- 本题首先要清楚两点：

只有一只股票！
当前只有买股票或者卖股票的操作
想获得利润至少要两天为一个交易单元。

- 把利润分解为每天为单位的维度,其实我们需要收集每天的正利润就可以，收集正利润的区间，就是股票买卖的区间，而我们只需要关注最终利润，不需要记录区间。

那么只收集正利润就是贪心所贪的地方！

局部最优：收集每天的正利润，全局最优：求得最大利润。

![题目分解为每天的利润](https://code-thinking-1253855093.file.myqcloud.com/pics/2020112917480858-20230310134659477.png)

### 2. 跳跃游戏 #55

思路：
- 其实跳几步无所谓，关键在于可跳的覆盖范围！

不一定非要明确一次究竟跳几步，每次取最大的跳跃步数，这个就是可以跳跃的覆盖范围。

这个范围内，别管是怎么跳的，反正一定可以跳过来。

那么这个问题就转化为跳跃覆盖范围究竟可不可以覆盖到终点！

每次移动取最大跳跃步数（得到最大的覆盖范围），每移动一个单位，就更新最大覆盖范围。

贪心算法局部最优解：每次取最大跳跃步数（取最大覆盖范围），整体最优解：最后得到整体最大覆盖范围，看是否能到终点。

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:

        cover = 0
        if len(nums) == 1: return True
        for i in range(0, len(nums)):
            if i <= cover:
                cover = max(i + nums[i], cover)
                # print(cover)
                if cover >= len(nums)-1:
                    return True
        return False
        
```

### 3. 跳跃游戏II #45


```python
cur_distance = 0  # 当前覆盖最远距离下标
        ans = 0  # 记录走的最大步数
        next_distance = 0  # 下一步覆盖最远距离下标
        
        for i in range(len(nums)):
            next_distance = max(nums[i] + i, next_distance)  # 更新下一步覆盖最远距离下标
            if i == cur_distance:  # 遇到当前覆盖最远距离下标
                ans += 1  # 需要走下一步
                cur_distance = next_distance  # 更新当前覆盖最远距离下标（相当于加油了）
                if next_distance >= len(nums) - 1:  # 当前覆盖最远距离达到数组末尾，不用再做ans++操作，直接结束
                    break
        
        return ans


```
