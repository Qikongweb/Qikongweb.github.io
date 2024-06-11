---
title: 202405027_merge_array
date: 2024-05-27 16:01:45
tags:
---

## merge two sorted List #23

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        
        dump = ListNode(-1)
        dir_dump = dump # 合并后的指针

        dir1 = list1
        dir2 = list2
        while dir1 and dir2:
            if dir1.val < dir2.val:
                dir_dump.next = dir1
                dir1 = dir1.next
            else:
                dir_dump.next = dir2
                dir2 = dir2.next
            dir_dump = dir_dump.next
        if dir1 is not None:
            dir_dump.next = dir1
        if dir2 is not None:
            dir_dump.next = dir2
        return dump.next
```

## merge sorted array #88
思路：
- 需要灵活解题，思路很巧妙，我想到了双指针，但是没有想到遍历顺序，正常来说都是从前往后遍历，
- 但 后面是空的呀，所以这道题需要我们稍微变通一下:将双指针初始化在数组的尾部，然后从后向前进行合并，即使覆盖了nums1的内容，但是内容早已用过了，所以不会影响答案
- 参考合并两个有序链表，但是链表是从dummy node开始的，只是改变指针的方向，是从头开始合并的，但是不适用于数组。为了不覆盖nums1，所以想到从后向前遍历

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        p1 = m - 1 # 数组1的指针，这里不能是从len(nums1)开始，因为要从比较的数组的最后一位开始
        p2 = n - 1 # 数组2的指针
        # print(p1,p2)
        p = len(nums1) - 1 # 从最后一位开始，后面的是空

        while p1 >=0 and p2 >= 0:
            if nums1[p1] >= nums2[p2]:
                nums1[p] = nums1[p1]
                p1 -= 1
            else:
                nums1[p] = nums2[p2]
                p2 -= 1
            p -= 1
        # 如果都比较完之后，可能会有p1或者p2还没有走完，这里不需要考虑p1，因为p1本来就在里面了，主要考虑p2是不是还有剩余的元素
        # while p1 >= 0:
        #     nums1[p] = nums1[p1]
        #     p1 -= 1
        #     p -= 1
        while p2 >= 0:
            nums1[p] = nums2[p2]
            p2 -= 1
            p -= 1
        return nums1

```

## move zero elements #283
双指针的方法，太容易不写了

## remove duplicate from sorted Array II

思路：
- 这道题是去重，一个元素最多只能出现两次，把不符合规律的是放在最后
- 首先这道题是排序后的，马上想到用双指针，跟上一个题283完全一样，不一样的是这里每个数可以出现最多两次
- 这里要判断符合条件的数的条件有所不一样，要判断的不等于slow-2的数
- 为什么呢？因为slow记录的符合规则的数，fast(i)记录的是要更新的数。
- 方法就是如果符合规则和slow交换，然后slow+1，不符合规则就什么都不变，fast(i)每次都往后移动一位

```python
        slow = 2
        for i in range(2,len(nums)):
            
            if nums[i] != nums[slow-2]:
                nums[slow], nums[i] = nums[i], nums[slow]
                slow += 1
        return slow
```

## Interval List Intersections 986

思路：
- 双指针的方法，因为这里定义的是一个区间，比较区间的左边届和右边界，取左边届的最大值和右边界的最小值就是要保存的结果
- 之后如果移动指针呢？比较右边界谁小谁往后移动一组区间
```python
class Solution:
    def intervalIntersection(self, firstList: List[List[int]], secondList: List[List[int]]) -> List[List[int]]:
        # two pointers method
        if len(firstList) == 0 or len(secondList) == 0:
            return []
        i = 0
        j = 0
        res = []

        while i < len(firstList) and j < len(secondList):
            left = max(firstList[i][0], secondList[j][0])
            right = min(firstList[i][1], secondList[j][1])
            if left <= right:
                res.append([left,right])
            if firstList[i][1] > secondList[j][1]:
                j += 1
            else:
                i += 1
        return res
```

## squares of sorted list #977

思路：
- 同样是双指针，双指针一前一后，因为要比较的平方后的最大值，所以出现的位置一定是在两端。

## 田忌赛马 双指针 #870 Advanced shuffle

思路：
- 将⻬王和田忌的⻢按照战斗力排序，然后按照排名一一对比。如果田忌的⻢能赢，那就比赛，如果赢不了，那就换个垫底的来送人头，保存实力。
- 两个数组都排序，双指针，每个都指向其中一个，拿双方最大的值比较，如果不行就拿一个最小的数抵上




