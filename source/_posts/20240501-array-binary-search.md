---
title: 20240501-array-binary-search
date: 2024-05-02 07:06:00
tags:
---

## Array Binary Search

## 1. Binary Search #704

思路：
- 根据所选的区间决定之后程序里的条件和遍历等式，有两个可选用的区间，左闭右开，左闭右闭
这里比较[left, right], [left, right)
程序里？的位置是根据选择上面的哪个区间决定的
```python
left = 0
right = ? len(nums)-1@1  len(nums)@2, #一种情况，区间包括right，包括右边界所以为了nums的合法行，就必须从len(nums)-1开始，如果是第二种情况，区间不包括右边界，所以right可以从len(nums)开始
while left ? right:  #第一种情况如何要合法行的话，<=都可以，因为包括右边界，例如[1,1],第二种情况，不包括右边界，如果保证合法性的话就只能是小于号，< 例如[1,1)这是不对的
    mid = (left + right) //2
    if nums[mid] > target:
        right = mid ? # 第一种情况右边界是闭合的，如果上面比较过一次mid了之后，这里有边界可以跳过mid，变成mid-1，第二种情况右边界是打开的，不包括右边界，所以这里右边界可以直接等于mid
    elif nums[mid] < target:
        left = mid + 1  #因为左边一直都是闭合的，所以如果mid已经从来比较一次了，下一次就不用比较mid，跳过，从mid+1开始
    else:
        return mid

```
```
这里有两个原则，一个是是否合法，也就是进入循环的条件的合法性，另一个就是考虑，right是否包含上一个mid
|                 | ?#1          | ?#2   | ?#3       |
|-----------------|--------------|-------|-----------|
| [left,right]    | len(nums)-1  |  <=   |  mid - 1  |
| [left,right)    | len(nums)    |  <    |  mid      |
```

## 2. 移除元素 #27

思路：
- 这道题就是把所有不等于要删除的元素的值放在前面，返回的是有几个，但是要改变的是这个数组的结构
- 把不删除的元素全部放在前面，返回的是最后一个index
- 需要用到快慢指针，慢指针是获取数组中需要更新的位置，快指针是获取新数组要更新的元素，因为快指针是index，每次都往后走一个，碰到不是要删除的元素就需要跟慢指针的位置交换一下，这样慢指针永远都是记录的不删的元素，一直到快指针走完全程，如果慢指针不走了，那么说明后面都是要删除的元素。

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:

        slow = 0
        fast = 0

        while fast < len(nums):
            if nums[fast] != val:
                nums[slow] = nums[fast]
                slow += 1
            fast += 1
        # print(nums)
        return slow
        # 第二种方法：是用index作为快指针
        for i in range(len(nums)):
            if nums[i] != val:
                nums[i], nums[slow] = nums[slow], nums[i]
                slow += 1
        return slow
```

## 2. Squares of sorted array #977

```python
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        left = 0
        right = len(nums) - 1
        i = len(nums) - 1 # 指向结果集最后一个位置
        result = [ float('-inf') for _ in range(len(nums))]
        # 思路，因为已经是排序后的数组，前面的数平方之后要不就是最后面要不就是最前面,这个不对，
        # 因为前面的负数如果平方后可能会再中间，或者后面，未必一定在最后
        # 但是最大值，一定出现在两端，所以我就比较两端，哪个大就插入到结果里，从后往前插入，因为返回的是从小打大排列的数组
        while left <= right:
            # print(left, right)
            if nums[left]*nums[left] > nums[right]*nums[right] :
                result[i] = nums[left] * nums[left]
                left += 1
                
            else:
                result[i] =  nums[right] * nums[right]
                right -= 1
            i -= 1
        return result
```

## 3. Minimum Size Subarray Sum

思路：
- 双指针，滑动窗口，注意的是是不是有指针可以用index来代替，这样一个for循环就好了。
- 滑动窗口就是调节左右指针的位置，如果窗口里面没有满足要求，那么不断扩大右指针，如果窗口满足要求了，就开始缩小左指针了，这样才能找到最小的窗口

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:

        # 这道题不能排序，因为要找的是最小的符合条件的长度
        result = float('inf')
        left = 0
        right = 0
        sum = 0
        while right <len(nums):
            sum += nums[right]
            # print(left,right, sum, result)
            while sum >= target:
                if (right-left + 1) < result:
                    result = right-left + 1
                
                # print(left,right, sum, result)
                sum -= nums[left]
                left += 1
            right += 1
        if result == float('inf'):
            return 0
        else:
            return result
```

