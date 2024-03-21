---
title: 20240315 backtracking-1
date: 2024-03-15 06:02:37
tags:
---

## BackTracking 回溯 day1

### 1. 理论基础

1. 什么是回溯法
回溯法也可以叫做回溯搜索法，它是一种搜索的方式。回溯的本质是穷举，穷举所有可能，然后选出我们想要的答案，回溯就是暴力搜索，用来搜索出来for循环做不到的情况

2. 回溯法可以解决的问题
- 组合问题：N个数里面按一定规则找出k个数的集合
- 切割问题：一个字符串按一定规则有几种切割方式
- 子集问题：一个N个数的集合里有多少符合条件的子集
- 排列问题：N个数按一定规则全排列，有几种排列方式
- 棋盘问题：N皇后，解数独等等

3. 如何理解回溯法
- 回溯法解决的问题都可以抽象为树形结构，之前做的递归时，回溯就隐藏的递归的下面
- 因为回溯法解决的都是在集合中递归查找子集，**集合的大小就构成了树的宽度，递归的深度，都构成的树的深度**。

4. 回溯法模板
- 回溯函数模板返回值以及参数
- 回溯函数终止条件，一般到叶子节点，处理终止条件
- 回溯搜索的遍历过程
![回溯过程](https://code-thinking-1253855093.file.myqcloud.com/pics/20210130173631174.png)

大家可以从图中看出for循环可以理解是横向遍历，backtracking（递归）就是纵向遍历，这样就把这棵树全遍历完了，一般来说，搜索叶子节点就是找的其中一个结果了。
回溯模版
```
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}

```

### 2. 组合 #77

思路：
- 为什么不能用for循环，如果只求两个数的和，那可以用两层for循环，但是如果是k个数之和，就要用k个for loop，解不了，所以用回溯，回溯法就用递归来解决嵌套层数的问题。
- 递归来做层叠嵌套（可以理解是开k层for循环），每一次的递归中嵌套一个for循环，那么递归就可以用于解决多层嵌套循环的问题了。

![回溯过程](https://code-thinking-1253855093.file.myqcloud.com/pics/20201123195242899.png)

- 图中可以发现n相当于树的宽度，k相当于树的深度。
如何理解上面的这句话，我的理解是树的宽度就是说循环的开始条件，是什么也就是树的根节点是什么，每一个情况对应的树的根节点就是不一样的
第二，k是树的深度，说明，这初始条件下，我往下找k次，这样就正好找到了k个元素，就是我们想要找的，之后就是一层一层回溯，数组的递归有点像遍历N叉树一样，一个叉代表一个数组的值，知道回到最初的根节点，然后for循环到下一个根节点开始往下遍历

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        self.res = []
        self.path = []
        self.backtracking(n,k,1) #为什么是从1开始，因为1代表是数组元1，2，3，而不是index在本题里
        return self.res
    # 递归函数的返回值以及参数
    def backtracking(self, n, k, startIndex):
        # 回溯函数终止条件
        if len(self.path) == k:
            self.res.append(self.path[:])
            return
        # 单层搜索的过程
        for i in range(startIndex, n+1):   # 在这一层，就开始了递归，path从空开始，当i等于1时，进入到第一个数的递归，也就是后面的递归是从2开始往path里加数，然后等k个组合都完事了，pop出去，最后返回的是path为空，进入i=2的第二层循环，也就是i是值传递，不会被后面的影响
            # print(i)
            self.path.append(i)
            self.backtracking(n,k, i+1)
            self.path.pop()

        

```

剪枝：

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        result = []  # 存放结果集
        self.backtracking(n, k, 1, [], result)
        return result
    def backtracking(self, n, k, startIndex, path, result):
        if len(path) == k:
            result.append(path[:])
            return
        for i in range(startIndex, n - (k - len(path)) + 2):  # 优化的地方path循环的区间是左闭右开【0，3）所以是+2
            path.append(i)  # 处理节点
            self.backtracking(n, k, i + 1, path, result)
            path.pop()  # 回溯，撤销处理的节点
```
思路：
- 可以剪枝的地方就在递归中每一层的for循环所选择的起始位置。

如果for循环选择的起始位置之后的元素个数 已经不足 我们需要的元素个数了，那么就没有必要搜索了。

- 剪枝的步骤
    1. 已经选择的元素个数：path.size();

    2. 还需要的元素个数为: k - path.size();

    3. 在集合n中至多要从该起始位置 : n - (k - path.size()) + 1，开始遍历



![剪枝优化过程](https://code-thinking-1253855093.file.myqcloud.com/pics/20210130194335207-20230310134409532.png)