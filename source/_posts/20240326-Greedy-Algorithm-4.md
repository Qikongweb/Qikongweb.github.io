---
title: 20240326-Greedy-Algorithm-4
date: 2024-03-26 13:13:07
tags:
---

## 贪心算法 

### 1. 柠檬水找零 #860
我自己的解法：
```python
class Solution:
    def lemonadeChange(self, bills: List[int]) -> bool:
        cash_left = {}
        for i in range(len(bills)):
            match bills[i]:
                case 5:
                    cash_left[5] = cash_left.get(5,0) + 1
                case 10:
                    if cash_left.get(5,0) == 0:
                        return False
                    cash_left[5] = cash_left.get(5,0) - 1
                    cash_left[10] = cash_left.get(10,0) + 1
                case 20:
                    if cash_left.get(10,0)>=1 and cash_left.get(5,0)>=1:
                        cash_left[5] -= 1
                        cash_left[10] -= 1
                    elif cash_left.get(5,0)>=3 :
                        cash_left[5] -= 3
                    else:
                        return False

        return True
```
思路：
- 有如下三种情况：

情况一：账单是5，直接收下。
情况二：账单是10，消耗一个5，增加一个10
情况三：账单是20，优先消耗一个10和一个5，如果不够，再消耗三个5

- 因为美元10只能给账单20找零，而美元5可以给账单10和账单20找零，美元5更万能！

- 所以局部最优：遇到账单20，优先消耗美元10，完成本次找零。全局最优：完成全部账单的找零。


### 2. 根据身高重建队列 #406

```python
class Solution:
    def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        # 从身高由高到低排序
        # 先按照h维度的身高顺序从高到低排序。确定第一个维度
        # lambda返回的是一个元组：当-x[0](维度h）相同时，再根据x[1]（维度k）从小到大排序
        people = sorted(people, key=lambda x: (-x[0],x[1]))
        # print(people)

        res = []
        for i in range(len(people)):
            index = people[i][1]
            res.insert(index, people[i])
            # print(res)
        return res
```

思路很重要：
- 又是碰见两个维度进行的比较，一定注意先一个一个维度进行比较
- 首先按身高排序从大到小，此时我们可以确定一个维度了，就是身高，前面的节点一定都比本节点高！
- 之后比较第二个维度，进行插入按照身高排序之后，优先按身高高的people的k来插入，后序插入节点也不会影响前面已经插入的节点，最终按照k的规则完成了队列
- 插入的时候要重新定义一个数组，因为在原数组上插入会改变之后的遍历过程，可能会遍历到同一个数

- 局部最优：优先按身高高的people的k来插入。插入操作过后的people满足队列属性

- 全局最优：最后都做完插入操作，整个队列满足题目队列属性




### 3. 用最少数量的箭引爆气球 #452

思路很重要：
- 为了让气球尽可能的重叠，需要对数组进行排序。这个我想到了，先排序左边界
- 如果这个区间的左边界比上个区间的右边界还大，肯定不重复，就一定结果加1
- 如果有重复的情况，就是左边界比右边界小，那么要更新右边界，来保证是不是可以和再下一个重复，次步是关键
- 如果气球重叠了，重叠气球中右边边界的最小值 之前的区间一定需要一个弓箭。
- 这道题目贪心的思路很简单也很直接，就是重复的一起射了

![卡哥解题图，更好理解](https://code-thinking-1253855093.file.myqcloud.com/pics/20201123101929791.png)

```python
class Solution:
    def findMinArrowShots(self, points: List[List[int]]) -> int:
        if len(points) == 0:
            return 0
        # sort left from min to max
        points = sorted(points, key=lambda x: x[0])
        res = 1
        for i in range(1,len(points)):
            # check if there chongdie
            if points[i-1][1] < points[i][0]:
                res += 1
            else:
                # 难点，重叠的时候需要更新右边届，取最小值，看是不是能和再下一个重叠
                points[i][1] = min(points[i][1],points[i-1][1])

        return res
```

