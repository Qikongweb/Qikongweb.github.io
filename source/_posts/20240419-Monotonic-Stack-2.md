---
title: 20240419-Monotonic-Stack-2 （#503，#42）
date: 2024-04-18 06:45:47
tags:
---


## 单调栈

### 1. 下一个更大元素II #503

思路：
- 一个单纯的单调栈问题加上数组是环的条件
- 方法用的是index取模的方法，就是模拟把两个数组拼接的过程，虽然循环的i是两倍的数组长度，但是如果index取模之后就在数组长度以内了。
- 我已经想到了，并且用了这个方法，但是实现的方式有一点不一样

```python
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        # 单调栈
        stack = []
        res = [-1] * len(nums)

        for i in range(len(nums)*2):
            # 这里的index也可以用取模的方法做
            index = i%len(nums)
            index = i
            if i >= len(nums):
                index = i - len(nums)
            while len(stack) > 0 and nums[index] > nums[stack[-1]]:
                res[stack[-1]] = nums[index]
                stack.pop()
            stack.append(index)
        # print( map_stack)
        
        return res
```

### 2. 接雨水 #42


单调栈 完全思路版：
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        # 单调栈的解法，找右边第一个不小于的元素
        # 先找到有面第一个比这个元素大的，再找左边第一个比他小的
        res = [0] * len(height)
        stack = [] # 存的是什么，存的是index
        for i in range(len(height)):
            while len(stack) > 0 and height[i] > height[stack[-1]]:
                # 找到了右面第一个比它大的元素
                right = height[i]
                # 找到左面第一个比它大的元素,有可能是0，这样形成一个凹槽
                if len(stack) == 1: # 说明左边的高度是0，那么最小的高度就是0，不能存雨水
                    left = 0
                    res[stack[-1]] = 0
                else:
                    left = height[stack[-2]] # 计算高度是左右高度的最小值再减去中间的高度
                    res[stack[-1]] = (min(right,left)-height[stack[-1]])*(i-stack[-2]-1)
                stack.pop()
            
            stack.append(i)
        # print(res)
       
        return sum(res)
```
精简版
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        # 单调栈的解法，找右边第一个不小于的元素
        # 先找到有面第一个比这个元素大的，再找左边第一个比他小的
        res = 0
        stack = [] # 存的是什么，存的是index
        for i in range(len(height)):
            while len(stack) > 0 and height[i] > height[stack[-1]]:
                mid_index = stack.pop()
                # 这里判断stack是否有值，是因为需要知道左边的高度，如果没值，就是0
                if stack:
                    # 找到了右面第一个比它大的元素
                    right = height[i]
                    # 找到左面第一个比它大的元素,有可能是0，这样形成一个凹槽
                    
                    left = height[stack[-1]]
                    res += (min(right,left)-height[mid_index])*(i-stack[-1]-1)
            
            stack.append(i)
        # print(res)
       
        return res
```

卡哥详细解题思路：
- 单调栈是横向技术收集雨水，需要知道高度和宽度，高度就是左右高度的最小值减去中间高度，宽度就是右的index-左index-1
- 单调栈是递增，栈顶和栈顶的下一个元素以及要入栈的元素，三个元素来接水！所以这里需要知道栈里面的第二个元素，所以就涉及到弹出一个元素之后还有没有第二个元素了，如果没有结果就是0，不用计算，如果有才会计算雨水高度

单调栈详解代码
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        # 单调栈
        '''
        单调栈是按照 行 的方向来计算雨水
        从栈顶到栈底的顺序：从小到大
        通过三个元素来接水：栈顶，栈顶的下一个元素，以及即将入栈的元素
        雨水高度是 min(凹槽左边高度, 凹槽右边高度) - 凹槽底部高度
        雨水的宽度是 凹槽右边的下标 - 凹槽左边的下标 - 1（因为只求中间宽度）
        '''
        # stack储存index，用于计算对应的柱子高度
        stack = [0]
        result = 0
        for i in range(1, len(height)):
            # 情况一
            if height[i] < height[stack[-1]]:
                stack.append(i)

            # 情况二
            # 当当前柱子高度和栈顶一致时，左边的一个是不可能存放雨水的，所以保留右侧新柱子
            # 需要使用最右边的柱子来计算宽度
            elif height[i] == height[stack[-1]]:
                stack.pop()
                stack.append(i)

            # 情况三
            else:
                # 抛出所有较低的柱子
                while stack and height[i] > height[stack[-1]]:
                    # 栈顶就是中间的柱子：储水槽，就是凹槽的地步
                    mid_height = height[stack[-1]]
                    stack.pop()
                    if stack:
                        right_height = height[i]
                        left_height = height[stack[-1]]
                        # 两侧的较矮一方的高度 - 凹槽底部高度
                        h = min(right_height, left_height) - mid_height
                        # 凹槽右侧下标 - 凹槽左侧下标 - 1: 只求中间宽度
                        w = i - stack[-1] - 1
                        # 体积：高乘宽
                        result += h * w
                stack.append(i)
        return result

# 单调栈压缩版
class Solution:
    def trap(self, height: List[int]) -> int:
        stack = [0]
        result = 0
        for i in range(1, len(height)):
            while stack and height[i] > height[stack[-1]]:
                mid_height = stack.pop()
                if stack:
                    # 雨水高度是 min(凹槽左侧高度, 凹槽右侧高度) - 凹槽底部高度
                    h = min(height[stack[-1]], height[i]) - height[mid_height]
                    # 雨水宽度是 凹槽右侧的下标 - 凹槽左侧的下标 - 1
                    w = i - stack[-1] - 1
                    # 累计总雨水体积
                    result += h * w
            stack.append(i)
        return result
```

暴力解法：
- 纵向求雨水的体积
- 要以i为中心计算左边最高的位置和右边最高的位置，比较得到最小值-i的高度，然后乘1，因为是纵向求体积，宽度就是1