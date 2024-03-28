---
title: 20240328-Greedy-Algorithm-6
date: 2024-03-27 13:28:55
tags:
---

## 贪心算法 

### 单调递增的数字 #738

```python
class Solution:
    def monotoneIncreasingDigits(self, n: int) -> int:
        arr = list(str(n))

        for i in range(len(arr)-1, 0, -1):
            print(i, arr[i], arr[i-1])
            if arr[i-1] > arr[i]:
                # 将修改位置后面的字符都设置为9，因为修改前一个字符可能破坏了递增性质，构造一个9的数组直接替换i后面的所有数组为9
                arr[i:] = ['9'] * (len(arr)-i)
                # 将前一个字符减1
                arr[i-1] = str(int(arr[i-1]) - 1)
        res = "".join(map(str, arr))
        return int(res)
```


另一种解法，立flag，把flag后面的位全替换成9
```python
class Solution:
    def monotoneIncreasingDigits(self, N: int) -> int:
        # 将整数转换为字符串
        strNum = str(N)
        # flag用来标记赋值9从哪里开始
        # 设置为字符串长度，为了防止第二个for循环在flag没有被赋值的情况下执行
        flag = len(strNum)
        
        # 从右往左遍历字符串
        for i in range(len(strNum) - 1, 0, -1):
            # 如果当前字符比前一个字符小，说明需要修改前一个字符
            if strNum[i - 1] > strNum[i]:
                flag = i  # 更新flag的值，记录需要修改的位置
                # 将前一个字符减1，以保证递增性质
                strNum = strNum[:i - 1] + str(int(strNum[i - 1]) - 1) + strNum[i:]
        
        # 将flag位置及之后的字符都修改为9，以保证最大的递增数字
        for i in range(flag, len(strNum)):
            strNum = strNum[:i] + '9' + strNum[i + 1:]
        
        # 将最终的字符串转换回整数并返回
        return int(strNum)
```

思路：
- 题目要求小于等于N的最大单调递增的整数，那么拿一个两位的数字来举例。

例如：98，一旦出现strNum[i - 1] > strNum[i]的情况（非单调递增），首先想让strNum[i - 1]--，然后strNum[i]给为9，这样这个整数就是89，即小于98的最大的单调递增整数。

例如：1000，如果只把大于后面的数字替换成9，就是900，不是正确结果，999才是，所以要把后面所有的数字都变成9，所以注定要从后往前遍历

- 两种解法思路是一样的，实现不一样，第一种是先int==》str==》list，好处是可以随时修改list任意位置的值，最后再转回来list==》str==》int
第二种解法是只有int==》str，在str的上面改变数字，要是涉及拼接字符串了，我比较喜欢第一种方法


### 监控二叉树 #968 跳过