## 4. Fruits into basket #904

思路：
- 双指针，滑动窗口，这道题的意思是只能选两种水果，最多能采多少颗树，两种水果意味着，数组里的值，只能选两个，而且还的是挨着，计算最多采多少颗树，就是求最长的子序列的长度
- 难点在于，我用了hash map，记录了两种水果，但是如果新加入一种水果之后，如果才能把最早加入的水果去掉。而不是去掉后面加入进来的水果，这里有两种方法：
    - 第一种是hash map里记录的是某一种水果的下标，left更新的时候就是用要删除的key对应的下标加1，为什么保留left呢？要求的是子序列的长度，所以right-left+1
    - 第二种解法是hash map存的是水果树的数量，这样的话，left只保留了初始位置，要删除key对应的值，就是一个一个删，每删一个left加1

个人喜欢第一种方法
```python
class Solution:
    def totalFruit(self, fruits: List[int]) -> int:
        hash_map = {}
        result = 0
        left = 0 #//我们采摘的第一种果子的起始下标
        for i in range(len(fruits)):
            if (fruits[i]) in hash_map:
                hash_map[fruits[i]] = i #//不断更新果子的最后出现的下标
            else:
                if len(hash_map) < 2:
                    hash_map[fruits[i]] = i
                else:
                    key_pop = min(hash_map, key=hash_map.get) #//求出记录较小的下标，就是准备要删的key
                    left = hash_map[key_pop] + 1 #更新left，就是要删除的下标加1
                    del hash_map[key_pop]  #删除我们采摘的第一种果子的下标
                    hash_map[fruits[i]] = i #加入一种新果子的下标
            result = max(result, i-left+1) #//我们每次都需要更新采摘果子的最大值，不论是否出现第三种果子
                
        return result
```

第二种方法：
```python
class Solution:
    def totalFruit(self, fruits: List[int]) -> int:
        hash_map = {}
        result = 0
        left = 0
        for i in range(len(fruits)):
            if (fruits[i]) in hash_map:
                hash_map[fruits[i]] += 1
            else:
                if len(hash_map) < 2:
                    hash_map[fruits[i]] = 1
                else:
                    while hash_map[fruits[left]] > 0: # 这里是关键，其实就是如果更新left，这里是基于，减少一个value，left加1，往前移动一个，一直到都减到0，那么就把这个key删掉
                        hash_map[fruits[left]] -= 1
                        if hash_map[fruits[left]] == 0:
                            del hash_map[fruits[left]]
                            hash_map[fruits[i]] = 1
                            left += 1
                            break
                        left += 1
                   
            result = max(result, i-left+1)
                
        return result
```

## 新的关于搜索求左边届和右边界

使用场景？
- 在有序数组的条件下可能会遇到多个target连在一起，那么就涉及到算法求最左面还是最右面的边界值，常用到的是搜索左边届还是右边界，但是应用题里通常会让你求最值。
一定是在有序数组条件下，满足下图的都可以用二分搜索：

![二分搜索](./20240507-data-structure/Screen%20Shot%202024-05-14%20at%205.50.42%20AM.png)

搜索左边界模版，右边界模版,用的是左闭右开区间
```python 搜索左边界模版
left = 0, right=len(nums)
while left < right:
    mid = (right + left) //2
    if nums[mid] == target:
        right = mid # 如果是右边界的话就是left = mid+1
    elif nums[mid] > target:
        right = mid
    else:
        left = mid + 1
```

## 1011 Capacity To Ship Packages Within D days

思路：
- 可以联想到二分搜索，因为符合上面的图，只是斜率是-1，虽然是相反，但是也是可以用二分搜索
- 分析，如果归纳到二分搜索target 就是天数，运载能力就是自变量x，天数和运载能力成反比，所以f(x) = function(capacity),x的left就是max（weight)需要的天数是len（weight）（最大的天数），right就是sum(weights),需要的天数是1
- 我是明确计算出了，每个capacity对应的天数,
- 我通过做了下面的题，觉得没必要明确计算出来capacity对应的天数，因为只需要在满足max_weight条件下，如果计算得到的天数还是大于target的天数，那么就不管怎么分配天数都会大于target。更容易进入下一次的比较。

