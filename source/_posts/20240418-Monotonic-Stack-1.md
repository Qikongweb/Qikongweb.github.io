---
title: 20240418-Monotonic-Stack-1（#739，#496）
date: 2024-04-17 16:23:13
tags:
---

## Monotonic Stack day 1

### 1. 每日温度 #739

思路：
- 我想到了暴力解法，两层for循环，但是超时了
- 这道题用的是单调栈，什么是单调栈，什么时候用单调栈，优势是什么？
什么时候用单调栈
- 通常是一维数组，要寻找任一个元素的右边或者左边第一个比自己大或者小的元素的位置，此时我们就要想到可以用单调栈了。时间复杂度为O(n)。
- 什么是单调栈
单调栈的本质是空间换时间，因为在遍历的过程中需要用一个栈来记录右边第一个比当前元素高的元素，优点是整个数组只需要遍历一次。
- 优势是什么？本质是什么？
更直白来说，就是用一个栈来记录我们遍历过的元素，因为我们遍历数组的时候，我们不知道之前都遍历了哪些元素，以至于遍历一个元素找不到是不是之前遍历过一个更小的，所以我们需要用一个容器（这里用单调栈）来记录我们遍历过的元素。
- 在使用单调栈的时候首先要明确如下几点：
    - 栈里可以存数字也可以存index
    - 注意栈的顺序是从栈顶到底部，递增还是递减
    - 如果求一个元素右边第一个更大元素，单调栈就是递增的，如果求一个元素右边第一个更小元素，单调栈就是递减的。

这里题遍历的时候有三种情况

情况一：当前遍历的元素T[i]小于栈顶元素T[st.top()]的情况，加入栈（因为是栈顶到栈底是递增的，所以加入进去的前面的都是小的元素）
情况二：当前遍历的元素T[i]等于栈顶元素T[st.top()]的情况，弹出栈顶元素，加入新的元素（弹出栈顶元素就是找到了右边第一个比栈顶元素的的值了，做记录，然后接着看栈里面第二个元素，如果还是比T[i]小，继续弹出，直到遇到比它大的元素或者为空栈的时候就停止，然后把T[i]加入进去）
情况三：当前遍历的元素T[i]大于栈顶元素T[st.top()]的情况，弹出栈顶元素，加入新的元素，和上面第二种情况一样

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        res = [0] * len(temperatures)
        # 暴力解法，两层for循环，时间复杂度是n^2，结果超时了
        # for i in range(len(temperatures)):
        #     for j in range(i,len(temperatures)):
        #         if temperatures[j] > temperatures[i]:
        #             res[i] = j-i
        #             break
        # # print(res)
        # return res

        # 单调栈解法
        arr = []
        for i in range(len(temperatures)):
            while len(arr)>0 and temperatures[i] > temperatures[arr[-1]]:
                res[arr[-1]] = i - arr[-1]
                arr.pop()
            arr.append(i)
            
        # print(res)
        return res
```

### 2.下一个更大元素 I #496

思路：
- 和上一题的思路是一样的，难点在于如果建立nums1和nums2之间的联系，我想到的是用map，因为注意题目中说是两个没有重复元素 的数组 nums1 和 nums2。

- 这里是我写的单调栈的思路，我是先把nums2找的右边第一个大的元素建立一个map映射，key存的是元素，value存的是结果值，然后再去遍历nums1，去找map里面是不是有这个元素存在，有的话放在结果集里。
```python
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        map_stack = {}
        stack = []

        for i in range(len(nums2)):
            while len(stack) > 0 and nums2[i] > stack[-1]:
                map_stack[stack[-1]] = nums2[i]
                stack.pop()
            stack.append(nums2[i])
        # print( map_stack)
        res = [-1] * len(nums1)
        for j in range(len(nums1)):
            if nums1[j] in map_stack:
                res[j] = map_stack[nums1[j]]
        return res

```

- 卡哥的思路是先把nums1建成一个map，做了结果集的index和nums1里元素的映射关系，然后在遍历nums2找到结果的时候，用map映射关系，找到nums1的元素，从而找到结果集的index，并进行插入，这种方法比我的方法空间复杂度稍稍底一点，不用把nums2所有的元素结果都存下来，只挑map里有映射关系的数存下来

```python
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # 单调栈 卡哥思路
        res = [-1] * len(nums1)
        map_nums1 = {}
        for i in range(len(nums1)):
            map_nums1[nums1[i]] = i
        stack = [] # 这里我存的元素，存index也可以
        for i in range(len(nums2)):
            while len(stack) > 0 and nums2[i] > stack[-1]:
                if stack[-1] in map_nums1:
                    index = map_nums1[stack[-1]]
                    res[index] = nums2[i]
                stack.pop()
            stack.append(nums2[i])
        return res

        # 版本二， 这里用了index的检索方法，就省略了map的映射记录了
class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        stack = []
        # 创建答案数组
        ans = [-1] * len(nums1)
        for i in range(len(nums2)):
            while len(stack) > 0 and nums2[i] > nums2[stack[-1]]:
                # 判断 num1 是否有 nums2[stack[-1]]。如果没有这个判断会出现指针异常
                if nums2[stack[-1]] in nums1:
                    # 锁定 num1 检索的 index
                    index = nums1.index(nums2[stack[-1]])
                    # 更新答案数组
                    ans[index] = nums2[i]
                # 弹出小元素
                # 这个代码一定要放在 if 外面。否则单调栈的逻辑就不成立了
                stack.pop()
            stack.append(i)
        return ans
```

