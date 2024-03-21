---
title: 20240318 backtracking-3
date: 2024-03-17 06:02:02
tags:
---

## Backtracking day3

### 1. 组合总和 #39

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        self.res = []
        self.path = []
        self.backtracking(candidates, target, 0)
        return self.res
    
    def backtracking(self, nums, target, index):
        if sum(self.path) > target:
            return
        if sum(self.path) == target:
            self.res.append(self.path[:])
            return

        for i in range(index, len(nums)):
            self.path.append(nums[i])
            self.backtracking(nums, target, i)
            self.path.pop()
        
```

注意：
- self.res.append(self.path[:])，一定要用path[:]，不能传入引用而是穿数值
- 在递归的时候用的是i，因为可以有重复元素
- 做剪枝的时候要先排序，然后只要循环出现了大于target的时候后面都不用循环了，注意的额是要在push之前比较结果，如果是push之后要记得弹出，因为会一直比target大，而不再进入递归了

做了程序优化和剪枝之后的代码
```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        candidates.sort()  # 需要排序
        self.backtracking(candidates, target, 0, res, [])
        return res
    
    def backtracking(self, nums, target, index, res, path):
        if sum(path) == target:
            res.append(path[:])
            return

        for i in range(index, len(nums)):
            if sum(path) + nums[i] > target:   # 在push进去之前比较
                continue
            path.append(nums[i])
            self.backtracking(nums, target, i, res, path)
            path.pop()
```

### 2. 组合总和II #40

思路：
- 这道题和39题最主要的区别就是，给的数组中有重复的元素，而如果按之前的写法，结果集里肯定有重复的数组，此题是不可以有重复的数组，所以就是要在结果集里面去重
- 要在递归回溯的过程中去重，不然在最后去重，是会超时的
- 卡哥思路是：“使用过”在这个树形结构上是有两个维度的，一个维度是同一树枝上使用过，一个维度是同一树层上使用过。回看一下题目，元素在同一个组合内是可以重复的，怎么重复都没事，但两个组合不能相同。
所以我们要去重的是同一树层上的“使用过”，同一树枝上的都是一个组合里的元素，不用去重。

- 首先对数组排序
- 定义一个used的数组，记录每个元素有没有使用过，
- 当遇见两个数相等的时候，我需要知道前一个数的使用状态，如果前一个的状态是使用过了，那这个相等的数可以直接跳过，如果前一个的状态是使用中，那证明当前的数是在树枝上，可以有相等的存在，比如说[1,2,2,2,...],如果上次循环到第二个2，正要循环第三个2时，有两种情况，1，path = [1,2], 2, path = [1],第一种情况就是第一个2正在使用中，第二个2是一个树枝，可以重复取，第二种情况是第二个2已经都循环一遍了，它此时的状态是未使用，因为回溯的时候已经把它的状态回溯到最初的状态了，如果我们再去循环第3个2，会和上一个2有重复的情况，所以我们就要跳过这种情况了。第二种情况这就是卡哥说的树层的情况

```python
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        self.path = []
        candidates.sort()
        self.used = [0] * len(candidates)

        self.backtracking(candidates, target, 0, res)
        return res

    def backtracking(self, nums, target, index, res):
        if sum(self.path) == target:
            res.append(self.path[:])
            return
        if sum(self.path) > target:
            return
        for i in range(index, len(nums)):
            # print(self.path)
            if sum(self.path) + nums[i] > target:
                break
            # print(self.used)
            if i>0 and nums[i] == nums[i-1] and self.used[i-1] == 0:
                continue
            self.path.append(nums[i])
            self.used[i] = 1
            self.backtracking(nums, target, i+1, res)
            self.path.pop()
            self.used[i] = 0
```

另一种解法用index去重：
```python
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        res = []
        self.path = []
        candidates.sort()
        self.backtracking(candidates, target, 0, res)
        return res

    def backtracking(self, nums, target, index, res):
        if sum(self.path) == target:
            res.append(self.path[:])
            return
        if sum(self.path) > target:
            return
        for i in range(index, len(nums)):
            if sum(self.path) + nums[i] > target:
                break
            # 要对同一树层使用过的元素进行跳过             
            if i > index and nums[i] == nums[i-1]:
                continue
            self.path.append(nums[i])
            self.backtracking(nums, target, i+1, res)
            self.path.pop()

```
因为从一层从树枝递归的时候，每次ℹ️都是从index开始遍历的，进不去if，但是通过回溯会把i回溯的最初的上一层，也就是最初的i遍历完了，还是原来的i，但是for loop要往下走了，这时i+1肯定大于i，就可以进if条件了，换句话说，就是回溯到树层了，要往下循环了，自然要去重下一个一样的数字，思路是一样的，换个写法而已。我也能理解


### 分割回文串 #131

```python
class Solution:
    def partition(self, s: str) -> List[List[str]]:
        self.res = []
        self.path = []
        self.backtracking(s, 0)
        return self.res

    def backtracking(self, s, startIndex):
        # 切割线切到了字符串最后面，说明找到了一种切割方法，此时就是本层递归的终止条件
        if startIndex >= len(s):
            self.res.append(self.path[:])
            return

        for i in range(startIndex, len(s)):
            s_sub = s[startIndex: i+1]
            print(s, startIndex, i+1, s_sub)
            # 只有在是回文的时候再往下分割
            if s_sub == s_sub[::-1]:
                self.path.append(s_sub)
            
                self.backtracking(s,i + 1)
                self.path.pop()
```

思路：
- 切割问题类似组合问题
    - 组合问题：选取一个a之后，在bcdef中再去选取第二个，选取b之后在cdef中再选取第三个.....。
    - 切割问题：切割一个a之后，在bcdef中再去切割第二段，切割b之后在cdef中再切割第三段.....。

- 那么在代码里什么是切割线呢？

在处理组合问题的时候，递归参数需要传入startIndex，表示下一轮递归遍历的起始位置，这个startIndex就是切割线。

- 来看看在递归循环中如何截取子串呢？

在for (int i = startIndex; i < s.size(); i++)循环中，我们 定义了起始位置startIndex，那么 [startIndex, i] 就是要截取的子串。
其实就是startIndex，从0，1，2，字串为s[0],s[1],s[2],回溯之后就到了1，字串为s[1:2],再回溯到了0，字串就是s[0:1],最后回溯到0时，字串就是s[0:2]
