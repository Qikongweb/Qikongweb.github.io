---
title: 20240226 hash table
date: 2024-02-24 16:25:41
tags:
---

## Hash Table

### 1. 有效的字母异位词 #242

```python
# 数组解法
arr = [0] * 26
for ele in s:
    arr[ord(ele) - ord('a')] += 1
for ele2 in t:
    arr[ord(ele2) - ord('a')] -= 1

for i in arr:
    if i != 0:
        return False
return True

# defaultDic 解法
from collections import defaultdict
s_dict = defaultdict(int)
t_dict = defaultdict(int)
for e in s:
    s_dict[e] += 1
for x in t:
    t_dict[x] += 1
return s_dict == t_dict

# counter 解法
from collections import Counter
x = Counter(s)
y = Counter(t)
return x == y
```

## 思路
- 这道哈希表的题首先考虑用数组，在存的值不是特别大的话，可以用数组，用数组运行快，比set和map结构简单，但是不适合用数组的场景是，数据量特别大，或者存储的位置不连续，占用空间就得开的很大
- ord()和chr()是两个关于ASCII的转换的函数，ord是把一个字符串（Unicode 字符）作为参数，返回对应的ASCII 数值，或者Unicode 数值。chr主要用来表示ascii码对应的字符，可以用十进制，也可以用十六进制。
ord : 字符串=>ASCII  chr : ASCII => 字符串
- ***python里面是可以直接比较两个dict是否相等***
- defaultdic (字母都是小写)，Counter (第一个字母C是的大写)

### 2. 两个数组的交集 #349

哈希表使用场景：给一个元素，判断在这个集合是否出现过，快速查找

```python
# 自己想出来的解法，我循环短的数组，然后判断是不是在另一个长的里面，且在结果集里面
res = []
size1 = len(nums1)
size2 = len(nums2)
if(size1 > size2):
    for s in nums2:
        if s in nums1 and s not in res:
            res.append(s)
else:
    for s in nums1:
        if s in nums2 and s not in res:
            res.append(s)
return res

# 使用字典和集合 推荐解法
table = {}
res = set() # 定义一个空set是为了去重

for s in nums1:
    # table[s] = 1 # my code
    table[s] = table.get(s,0) + 1 # 如果s存在table里，返回对应的值加1，如果不存在返回默认值0加1
for t in nums2:
    if t in table:
        res.add(t) # 添加一个ele到set里
        del table[t] # 删除一个set里面的key，为了去重
return list(res) # 转set到list

# 数组解法例子
count1 = [0]*1001
count2 = [0]*1001
result = []
for i in range(len(nums1)):
    count1[nums1[i]]+=1
for j in range(len(nums2)):
    count2[nums2[j]]+=1
for k in range(1001):
    if count1[k]*count2[k]>0:
        result.append(k)
return result
```
python语法：
- table[num] = table.get(num,0) + 1，table字典中获取键为num的-值，如果num不存在于字典中，则返回默认值0。然后将获取到的值加1。
- del my_dict['a']，del关键字被用来删除字典my_dict中的键为'a'的键值对。


### 3. 快乐数 #202

```python
table = set()
        
while n != 1:
    # res = 0
    # while n > 0:
    #     digit = n % 10
    #     res = res + digit * digit
    #     n = n // 10
    
    n = sum(int(x)**2 for x in str(n))
    if n in table:
        return False
    else:
        table.add(n)

return True
```
思路：不难，语法不太熟，
- python sum(list) ex: sum([1,2,3,4])
- n 不等于1的时候进去循环，然后不断更新n，并把n存在table里，如果table里找到n，有循环就返回，如果结果是1也返回

### 4. 两数之和 #1

```python
table = {}

for x in range(0, len(nums)):
    if nums[x] in table and x != table[nums[x]]:
        return [x, table[nums[x]]]
    table[target-nums[x]] = x
    
return []  
```

思路：
- 这个元素是否在集合中出现过，就要想到用hash map,这道题在遍历其中的元素的时候要知道以前的元素是否跟当前的有匹配，如果有的话就返回正确的值了，这样就是把之前所有遍历过的元素放在一个表里，让当前的元素快速查找，所有就想到了哈希表
- 知道哈希表之后，用数组，还是set，还是dict，因为要保存index以及数值，所以我们要用dict，也就是map

**Hash table in Python**
### 哈希表对应数据结构有数组，set，map
### 在Python中，Set 和 Map 是两种不同的数据结构，它们有以下区别：

1. Set（集合）是一种无序且元素唯一的数据结构，它不包含重复的元素。在集合中，每个元素都是独一无二的。例如：

```python
my_set = {1, 2, 3, 4, 5}
```

2. Map（字典）是一种键值对的数据结构，它包含了键（key）和值（value）之间的映射关系。在字典中，键是唯一的，但值可以重复。例如：

```python
my_dict = {'apple': 2, 'banana': 3, 'cherry': 5}

```
总的来说，Set 是一种用于存储唯一元素的数据结构，而 Map 是一种用于存储键值对映射关系的数据结构。在Python中，Set 对应的数据结构是集合（set），Map 对应的数据结构是字典（dictionary）。

**defaultdic in Python**
### 1. 为什么要用defaultdic？想拿dic里面的一个值，报错了，没办法，进行下去，如果有一个默认值，就可以继续查找
- 在字典中查找某个值时，若key不存在时则会返回一个KeyError错误而不是一个默认值，这时候可以使用defaultdict函数。

**注意：使用dict[key]时，若key不存在则报错；使用dict.get(key)时，若key不存在则会返回一个默认值。**

**注意：key不存在dict当中时，dict[key] = value是赋值，不会报错；key不存在dict当中，以dict[key]方式获取值，会报错**

### 2.返回的默认是都可以是什么？int=>0 list=>[], str=>"", set=>()
- defaultdict接受一个工厂函数作为参数，如下来构造：

```
dict =defaultdict( factory_function)
```

factory_function可以是list、set、str等等，作用是当key不存在时，返回的是工厂函数的默认值，比如list对应[ ]，str对应的是空字符串，set对应set( )，int对应0。

***defaultdict是python内建dict类的一个字类，功能与dict相同，但它带有一个默认的值，若key值不存在时返回一个默认的值。***


### 3. counter 在python里是什么？
Counter（）是collections里面的一个类，作用是计算出字符串或者列表等中不同元素出现的个数，返回值可以理解为一个字典
***看例子就明白了***
```
s = 'abbccc'
res = Counter(s)
print(res) #Counter({'c': 3, 'b': 2, 'a': 1})
```