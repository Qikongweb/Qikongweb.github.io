---
title: 20240325 Greedy Algorithm-3
date: 2024-03-24 07:33:45
tags:
---

## 贪心算法 

### 1. K次取反后最大化的数组和 #1005

思路：
- 两次贪心策略：
    - 第一次：就是把整体的最大的负数拿出来取反，这样对于累加和事最有帮助的。
    - 第二次：就是所有都是整数的时候，如果k还有值，那就是把最小的正数拿出来反复取反，直到k为0，因为不管最后是正还是负，对于总数和都是消耗最小的
- 贪心的思路，局部最优：让绝对值大的负数变为正数，当前数值达到最大，整体最优：整个数组和达到最大。
- 局部最优：只找数值最小的正整数进行反转，当前数值和可以达到最大

- 那么本题的解题步骤为：

第一步：将数组按照绝对值大小从大到小排序，注意要按照绝对值的大小
第二步：从前向后遍历，遇到负数将其变为正数，同时K--
第三步：如果K还大于0，那么反复转变数值最小的元素，将K用完
第四步：求和

```python
class Solution:
    def largestSumAfterKNegations(self, nums: List[int], k: int) -> int:
        # arr_sort = sorted(nums, key=abs)
        nums.sort(key=abs,reverse=True)
        # print(nums)
        for i in range(0, len(nums)):
            if nums[i] < 0:
                nums[i] = -nums[i]
                k -= 1
            if k == 0:
                break
        if k % 2 !=0:  # 这里我只判断奇数还是偶数就可以了，不用wile循环
            nums[-1] *= -1  
        # while k > 0:
        #     nums[-1] = -nums[-1]
        #     k -= 1

        return sum(nums)
```

性能更好的代码
```python
class Solution:
    def largestSumAfterKNegations(self, nums: List[int], k: int) -> int:
        nums.sort(key=abs,reverse=True)
        for i in range(0, len(nums)):
            if nums[i] < 0 and k >0:
                nums[i] *= -1
                k -= 1
        if k % 2 !=0:
            nums[-1] *= -1  
        return sum(nums)
```

### 2. 加油站 #134
思路：

直接从全局进行贪心选择，情况如下：

情况一：如果gas的总和小于cost总和，那么无论从哪里出发，一定是跑不了一圈的

情况二：rest[i] = gas[i]-cost[i]为一天剩下的油，i从0开始计算累加到最后一站，如果累加没有出现负数，说明从0出发，油就没有断过，那么0就是起点。

情况三：如果累加的最小值是负数，汽车就要从非0节点出发，从后向前，看哪个节点能把这个负数填平，能把这个负数填平的节点就是出发节点。   

```python

class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        curSum = 0  # 当前累计的剩余油量
        minFuel = float('inf')  # 从起点出发，油箱里的油量最小值
        
        for i in range(len(gas)):
            rest = gas[i] - cost[i]
            curSum += rest
            if curSum < minFuel:
                minFuel = curSum
        
        if curSum < 0:
            return -1  # 情况1：整个行程的总消耗大于总供给，无法完成一圈
        
        if minFuel >= 0:
            return 0  # 情况2：从起点出发到任何一个加油站时油箱的剩余油量都不会小于0，可以从起点出发完成一圈
        
        for i in range(len(gas) - 1, -1, -1):
            rest = gas[i] - cost[i]
            minFuel += rest
            if minFuel >= 0:
                return i  # 情况3：找到一个位置使得从该位置出发油箱的剩余油量不会小于0，返回该位置的索引
        
        return -1  # 无法完成一圈
```

### 3. 分发糖果 #135

```python
class Solution:
    def candy(self, ratings: List[int]) -> int:
        candyVec = [1] * len(ratings)
        
        # 从前向后遍历，处理右侧比左侧评分高的情况
        for i in range(1, len(ratings)):
            if ratings[i] > ratings[i - 1]:
                candyVec[i] = candyVec[i - 1] + 1
        
        # 从后向前遍历，处理左侧比右侧评分高的情况
        for i in range(len(ratings) - 2, -1, -1):
            if ratings[i] > ratings[i + 1]:
                candyVec[i] = max(candyVec[i], candyVec[i + 1] + 1)
        
        # 统计结果
        result = sum(candyVec)
        return result

```