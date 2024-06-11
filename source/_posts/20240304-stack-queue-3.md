---
title: 20240304 stack-queue-3
date: 2024-03-04 04:55:02
tags:
---

## 栈与队列

### 1. 滑动窗口最大值 #239

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        res = []
        w = deque()

        for i in range(len(nums)):
            # 窗口是递减排序，排除最后一个比当前小的数
            while w and nums[i]>w[-1]:
                w.pop()
            # 不能先推进去，那就没法比较最后一个元素，因为最后一个一直是他本身了
            w.append(nums[i])

            if i >= k-1:
                res.append(w[0])
                # 因为已经取出去最大值了，如果当前q的最大值正好的下次要被右移走的值，直接先去掉
                if nums[i-k+1] == w[0]:
                    w.popleft()
                
        return res
```

思路：
- Deque，时间复杂度为O(n)，用deque是因为是双向堆栈，不仅可以向队尾插入数据，也允许向头部添加数据，同理取数据时，头尾两侧的数据均可以取出。
- 定义一个deque，是一个递减的队列，头部一定是最大值，如果推进来的数比队尾的大，那就自动把队尾pop出去，然后再加进来一个新的，例如[1,3,-1,-3,5,3,6,7],先从 Deque 尾部向前查看已存在的数字，数字1要小于当前的3，我们可以认定数字1就是没用的那个元素,原因分析：因为当前3大于1，在向右滑动k区间的过程中，当3被移除出区间之前，1永远不会是最大的那个值，并且在3被移除出区间的时间点，数字1已经先一步被移除出区间了，因此无论如何1也不会被选作最大值，即1是无用数据
- 也就是在3被移出去之前，3在最左边，他就一直是最大的，除非，把3移走了

### 2. 前 K 个高频元素 #347

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        m = {}
        for i in range(len(nums)):
            m[nums[i]] = m.get(nums[i],0) + 1
        # freq_table = Counter(nums)
        
        # 定义小顶堆，用heap
        res = []
        for key,val in m.items():
            heapq.heappush(res, (val,key))
            if len(res)>k:
                heapq.heappop(res)
        r = [0]*k
        for i in range(len(r)):
            r[i] = heapq.heappop(res)[1]
        return r


```

思路：
- 想到的是大顶堆和小顶堆（max heap， min heap），底层结构时候二叉树，父节点永远比左右子节点大或者小
- 只排序前k个，把一个数组放进去，自动排序

时间复杂度：
- 对n个数的数组排序的时间复杂度是 O(n log n),看下面的时间复杂度的解释
- 用deque，维护前k个元素排序的时间复杂度是O(n log k)

**heapq库简介**

- heapq 提供了构建小顶堆的方法和一些对小顶堆的基本操作方法(如入堆，出堆等)，可以用于实现堆排序算法。

堆是一种基本的数据结构，堆的结构是一棵完全二叉树，并且满足堆积的性质：每个节点(叶节点除外)的值都大于等于(或都小于等于)它的子节点。
完全二叉树就是每个父节点都有两个子节点，并且最后一排的子节点都是在最左边，

堆结构分为大顶堆和小顶堆，在heapq中使用的是小顶堆：

1. 大顶堆：每个节点(叶节点除外)的值都大于等于其子节点的值，根节点的值是所有节点中最大的。

2. 小顶堆：每个节点(叶节点除外)的值都小于等于其子节点的值，根节点的值是所有节点中最小的。

- 用法：
    - 创建堆，heapq.heappush(list, ele) or heapq.heapify(list)
    - 可以用来排序，heapq.heappop()弹出的永远是堆顶的最小值
    - 使用nlargest(k,list)与nsmallest(k,list)方法，取出最大或最小的前k个数
    - 使用heapq合并两个有序列表heapq.merge(sorted(array_a), sorted(array_b))
    - heapq.heapreplace(heap, item)弹出并返回 heap 中最小的一项，同时推入新的 item，是heappush和heappop的组合
    - 这道题为什么只维护前k的元素呢？碰见大的就加入，把最小的弹出，因为根据时间复杂度，只维护k个数的时间复杂度是klogn，而正常维护n的排序需要的是nlogn，如果k比较小的是，性能就很好

**时间复杂度**
查找一个数，如果是用的二分法：
总共有n个元素，每次查找的区间大小就是n,n/2,n/4,n/8......(n/2)^k, 其中k是循环的次数，由于最后找到是一个数，所以区间越来越小，可以约等于1，即(n/2)^k=1
那么k=log2n (以2为底n的对数)，所以时间复杂度可以表示为O(logn)
那么如果是排序呢，一个数组有n个数，排序是基于二分法的，就是排序的时间复杂度就是O(nlogn)