```python
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:

        # target 就是天数，运载能力就是自变量x，天数和运载能力成反比，所以f(x) = function(capacity),
        # x的left就是max（weight)需要的天数是len（weight）（最大的天数），right就是sum(weights),需要的天数是1

        left = max(weights) # x 坐标
        right = sum(weights)
        day_min = 1 # y 坐标
        day_max = len(weights)

        while left < right:
            mid = (left+right) //2
            days_mid = self.calDays(mid, weights)  # 根据一个承载重量，算出来多少天可以运完
            # print(mid,days_mid)
            if days_mid == days: # 这里可以优化合并一下，变成<=
                right = mid 
            elif days_mid < days:
                right = mid
            else:
                left = mid + 1
        return left
    
    def calDays(self, capcity, weights):
        days_total = 0
        w = 0

        for i in weights:
            w += i
            if w > capcity: # 这里是如果总的值大于capcity，那么total要统计到下一天
                days_total += 1
                w = i
            elif w == capcity: # 如果正好等于capacity，那么就从0开始
                days_total += 1
                w = 0
                
        if w > 0 : 
            days_total += 1
        return days_total
```

另一个解法：
不会明确计算出来capacity对应的天数，因为只需要在满足max_weight条件下，如果计算得到的天数还是大于target的天数，那么就不管怎么分配天数都会大于target。更容易进入下一次的比较。

```python
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:

        # target 就是天数，运载能力就是自变量x，天数和运载能力成反比，所以f(x) = function(capacity),
        # x的left就是max（weight)需要的天数是len（weight）（最大的天数），right就是sum(weights),需要的天数是1

        # print(max(weights), sum(weights))
        left = max(weights) # x 坐标
        right = sum(weights)

        while left < right:
            mid = (left+right) //2
            days_mid = self.isLarger(mid, weights, days)  # 根据一个承载重量，算出来多少天可以运完
            # print(mid,days_mid)
            if days_mid:
                left = mid + 1
            else:
                right = mid
        return left
    
    def isLarger(self, capcity, weights,days):
        days_total = 1 # 这里初始化1，因为至少有一天
        w = 0

        for i in weights:
            w += i
            if w > capcity:
                days_total += 1
                w = i
                if days_total > days: # 这里如果大于days，没必要比较下面的了
                    return True
        
        return False

```

## 410 Split Array largest Sum

```python
class Solution:
    def splitArray(self, nums: List[int], k: int) -> int:
        # 简单说一下思路，二分搜索的思路，如果子数组的最大和是sum,就是划分一个子数组，如果最大和是max，就是分割len(nums)次
        # 如何确定横坐标和纵坐标，因为用二分搜索最后得出来的是left的值，也就是横坐标是我们要求的最大子数组的和
        # 纵坐标是在一个最大子数组和确定的时候，可以分割几个子数组
        # target = k，横坐标是最大值的和，纵坐标是分的次数
        # 可以认为二分搜索是一个递减的曲线，也就是在mid等于k的时候，取最左边的值才是最大的子数组的和

        left = max(nums)
        right = sum(nums)
        # print(val_max_left,val_max_right)
        # 下面是二分搜索，左闭右开区间
        while left < right: # 如果加=，就是【1，1）不合法区间
            mid = (right+left)//2
            val_max_mid = self.isLargerTarget(mid, k, nums) # 这里只比较不用具体计算出来到底是多少
            if val_max_mid : # mid > k
                left = mid + 1
            else:  # mid <= k
                right = mid
        return left
    def isLargerTarget(self, max_num, k, nums):
        count = 1
        curr_sum = 0
        # 这里统计的是在最大值小于max_num的情况下，能划分多个个子数组，如果想要划分最少的子数组
        # 那么每个子数组的和都是越接近max_num，这样才能保证子数组的个数最小，如果是在这种情况下，最小的
        # 划分子数组的次数都要大于k，那么无论怎么划分子数组都肯定大于k,k就是target
        for num in nums:
            curr_sum += num
            if curr_sum > max_num:
                count += 1
                curr_sum = num
                if count > k:
                    return True
        return False
```

## 35 Search Insert Position

- 有公式的推导过程

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        
        # 二分法： 左闭右开
        left = 0
        right = len(nums)
        while left < right:
            mid = (left + right)//2
            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                right = mid
            else:
                left = mid + 1
        return right
        # 这里为什么返回的是right，因为截止的时候一定是left==right，而最后一次的循环是应该right-left = 1
        # 这个时候，mid= (left+right)//2= (2(left)+1)//2 = left
        # 分两种情况，一种是mid>target, right=mid=left,return mid,left,right哪个都行
        # 第二种情况是mid<target, left=mid+1, 这时right仍然是right=left+1, return 应该是返回后一个值，left+1,right,mid+2
        # 综合上面两种情况，都能统一返回一个值的话就是right
```

