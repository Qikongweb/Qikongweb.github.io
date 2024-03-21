---
title: 20240320 backtracking-5
date: 2024-03-19 20:43:12
tags:
---

## Backtracking day5

### 1. 递增子序列 #491

```python
class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        self.res = []
        self.path = []
        self.backtracking(nums,0)
        return self.res

    def backtracking(self, nums, index):
        # 收集子集
        if len(self.path)>1:
            self.res.append(self.path[:])
        used = []
        for i in range(index, len(nums)):
            # if i > index and nums[i] == nums[i-1]:  #错误的去重方法，不适用本题
            #     continue
            if (len(self.path)> 0 and nums[i] < self.path[-1]) or nums[i] in used:
                continue
            used.append(nums[i])
            self.path.append(nums[i])
            self.backtracking(nums, i+1)
            self.path.pop()

        # 可以定义set
        uset = set()  # 使用集合对本层元素进行去重
        for i in range(startIndex, len(nums)):
            if (path and nums[i] < path[-1]) or nums[i] in uset:
                continue
            
            uset.add(nums[i])  # 记录这个元素在本层用过了，本层后面不能再用了
            path.append(nums[i])
            self.backtracking(nums, i + 1, path, result)
            path.pop()

```

思路：
- 首先也是去重，我就直接想到的是90题的去重方法，用的25-26行去重，但是前提是得给nums排序，但是这个题不能排序，为什么？
    - 在90.子集II (opens new window)中我们是通过排序，再加一个标记数组来达到去重的目的。而本题求自增子序列，是不能对原数组进行排序的，排完序的数组都是自增子序列了。所以不能使用之前的去重逻辑！
- 那怎么才能去重，其实也是树层上的去重，如果是有序的话，就比较和前一个一样就跳过，但是无序的话我需要知道前面任何一个有没有一样的元素出现过，也就是我现在要用的used数组，或者是set，用这个数组记录的是树层上出现过的元素，不用回溯，因为每个树层都定义一个新的数组，只有判断当前的元素在不在里面就可以，这个去重方法也适用前面的90题，感觉用的更广泛
- used可以定义set，map，这里用的数组，因为数的范围不大，数组，set，map都可以做哈希表，而且数组干的活，map和set都能干，但如果数值范围小的话能用数组尽量用数组


2. 全排列 #46

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        self.res = []
        self.path = []
        used = [0] * len(nums)

        self.backtracking(nums, used)
        return self.res

    def backtracking(self, nums, used):
        if len(self.path) == len(nums):
            self.res.append(self.path[:])
        
        for i in range(0, len(nums)):
            if used[i] == 0:
                used[i] = 1
                self.path.append(nums[i])
                self.backtracking(nums, used)
                self.path.pop()
                used[i] = 0
```


思路：
- 排列问题需要一个used数组，标记已经选择的元素，每次循环的范围是从0到len（nums），不是从startIndex开始，循环的时候只要把used的数组用过的数跳过就可以了，因为used是树层上用过的数
- 因为排列问题，每次都要从头开始搜索，例如元素1在[1,2]中已经使用过了，但是在[2,1]中还要再使用一次1。而used数组，其实就是记录此时path里都有哪些元素使用了，一个排列里一个元素只能使用一次。


### 3. 全排列II  #47

```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        self.res = []
        self.path = []
        used = [0] * len(nums)
        nums.sort()
        self.backtracking(nums, used)
        return self.res

    def backtracking(self, nums, used):
        if len(self.path) == len(nums):
            self.res.append(self.path[:])
        
        for i in range(0, len(nums)):
            if i> 0 and used[i-1]==0 and nums[i] == nums[i-1]:
                continue
            if used[i] == 0 :
                used[i] = 1
                self.path.append(nums[i])
                self.backtracking(nums, used)
                self.path.pop()
                used[i] = 0
```

思路：
- 去重的逻辑，很有意思，套用的是组合II的去重逻辑，**必须是排列的数组**，那里用的是判断
```if i > index and nums[i]==nums[i-1]```
为什么i > index,为了确保是树层去重，而i-1和i的数比较是为了去掉树层一样的数，这个题，没有index，用什么呢？用的是used数组里的used【i】是不是0，used也是树层上的数是不是用过。由于是已经排序过的数组，那只比较前一个数和现在的数是不是想等就可以去重了。
- 组合II里面的去重有另一种方法，也是用的used数组，可以实现树层去重，这里是一样的，这里还利用used判断数字是不是已经出现过，看上一题
- 只要给定一个可包含重复数字的序列，一定需要去重， 还要强调的是去重一定要对元素进行排序，这样我们才方便通过相邻的节点来判断是否重复使用了

去重的写法：
1. 用used数组去重，一定要排序：组合总和#40，子集#98，全排列#47，

2. 用i>index比较去重，一定要排序，和1很像：组合总和#40，子集#98

3. 用每个树层定义set去重：组合总和#40，子集#98，  递增子序列#491

为什么第三种用set用的更广呢，因为他不需要排序，对于491题，不可以排序，会影响结果，只能用第二种方法，而47题只用used数组，因为used数组还要判断是不是使用过数，所以不能用index，而set会影响效率，很多余。能用1，2种方法就不要用第三种，原因如下：


- 使用set去重的版本相对于used数组的版本效率都要低很多
- 而使用used数组在时间复杂度上几乎没有额外负担！

- 使用set去重，不仅时间复杂度高了，空间复杂度也高了，在本周小结！（回溯算法系列三） (opens new window)中分析过，组合，子集，排列问题的空间复杂度都是O(n)，但如果使用set去重，空间复杂度就变成了O(n^2)，因为每一层递归都有一个set集合，系统栈空间是n，每一个空间都有set集合。

- 那有同学可能疑惑 用used数组也是占用O(n)的空间啊？

used数组可是全局变量，每层与每层之间公用一个used数组，所以空间复杂度是O(n + n)，最终空间复杂度还是O(n)。

