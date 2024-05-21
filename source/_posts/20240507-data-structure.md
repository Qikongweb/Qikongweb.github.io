---
title: 20240507-data-structure-前缀和-差分数组
date: 2024-05-08 15:41:14
tags:
---

# labuladong 刷题第一题

## 数组和链表理论（优缺点）：

- 数组因为是紧凑连续存储，可以随机访问，通过索引快速找到元素，但是正因为是连续存储，一开始的内存空间就被分配完了，如果要是扩容，需要更大的空间，需要重新分配更大的空间，再把数据重新复制过去，时间复杂度是O(n),如果想在中间插入或者删除，每次都需要搬移后面的元素，所以时间复杂度也是O(n)

- 链表因为不连续，而是靠指针指向下一个元素，所以不存在数组扩容的问题，而删除或者插入的时间复杂度是O(1)，因为只要把指针变动一下，指向下一个元素就可以了，但是不能根据一个索引快速查到元素，必须要从前到后一个一个查一遍，而且因为要存指针，所以空间复杂度更大一些。

- 总结：数据结构的基本存储方式就是链式和顺序两种，基本操作就是增删查改，遍历方式无非迭代和递归。

## 数组单链表算法总结：

- 单链表常考的技巧就是双指针，后文 单链表六大技巧 全给你总结好了，这些技巧就是会者不难，难者不会。
- 数组常用的技巧有很大一部分还是双指针相关的技巧，说白了是教你如何聪明地进行穷举。
- 首先说二分搜索技巧，可以归为两端向中心的双指针。如果让你在数组中搜索元素，一个for循环穷举肯定能搞定对吧，但如果数组是有序的，二分搜索不就是一种更聪明的搜索方式么。
- 再说说 滑动窗口算法技巧，典型的快慢双指针，快慢指针中间就是滑动的「窗口」，主要用于解决子串问题
- 还有回文串相关技巧，如果判断一个串是否是回文串，使用双指针从两端向中心检查，如果寻找回文子串，从中心到两端扩展
- 最后说说 前缀和技巧 和 差分数组技巧。如果是频繁的计算子数组的和或者频繁的对子数组进行增减，那就计算出一个预设数组，避免每次都for循环

## 数组链表主要技巧就是双指针，两边向中间，中间向两边，快慢指针

## 前缀和 #303 #304

思路：
- 这道题就是如何针对频繁的对子数组加和，就是创建一个数组，是累加的数组，之后就是累加数组里面随便两个index的差就是要求的两个index的子数组之和。
- 累加和的数组长度上加1，因为在计算的时候后一个等于前一个加上nums,其实就是前面空出来一个0，为了得出left的值，不是从left来时，而是从left-1开始的。

```python
class NumArray:

    def __init__(self, nums: List[int]):
        
        self.sum_list = [0 for _ in range(len(nums)+1)] # 创建累加和数组，长度加1
        for i in range(len(nums)):
            self.sum_list[i+1] = nums[i] + self.sum_list[i] 
        # print(self.sum_list)
        

    def sumRange(self, left: int, right: int) -> int:
        return self.sum_list[right+1] - self.sum_list[left] 
```

304 二维累加和,考虑的是如何得到累加和，如何求得两个index直接的面积，就是一个dp的递推公式的问题。
无论是求累加和二维数组还是求中间的一个矩阵面积都是根据下图的一个递推公式
![dp递推公式](./20240507-data-structure/Screen%20Shot%202024-05-09%20at%206.14.13%20AM.png)
```python
class NumMatrix:

    def __init__(self, matrix: List[List[int]]):

        self.sum_list = [[0 for _ in range(len(matrix[0])+1)] for _ in range(len(matrix)+1)]
        for i in range(len(matrix)):
            for j in range(len(matrix[0])):
                self.sum_list[i+1][j+1] = self.sum_list[i+1][j] + self.sum_list[i][j+1] - self.sum_list[i][j] + matrix[i][j]
        # print(matrix,self.sum_list)


    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:

        return self.sum_list[row2+1][col2+1] - self.sum_list[row1][col2+1] - self.sum_list[row2+1][col1] + self.sum_list[row1][col1]
```

## 差分数组

## 本文讲一个和前缀和思想非常类似的算法技巧「差分数组」，差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减。

原理：
```
index i 0 1 2 3 4 5
原数组   0 0 0 0 0 0    
diff    0 0 0 0 0 0    
计算方式：         diff[i] = nums[i]-nums[i-1] i >0
从diff反推回原数组: nums[i] = diff[i] + nums[i-1]
如果给diff[i] += 3，  nums[i] = diff[i] +3 + nums[i-1], 这样就在i之后的每一个元素上都加了3
这里如果区间是i,j,那么更新第二个diff，就应该用的是j+1，而不是j，因为j属于区间内的元素要保留
如果给diff[j] += -3， nums[j] = diff[j] +3 + nums[j-1], 这样就在j之后的每一个元素上都减了3, j > i
还原方程：nums[i] = diff[i] + nums[i-1]，这里有一个推导 nums[i-1] = diff[i-1]+nums[i-2]
... nums[i] = diff[i] + diff[i-1] + diff[i-2] + ... +diff[0]
上面的就是还原方程：就是一个累加的方程
此时两个合并的话，就是在diff[i:j]的区间上的元素都加了3,这样就是花了O(1)的时间修改diff，多次修改diff，然后反推回原数组，从而达到给原数组的i，j区间做了修改
```

