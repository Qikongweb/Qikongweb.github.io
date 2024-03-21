---
title: 20240319 backtracking-4
date: 2024-03-18 11:39:46
tags:
---

## Backtracking day4

### 1. 复原IP地址 #93

第一种解法：我自己的代码：
```python
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        self.res = []
        self.path = []
        self.backtracking(s,0)
        return self.res

    # 这道题和字符串切割的题思路是一样的
    def backtracking(self, nums, startIndex):
        # 终止条件就是判断切割线（index）遍历到末尾了，如果path里有4个元素，就加到结果集里
        if startIndex >= len(nums) or len(self.path) > 4:
            if len(self.path) == 4:
                s = ".".join(self.path)
                self.res.append(s)
            return
        for i in range(startIndex, len(nums)):
            
            s_sub = nums[startIndex:i+1]
            if self.is_valid(nums, startIndex, i):
                # print(s_sub)
                self.path.append(s_sub)
                self.backtracking(nums, i+1)
                self.path.pop()

    # check if the string is legal 
    def is_valid(self, s, start, end):
        if start > end:
            return False
        if s[start] == '0' and start != end:  # 0开头的数字不合法
            return False
        num = 0
        for i in range(start, end + 1):
            if not s[i].isdigit():  # 遇到非数字字符不合法
                return False
            num = num * 10 + int(s[i])
            if num > 255:  # 如果大于255了不合法
                return False
        return True

```

第二种解法：也是我的代码
```python
class Solution:
    def restoreIpAddresses(self, s: str) -> List[str]:
        self.res = []
        self.path = []
        self.backtracking(s,0)
        return self.res

    # 这道题和字符串切割的题思路是一样的
    def backtracking(self, nums, startIndex):

        # 终止条件当path有三个数就终止了
        if len(self.path) == 3:
            if self.is_valid(nums, startIndex, len(nums)-1):
                s = ".".join(self.path)+'.'+nums[startIndex:len(nums)]
                self.res.append(s)
            return
        for i in range(startIndex, len(nums)):
            s_sub = nums[startIndex:i+1]
            if self.is_valid(nums, startIndex, i):
                self.path.append(s_sub)
                self.backtracking(nums, i+1)
                self.path.pop()
            else:
                break

    # check if the string is legal
    def is_valid(self, s, start, end):
        if start > end:
            return False
        if s[start] == '0' and start != end:  # 0开头的数字不合法
            return False
        num = 0
        if int(s[start:end+1])>=0 and int(s[start:end+1])<=255:
            return True
        return False

```

两种解法：
1. 判断的终止条件是切割到最后一位，path推进来4个数，这样在之前递归的时候就已经判断只有有效的值才推进path里面，剪枝就是path里面大于4就返回，因为如果没到s的结尾，他还会继续往下切割下一位，就不需要了，path已经有4个了
2. 第二种解法就是终止条件，我判断path里有三个数，这样默认就是剩余的算最有一个数，所以只要有三个我就返回，但是我得在终止条件里加一个对最后剩下的数的一个有效判断，这样才最后拼接结果到res里，注意的是我不需要再推进path里了，这么没有回溯

无非就是终止条件怎么写，一个是有三位就终止，一个是4位终止，如果有四个终止的话得加上循环到最后一个位了，注意的是for 循环里break和continue的区别，break跳出循环，continue继续下面的循环

- 时间复杂度: O(3^4)，IP地址最多包含4个数字，每个数字最多有3种可能的分割方式，则搜索树的最大深度为4，每个节点最多有3个子节点。
空间复杂度: O(n)


### 2. 子集 #78

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        self.res = []
        self.path = []
        self.backtracking(nums,0)
        return self.res

    def backtracking(self, nums, startIndex):
        self.res.append(self.path[:])
        # if startIndex > len(nums):
        #     return
        for i in range(startIndex, len(nums)):
            self.path.append(nums[i])
            self.backtracking(nums, i + 1)
            self.path.pop()
```
思路：

- 子集和组合的回溯过程是一样的，但是收获结果的位置不一样，子集是在每个节点都要收获结果，组合是在终止条件也就是叶子节点才收获结果
- 此题没有终止条件也是可以的，因为进不了for循环
- 求取子集问题，不需要任何剪枝！因为子集就是要遍历整棵树。
- 时间复杂度: O(n * 2^n)：n表示数组的长度，2^n表示所有可能的子集数量，n * 2^n表示每个元素对于每个可能的子集的操作次数。
- 空间复杂度: O(n)

### 3. 子集II #98

```python
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        self.res = []
        self.path = []
        # self.used = [0]*len(nums)
        nums.sort()
        self.backtracking(nums, 0)
        return self.res
    
    def backtracking(self, nums, startIndex):
        # 树层去重
        self.res.append(self.path[:])

        for i in range(startIndex, len(nums)):
            if i > startIndex and nums[i]== nums[i-1]:
                continue
            self.path.append(nums[i])
            self.backtracking(nums, i+1)
            self.path.pop()

    # 利用set数组进行去重，可以应用到下一题，也是模拟树枝去重，用途更广，我在下一题详细说明
    def backtracking(self, nums, startIndex, path, result):
        result.append(path[:])  # 收集子集
        uset = set()
        for i in range(startIndex, len(nums)):
            if nums[i] in uset:
                continue
            uset.add(nums[i])
            path.append(nums[i])
            self.backtracking(nums, i + 1, path, result)
            path.pop()
```

思路：
这道题的思路就是子集去重，是78题和40题结合，就是求子集和数组去重的结合
本题也可以不使用used数组来去重，因为递归的时候下一个startIndex是i+1而不是0。

如果要是全排列的话，每次要从0开始遍历，为了跳过已入栈的元素，需要使用used。
时间复杂度: O(n * 2^n)
空间复杂度: O(n)