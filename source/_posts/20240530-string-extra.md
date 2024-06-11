---
title: 20240530_string_extra
date: 2024-05-29 16:49:32
tags:
---

## python string 处理的方法

## 原理：

- 字符串(str)是 Python 中最常用的数据类型，可以使用单引号或双引号来创建字符串。注意：

- Python不支持单字符类型(对应 c 语言中的 char)，单字符在 Python 中也是字符串类型。
字符串是不可变类型，即无法直接修改字符串的某一索引对应的字符，需要转换为列表处理。可以认为字符串是特殊的元组类型。

- 不可以已给某一个index赋值或者交换，所以上指针的时候需要交换两个指针的元素就不能使用了。

## 如果处理字符串？有一些方法

- 空格剥离， 去掉前面或者后面，或者前后的所有空格
    - lstrip() remove all space in the front
    - rstrip() remove all space at the back
    - strip() remove all space in the front and back

- 字符串拆分, spit(par)
    - 如果没有par，默认是根据空格拆分的
    - par也可以是任何的字符

- 将列表元素合成字符串 join(), 拆分之后如何合并呢？
    - "".join(list) 所有元素全部拼接在一起
    - " ".join(list) 所有元素全部用空格拼接在一起

- 如何反转字符串，很有意思，可以通过上面的拆分再合并实现，也可以用数组本身的特性
    - str.split(), res.reserve(), " ".join(res)
    - str[::-1] 字符串本身也是可以反转的

- 检查是否有字符串成员，这个方法和map，list是一样的用in
    - "abc" in "abcdfe" return Ture

- 找字符串的位置 find()
    - 返回子字符串第一次出现的第一个字符的索引，如果找不到子字符串，则返回-1

- 子字符串替换 replace(par1,par2) 把par1替换成par2
    - str.replace("a","abc")

- 同字母异序词检查， 用counter,不用counter的话要用map统计每个字母出现的次数，或者用list
    ```python
    from collections import Counter
    def is_anagram(s1, s2):  
        return Counter(s1) == Counter(s2)
    ```

- 回文检查
    - 创建一个单词的反转，然后和原来的比较是否是相等，用的是上面反转字符串的方法

## 151 reverse words in a string

思路比较简单
- 去多余的空格
- 反转整个数组
- 反转数组里每个单词
- python里面对字符串不能直接赋值操作，所以先转换成list，之后再用join连在一起

```python
class Solution:
    def reverseWords(self, s: str) -> str:
        # remove extra space
        str_list = list(s) # 拆成每个字符
        new_arr = self.removeExtraSpace(str_list)
        # 全部反转
        new_arr = self.reversOneWord(new_arr)
        # 反转每个单词
        start = 0
        for i in range(len(new_arr)+1):
            if i == len(new_arr) or new_arr[i] == ' ':
                # 开始反转单词
                new_arr[start:i] = self.reversOneWord(new_arr[start:i])
                start = i + 1
           
        # print(str_list_without_space)
        return "".join(new_arr)


    def removeExtraSpace(self, str_list):
        slow = 0
        i = 0
        while i < len(str_list): # 为什么要用while而不是用for，因为下面是一整个单词一起遍历
            if str_list[i] != " ": #不等于空格开始
                # 在每个单词的前面添加一个空格，除了第一个位置外
                if slow != 0: 
                    str_list[slow] = " "
                    slow += 1
                while i < len(str_list) and str_list[i] != " ": # 把整个单词都往前移动
                    str_list[slow] = str_list[i]
                    slow += 1
                    i += 1
            else:
                i += 1
        # print("".join(str_list[:slow]))
        return str_list[:slow]
    
       
    def reversOneWord (self, s: list):
        left = 0
        right = len(s)-1
        while left < right:
            s[left], s[right] = s[right], s[left]
            left += 1
            right -= 1
        return s 
```


## KMP算法

定义：
- KMP的思想就是当字符串发生不匹配时,根据已经知道的一部分匹配内容来避免重头匹配

解决什么问题？
- 解决的是一个字符串里是否包含另一个字符串，当不匹配的时候，需要往后移动几个字符而不是一个字符一个字符的移动，这样可以减少循环的次数，提高效率，可以认为是剪枝，但是到底移动几个字符呢？需要从前缀表里面查询知道，所以如果求出前缀表并利用前缀表里的元素就是解决匹配字符串少循环的关键

需要了解的概念？
- 前缀，后缀，最长的公共前后缀，前缀表（prefix table）
    - 一个字符串里面除了最后一个字符，其他的全部头部组合的连续子串，就是前缀
    - 一个字符串里面除了第一个字符，其他的全部尾部组合的连续子串，就是后缀
    - 最长的公共前后缀，是一个字符串最长且相等的前缀和后缀。
    - 前缀表，存储一个字符串所有的从最左边的字符到当前字符构成的字串的最长公共前后缀的长度,记录下标i之前（包括i）的字符串中，有多大长度的相同前缀后缀。
- next数组
    - 是由前缀表的数组变化而来，通常就是右移一位，在最左边补-1

- 有点发现：
    在前缀表求前缀的时候是每往后移动一位，那么就在前缀组合里每一个情况最后都加入新的字符，空加新字符就是新字符
    在求后缀的时候是每往后移动一位，那么就在后缀组合里每一个情况的第一位都加入新的字符，空加新字符就是新字符，因为后缀是从后往前组合而来的。
