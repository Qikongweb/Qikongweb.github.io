---
title: 20240316 backtracking-2
date: 2024-03-15 17:08:05
tags:
---

### Backtracking day2

## 1. 组合总和III #216

```python
class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        # 回溯算法
        self.res = []
        self.path = []

        self.backtracking(k, n, 1)
        # print(self.res)
        return self.res

    def backtracking(self, k, n, startIndex):
        
        # 终止条件
        if len(self.path) == k:
            if sum(self.path) == n:
                self.res.append(self.path[:])
            return

        for i in range(startIndex, 10):
            self.path.append(i)
            self.backtracking(k, n, i+1)  #这里需要注意的是从i+i开始，而不是从startIndex，会有重复结果会出现[1,3,3],[2,2,3]
            self.path.pop()
```

剪枝也做对了！！！和上一个题很像，思路参考上一题#77
```python
class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        # 回溯算法
        self.res = []
        self.path = []

        self.backtracking(k, n, 1)
        # print(self.res)
        return self.res

    def backtracking(self, k, n, startIndex):
        
        # 终止条件
        if len(self.path) == k:
            if sum(self.path) == n:
                self.res.append(self.path[:])
            return
        if sum(self.path)>n:
            return

        for i in range(startIndex, 10-(k - len(self.path))+ 1):
            self.path.append(i)
            self.backtracking(k, n, i+1)
            self.path.pop()

```

### 2. 电话号码的字母组合 #17

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        self.res = []
        self.path = ""
        self.dic = {
            2: 'abc',
            3: 'def',
            4: 'ghi',
            5: 'jkl',
            6: 'mno',
            7: 'pqrs',
            8: 'tuv',
            9: 'wxyz'
        }
        self.backtracking(digits, 0)
        return self.res


    # index 是指的是循环到第几个字母了
    def backtracking(self, digits, index):
        # 终止条件
        if len(self.path) == len(digits):
            if len(self.path) != 0:
                self.res.append(self.path)
            return
        # 单层递归回溯
        # 先把要做循环的数组找出来,每次递归进来，根据index，找到循环的第几个数，决定从哪个字符串里找字母
        d = self.dic[int(digits[index])]
        for i in range(0, len(d)):
            self.path += d[i]
            self.backtracking(digits, index + 1)
            self.path = self.path[:-1]
        
```
![回溯过程的图，出自于代码随想录里](https://code-thinking-1253855093.file.myqcloud.com/pics/20201123200304469.png)
思路：
- 这个index是记录遍历第几个数字了，就是用来遍历digits的（题目中给出数字字符串），同时index也表示树的深度。

- 每次递归的时候，先取数字对应的字符集，在本次字符集里取一个字母,然后从第一次往里添加第一个数字字符集的第一个字母，第二次添加第二个字符集的第一个字母，第三次添加第三个字符集的第一个字母，第四次添加第三个字符集的第二个字母，以此类推，
adg
adh
adi
aeg
aeh
aei
afg
afh