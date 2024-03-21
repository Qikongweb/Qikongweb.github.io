---
title: 20240321 backtracking-6
date: 2024-03-20 11:24:33
tags:
---

## 回溯 day6 

### 1. hard题 重新安排行程 #332 这道题二刷的时候再刷
[卡哥算法](https://programmercarl.com/0332.重新安排行程.html#算法公开课)

### 2. hard题 N皇后 #51 二维递归

首先来看一下皇后们的约束条件：

- 不能同行
- 不能同列
- 不能同斜线


python语法：深拷贝和浅拷贝的比较
1. 一维数组，两种方法都可以：
    - copy方法：B = A.copy()
    - 切片方法：B = A[:]
2. 二维数组，上面两种都不行，都是浅拷贝
    - 正确方法： B = [it.copy() for it in A]

### 一个大坑：二维数组创建的时候，千万不能用
```[[""]*n]*n```
一定要用
```[['.' for _ in range(n)] for _ in range(n)]```

- [['.']*n] * n和[['.' for _ in range(n)] for _ in range(n)]是两种不同的方式来创建一个大小为n x n的二维列表（棋盘）。

1. [['.']*n] * n: 这种方式会创建一个大小为n的列表，其中的每个元素都是['.']*n。这里的['.']*n是一个大小为n的列表，其中的每个元素都是'.'。由于列表是引用类型，所以当你将['.']*n赋值给列表的每个元素时，实际上是将所有元素都指向了同一个列表对象。这意味着当你修改其中一个元素时，会影响到其他元素。
2. [['.' for _ in range(n)] for _ in range(n)]: 这种方式使用了列表推导式，创建了一个大小为n的列表，每个元素都是['.' for _ in range(n)]。这里的['.' for _ in range(n)]是一个大小为n的列表，其中的每个元素都是'.'。由于列表是值类型，所以每个元素都是独立的列表对象，修改其中一个元素不会影响到其他元素。


```python
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        self.res = []

        chessboard = ['.'*n] * n
        print("chess",chessboard)

        self.backtracking(n, chessboard, 0)
        return self.res

    def backtracking(self, n, chessboard, row):
        #  终止条件 收集结果
        if row == n:
            
            self.res.append(chessboard[:])
        # 因为row是每行遍历选一个位置，所以col就是列遍历选哪一个位置
        for col in range(0,n):
            # 如何合法的话，这个位置就放queen
            if self.isValid(row,col,chessboard):
                chessboard[row] = chessboard[row][:col]+'Q'+chessboard[row][col+1:]
                self.backtracking(n, chessboard, row+1)
                chessboard[row] = chessboard[row][:col]+'.'+chessboard[row][col+1:]


        
    def isValid(self, row: int, col: int, chessboard) -> bool:
        
        # 检查列
        for i in range(row):
            if chessboard[i][col] == 'Q':
                return False  # 当前列已经存在皇后，不合法
        for i in range(col):
            if chessboard[row][i] == 'Q':
                return False  # 当前列已经存在皇后，不合法

        # 检查 45 度角是否有皇后，因为每层是从上往下遍历，所有只判断左上角45度
        i, j = row - 1, col - 1
        while i >= 0 and j >= 0:
            if chessboard[i][j] == 'Q':
                return False  # 左上方向已经存在皇后，不合法
            i -= 1
            j -= 1

        # 检查 135 度角是否有皇后，因为每层是从上往下遍历，所有只判断右上角45度
        i, j = row - 1, col + 1
        while i >= 0 and j < len(chessboard):
            if chessboard[i][j] == 'Q':
                return False  # 右上方向已经存在皇后，不合法
            i -= 1
            j += 1

        return True  # 当前位置合法

```
**踩了一个大坑**
我自己的代码，用的是二维数组做棋盘，在找到结果的时候，转一维数组
```python
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        self.res = []

        chessboard = [['.']*n for _ in range(n)]

        self.backtracking(n, chessboard, 0)
        return self.res

    def backtracking(self, n, chessboard, row):
        #  终止条件 收集结果
        if row == n:
            print(chessboard)
            d_2 = []
            # 这里是为了把棋盘的二维转一维
            for i in range(0,n):
                d_2.append(''.join(chessboard[i]))
            print(d_2)
            self.res.append(d_2[:])
            # return
        # 因为row是每行遍历选一个位置，所以col就是列遍历选哪一个位置
        for col in range(0,n):
            # 如何合法的话，这个位置就放queen
            if self.isValid(row,col,chessboard):
                print(row)
                chessboard[row][col] = 'Q'
                # print(chessboard)
                self.backtracking(n, chessboard, row+1)
                chessboard[row][col] = '.'


        
    def isValid(self, row: int, col: int, chessboard) -> bool:
        n = len(chessboard)
        for i in range(n):
                if chessboard[i][col] == 'Q':
                    return False
                if row - i >= 0 and col - i >= 0 and chessboard[row-i][col-i] == 'Q':
                    return False
                if row - i >= 0 and col + i < n and chessboard[row-i][col+i] == 'Q':
                    return False
        return True
```
### 3. hard题 解数独 #37 三维递归 这道题二刷的时候再刷 

思路：
- 本题中棋盘的每一个位置都要放一个数字（而N皇后是一行只放一个皇后），并检查数字是否合法，解数独的树形结构要比N皇后更宽更深。
- 一个for循环遍历棋盘的行，一个for循环遍历棋盘的列，一行一列确定下来之后，递归遍历这个位置放9个数字的可能性！
- 注意这里return false的地方，这里放return false 是有讲究的。

因为如果一行一列确定下来了，这里尝试了9个数都不行，说明这个棋盘找不到解决数独问题的解！

那么会直接返回， 这也就是为什么没有终止条件也不会永远填不满棋盘而无限递归下去！

卡哥解法
```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        self.backtracking(board)

    def backtracking(self, board: List[List[str]]) -> bool:
        # 若有解，返回True；若无解，返回False
        for i in range(len(board)): # 遍历行
            for j in range(len(board[0])):  # 遍历列
                # 若空格内已有数字，跳过
                if board[i][j] != '.': continue
                for k in range(1, 10):
                    if self.is_valid(i, j, k, board):
                        board[i][j] = str(k)
                        if self.backtracking(board): return True
                        board[i][j] = '.'
                # 若数字1-9都不能成功填入空格，返回False无解
                return False
        return True # 有解

    def is_valid(self, row: int, col: int, val: int, board: List[List[str]]) -> bool:
        # 判断同一行是否冲突
        for i in range(9):
            if board[row][i] == str(val):
                return False
        # 判断同一列是否冲突
        for j in range(9):
            if board[j][col] == str(val):
                return False
        # 判断同一九宫格是否有冲突
        start_row = (row // 3) * 3
        start_col = (col // 3) * 3
        for i in range(start_row, start_row + 3):
            for j in range(start_col, start_col + 3):
                if board[i][j] == str(val):
                    return False
        return True
```