举例：
![前缀表举例](https://img-blog.csdnimg.cn/80bf1ace54aa47aaaa28da4f5cf676bc.png)

## 知道了上面的前缀表如何求出来的，如何写代码得到前缀表?

定义两个指针i和j
- j指向真前缀末尾位置(同时j+1是最长相等真前后缀的长度)，那么如果j=0，就是在有两个字符的条件下
- i指向真后缀末尾位置。真后缀的首字母是真前缀的最后一个字符，真后缀的末尾是真前缀的第一个字符
- 初始化:j从0开始遍历模式串,i从1开始(因为真后缀不包括首字符)

这里是动态规划的思想

真前缀末尾位置的字符==i指向真后缀末尾位置的字符,最长相等前一最长真前后缀的长度加1

真前缀末尾位置的字符!=i指向真后缀末尾位置的字符,根据KMP的思想,我们可以回退到可能匹配的位置,j = next[j-1]如果匹配成功 ,长度等于j+1,匹配不成功继续回退

边界条件,当回退到j=0时并且s[i]和s[j]不相等,next[i]=j.(j=0,此时最长相等真前后缀长度为0)


因为i是一直往后找的，j用来看前缀是不是和后缀的字母一样
j还代表着i之前包括i的字串的最长相等前后缀的长度，其实就是next里面的值

一个自认为比较容易的理解方法就是把这一个串自己当作文本串(i)和模式串(j) 发现两者不匹配 那就回退到前缀相同的位置 即数组中前一个下标

```c
void getNext(int* next, const string& s){
  int j = 0;//初始化,next数组起始位置为0
  next[0] = j;
  for(int i = 1;i < s.size();i++){
   while (j > 0 && s[i] != s[j]){
     j = next[j-1];//回退到可能匹配的位置
   }
   if(s[i] == s[j]){
   j++;//如果匹配,最长相等真前后缀长度加1
   }
   next[i] = j;//j ==0 && s[i]==s[j] 的情况在这里体现
  }
}


int kmp(string l,string needle){
   if(needle.size( ) == 0){
     reutrn 0;//模式串长度为0
   }
   int next[needle.size( )];//建立next数组
   getNext(next, needle);
   int j = 0;
   for(int i = 0; i < l.size( ); i++){//开始匹配
   while (j > 0 && l[i] != needle[j]){//匹配不成功回退
         j = next[j-1];
   }
   if( l[i] == needle[j]){
     j++;//匹配成功就匹配下一个字符
   }
   if (j == needle.size){//匹配完了就返回首字母的位置
   return (i-needle.size( )+1);
   }
    }
   return -1;
}
```
                        
原文链接：https://blog.csdn.net/2301_76352996/article/details/132039757

- 如何使用前缀表?

假设n为文本串长度，m为模式串长度，因为在匹配的过程中，根据前缀表不断调整匹配的位置，可以看出匹配的过程是O(n)，之前还要单独生成next数组，时间复杂度是O(m)。所以整个KMP算法的时间复杂度是O(n+m)的。
暴力的解法显而易见是O(n × m)，所以KMP在字符串匹配中极大地提高了搜索的效率。

python 解法

```python （不减一）
class Solution:
    def getNext(self, next: List[int], s: str) -> None:
        j = 0
        next[0] = 0
        for i in range(1, len(s)):
            while j > 0 and s[i] != s[j]:
                j = next[j - 1]
            if s[i] == s[j]:
                j += 1
            next[i] = j
    
    def strStr(self, haystack: str, needle: str) -> int:
        if len(needle) == 0:
            return 0
        next = [0] * len(needle)
        self.getNext(next, needle)
        j = 0
        for i in range(len(haystack)):
            while j > 0 and haystack[i] != needle[j]:
                j = next[j - 1]
            if haystack[i] == needle[j]:
                j += 1
            if j == len(needle):
                return i - len(needle) + 1
        return -1
```

```python 减一操作
class Solution:
    def getNext(self, next, s):
        j = -1
        next[0] = j
        for i in range(1, len(s)):
            while j >= 0 and s[i] != s[j+1]:
                j = next[j]
            if s[i] == s[j+1]:
                j += 1
            next[i] = j
    
    def strStr(self, haystack: str, needle: str) -> int:
        if not needle:
            return 0
        next = [0] * len(needle)
        self.getNext(next, needle)
        j = -1
        for i in range(len(haystack)):
            while j >= 0 and haystack[i] != needle[j+1]:
                j = next[j]
            if haystack[i] == needle[j+1]:
                j += 1
            if j == len(needle) - 1:
                return i - len(needle) + 1
        return -1
```

## KMP 算法 #28

思路：
- 查找字串是否存在的循环步骤和求next数组的步骤是一样的

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        # prefix table
        next = [0] * len(needle)
        j = 0
        for i in range(1,len(needle)):
            # if needle[i],[j] is not same
            while j > 0 and needle[i] != needle[j]:
                j = next[j-1]
            # if same
            if needle[i] == needle[j]:
                j += 1
            next[i] = j
        # print(next)

        k = 0
        # apply next array
        for i in range(len(haystack)):
            
            while k > 0 and haystack[i] != needle[k]:
                k = next[k-1]
            if haystack[i] == needle[k]:
                k += 1
            if k == len(needle):
                return i - k + 1
            
        
        return -1
```

## KMP算法 #459

这道题是要找是否有重复的字串，思路：
- 在由重复子串组成的字符串中，最长相等前后缀不包含的子串就是最小重复子串
- 用KMP算法，求出next数组，拿到的next最后一位就是最长相等前后缀的长度
- 用字符串的长度对next数组的最后一位做除法，如果能整除就是存在，否则就是不存在

```python
class Solution:
    def repeatedSubstringPattern(self, s: str) -> bool:
        # KMP算法
        # next array
        next = [0] * len(s)
        j = 0
        for i in range(1,len(s)):
            while j > 0 and s[i] != s[j]:
                j = next[j-1]
            if s[i] == s[j]:
                j += 1
            next[i] = j
        # print(next)
        l = len(s) - next[len(s)-1]
        return len(s) % l == 0 and l != len(s)
```