解法：
```python
class Solution:
    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
        
        diff_list = [0 for _ in range(length)]  #这里定义了一个差分数组，因为都是0，跟原数组一样，不用特别计算一次了
        for a in updates:
            print(a,a[0],a[1],a[2])
            diff_list[a[0]] += a[2]
            if (a[1]+1) < length:
                diff_list[a[1]+1] -= a[2] #这里对差分数组进行更新,注意的是更新j，要要更新j之后的，也就是j+1
        nums = [0 for _ in range(length)]  #这里定义了一个原数组，因为都是0，也可以省略，直接用差分数组进行计算，有点像dp压缩了一样
        nums[0] = diff_list[0]
        for i in range(1,length):
            diff_list[i] = diff_list[i] + diff_list[i-1] #这里是diff代替了原数组
            nums[i] = diff_list[i] + nums[i-1] #用的原数组，如果用原数组的话，arr[0]要用的是diff[0]
        # print(diff_list,nums)
        return diff_list
        return list(accumulate(diff_list)) 就是上面的还原方程的for循环

```
accumulate()
- 在Python中，accumulate()函数返回的是一个迭代器对象，而不是一个列表。如果你想将累加结果存储在一个列表中，可以使用list()函数将迭代器转换为列表。

- 这是因为accumulate()函数返回的是一个惰性迭代器，即它在需要时才会计算每个元素的累加结果。如果你直接将迭代器赋值给一个变量，而不进行转换，那么变量将保存迭代器对象的引用，而不是累加结果的列表。

- 使用list()函数将迭代器转换为列表可以确保你得到的是一个包含累加结果的列表，而不是迭代器对象。
- accumulate 函数可以将一个序列中的元素累积起来，返回一个新的序列。这儿的累积不仅仅是指元素相加法，也可以是乘法，甚至是自定义的运算。
```python
def op(x, y):
    return x + 3 * y

nums = [1, 2, 3, 4, 5]
print(list(accumulate(nums)))  # 默认加法累积：[1, 3, 6, 10, 15]
print(list(accumulate(nums, operator.mul)))  # 乘法累积：[1, 2, 6, 24, 120]
print(list(accumulate(nums, op)))  # 自定义累积：[1, 7, 16, 28, 43]
# ⚠️initial参数需要 python>=3.8
print(list(accumulate(nums, op, initial=2)))  # 定义初始值
```

## 前缀和 #1314

思路：
- 这里是中规中矩的方法利用的是，求前缀和，然后根据前缀和求二维子数组的和
- 初始化的时候又多加了一行和一列，对于求累加和是方便了，但是后面求二维子数组的和就很麻烦，容易乱
- dp的递推公式的本质也是前缀和

```python
class Solution:
    def matrixBlockSum(self, mat: List[List[int]], k: int) -> List[List[int]]:
        # 先构建出前缀和的数组，这里为什么要定义多一行和多一列，为了初始化方便
        # 因为求和的时候必然会用到i-1，如果没有多一行和多一列，那么初始化的时候就是第一行是一个累加和，第一列也是也一个累加和，需要单独初始化
        matrix = [[0 for _ in range(len(mat[0])+1)] for _ in range(len(mat)+1)]
        res = [[0 for _ in range(len(mat[0]))] for _ in range(len(mat))]
        # 前缀和二维数组,也可以用dp的流程做出来这个题，dp的递推公式本质也是前缀和
        for i in range(1,len(mat)+1):
            for j in range(1, len(mat[0])+1):
                matrix[i][j] = matrix[i-1][j] + matrix[i][j-1] - matrix[i-1][j-1] + mat[i-1][j-1]

        # 利用累加和数组求一个二维子数组的和，这里注意的是因为累加和有初始化，这里i+1，j+1
        for i in range(0,len(res)):
            for j in range(0, len(res[0])):
                row_min, row_max = max(0, i-k), min(len(matrix)-1, i+k+1)
                col_min, col_max = max(0, j-k), min(len(matrix[0])-1, j+k+1) 
                # print(row_min, row_max, col_min, col_max)
                res[i][j] = matrix[row_max][col_max] - matrix[row_max][col_min] - matrix[row_min][col_max] + matrix[row_min][col_min]
        # print(res)
        return res
```

新的方法：
- 这里不用初始化多一行和多一列，做累加和的方法也很特别，是一行一行的做累加，初始化都不用了
- 从第二行开始，累加和要加上同一列的上面一行的值，这样实现了，从左到右，行累加和列累加

```python
class Solution:
    def matrixBlockSum(self, mat: List[List[int]], k: int) -> List[List[int]]:
        h, w = len(mat), len( mat[0])
        integral_image = [ [ 0 for y in range(w) ] for x in range(h) ]
        

        # building integral image to speed up block sum computation
        for y in range(0, h):
            summation = 0 # 每行从0开始，向右累加
            
            for x in range(0, w):
                summation += mat[y][x]
                integral_image[y][x] = summation
                
                if y > 0: # 每列从1开始，向上累加
                    integral_image[y][x] += integral_image[y-1][x]
        
        print(integral_image)
        # compute block sum by looking-up integral image
        output_image = [ [ 0 for y in range(w) ] for x in range(h) ]
        
        for y in range(h):
            for x in range(w):
                
                min_row, max_row = max( 0, y-K), min( h-1, y+K)
                min_col, max_col = max( 0, x-K), min( w-1, x+K)
                # 这里直接等于最大的累加和，再看要不要减去
                output_image[y][x] = integral_image[max_row][max_col]
                # 这里都要判断的是大于0，而不是等于0
                if min_row > 0:
                    output_image[y][x] -= integral_image[min_row-1][max_col]
                
                if min_col > 0:
                    output_image[y][x] -= integral_image[max_row][min_col-1]
                    
                if min_col > 0 and min_row > 0:
                    output_image[y][x] += integral_image[min_row-1][min_col-1]
                
        return output_image
```
