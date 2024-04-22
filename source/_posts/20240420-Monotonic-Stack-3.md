---
title: 20240420-Monotonic-Stack-3 （#84）
date: 2024-04-19 05:41:45
tags:
---

## 单调栈

### 1. 柱状图中最大的矩形 #84

思路：
- 单调递减栈，要找到以i为中心，的两边比它小的元素的index，然后求中间的面积
- 还需要注意的是首尾都加了个0，为什么？
    求面积的时候需要知道左边最小的left，有面最小的right其实就是栈顶和栈顶的下一个元素以及要入栈的三个元素组成了我们要求最大面积的高度和宽度
    尾部加0 如果数组本身就是升序的，例如[2,4,6,8]，那么入栈之后 都是单调递减，一直都没有走 情况三 计算结果的哪一步，所以最后输出的就是0了。



```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        # 单调栈，这道题是用单调递减栈，找到以i为中心，的两边比它小的元素的index
        res = 0
        stack = []
        heights.append(0) # 尾部加个0是为了清空栈，因为最后一位肯定是小于或等于heights的最后一位的
        heights.insert(0, 0) # 头部加个0，是为了保证stack的底部肯定有值，所以从计算第一个数的时候left就可以一直有值了
        # print(heights)
        for i in range(0,len(heights)):

            # print(stack,heights[i],i)
            while stack and heights[i] <= heights[stack[-1]]:
                mid = stack.pop() # middle index
                
                if stack:
                    right = i
                    left = stack[-1]

                    cube = (right - left - 1 ) * heights[mid]
                    # print(left,right, stack, cube)
                    res = max(res, cube)
                
            
            stack.append(i)
        # print(stack)
        return res
```