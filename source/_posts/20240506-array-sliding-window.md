---
title: 20240506-array-sliding-window
date: 2024-05-06 15:46:26
tags:
---


## 1. How Many Numbers Are Smaller Than the Current Number #1365

思路：
- 先排序，肯定想到了，下标就是这个数值对应的比它小的个数，可以先记录在map里，之后再导出来
- 有意思的是如果碰到相同的值的时候怎么处理？
    - 第一种办法，从前到后遍历的时候，遇到相同的值，直接跳过
    - 第二种办法，从后往前遍历，不用跳过，也不用判断相同的值的，因为遍历到index小的值的时候会自动替换后面的大的值

```python
class Solution:
    def smallerNumbersThanCurrent(self, nums: List[int]) -> List[int]:
        hash_map = {}
        arr = nums[:]
        arr.sort()
        res = [0]*len(nums)

        for i in range(len(arr)-1,-1, -1):
            hash_map[arr[i]] = i  #这里会在i遍历小的时候替换掉i大的值，当两个值相等时
        for i in range(len(nums)):
            res[i] = hash_map[nums[i]]
        # print(res)
        return res
```

## 2. Valid Montain array

```python
class Solution:
    def validMountainArray(self, arr: List[int]) -> bool:
        m = 0
        for i in range(1,len(arr)): # 第一个循环找到峰值，并记录下标，跳出循环
            if arr[i] > arr[i-1]:
                m = i
            else:
                break
        
        if m == 0 or m == len(arr)-1: # 如果下标是0或者最后，说明是单调递增或者递减
            return False
            
        for j in range(m+1,len(arr)): # 遍历后半部分，一旦出现不是下降的趋势就返False
            if arr[j] >= arr[j-1]:
                return False
       
        return True

        # 双指针，从两边像中间移动，看是不是两个指针会相遇,注意边界的问题，其实本质也是分为两半
        left = 0
        right = len(arr)-1
        while left < len(arr)-1 and arr[left] < arr[left+1]:
            left += 1
        while right >= 0 and arr[right] < arr[right -1]:
            right -= 1
        print(left,right)
        if left == right and left !=0 and left != len(arr)-1:
            return True
        else:
            return False
```

## 3. Unique number of occurrence

思路：这道题用了hash map，注意的是用set去重,然后用程度比较
```python
class Solution:
    def uniqueOccurrences(self, arr: List[int]) -> bool:
        m = {}
        for i in arr:
            m[i] = m.get(i,0) + 1
        s = set(m.values())
        if len(m) == len(s):
            return True
        else:
            return False
```

这道题的点在这里：如何用数组代替hash map，index代替key
```python
# 用list数组代替map，用index作为key
        map_arr = [0] * 2002 # 这里因为输入的值的范围是-1000 to 1000，因为负数不可以作为index
        for i in arr:
            map_arr[i+1000] += 1 # 一旦有负数，加1000之后的范围是0~2000，这里把值作为index，map_arr的数值作为统计的次数
        res = [False] * 1002 # 标记相同频率是否重复出现，因为0<=arr.length<=1000，所以判断是否会重复出现的频率也最多为频率为1001
        for index in range(2001):
            if map_arr[index] > 0:
                if res[map_arr[index]]:  #拿到map数组里面的值就是重复的频率，如果别的数（index就是数值）已经有这个频率了，再次遍历到这个数的频率就是true，也就是重复了。
                    return False
                else:
                    res[map_arr[index]] = True
        print(res)
        return True
```

## 4. Find pivot index #724

```python
class Solution:
    def pivotIndex(self, nums: List[int]) -> int:
        s = sum(nums)

        right = 0
        left = 0
        for i in range(len(nums)): # 这是遍历的时候当成中间值，因为求的就是中间值的i
            left += nums[i] #左边加上中间值
            right = s - left + nums[i] #右边加上中间值
            print(left,right)
            if left == right:
                return i 
        return -1
```