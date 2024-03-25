---
title: 20240325 backtracking-Greedy Algorithm-3
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