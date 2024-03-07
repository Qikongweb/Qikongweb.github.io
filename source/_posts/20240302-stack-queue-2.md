---
title: 20240302 stack-queue-2
date: 2024-03-01 13:45:49
tags:
---


## 栈与队列

### 1. 有效的括号 #20

```python
class Solution:
    def isValid(self, s: str) -> bool:
        # 自己的解法
        d = {'{':'}','(':')','[':']'}
        q = []

        for x in s:          
            if x in d:
                q.append(d[x])
            else:
                if len(q) == 0: 
                    return False
                last = q.pop()
                if x != last:
                    return False
                
        return len(q) == 0
        # (缩减版)
        d = {'{':'}','(':')','[':']'}
        q = []

        for x in s:          
            if x in d:
                q.append(d[x])
            elif len(q) == 0 or x != q.pop():
                return False    
                
        return len(q) == 0
```

思路：
- 很简单，应用栈的原理，主要就是从尾部推入右侧的括号，再右边推出，比较是不是匹配
- 可以用数组[], 也可以用queue（）


## 2. 删除字符串中的所有相邻重复项 #1047


```python
class Solution:
    def removeDuplicates(self, s: str) -> str:
        stack = []

        for x in s:
            if len(stack) == 0 or stack[-1] != x:
                stack.append(x)
            else:
                if stack[-1] == x:
                    stack.pop()
        return "".join(stack)
```
思路：
- 很简单，很上面的一个思路

## 3. 逆波兰表达式求值 #150

```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        # 自己的解法
        stack = []
        s = ['+','-','*','/']
        operator_dict = {"+": lambda x, y: x+y,
                 "-": lambda x, y: x-y,
                 "*": lambda x, y: x*y,
                 "/": lambda x, y: int(x/y)}

        for x in tokens:
            if x not in s:
                stack.append(x)
            else:
                a = stack.pop()
                b = stack.pop()
                operator_func = operator_dict[x]
                result = operator_func(int(b),int(a))

                stack.append(result)

        return int(stack[0])

# 也可以不pop出来两个，而是只在最后一位上做运算
        stack = []
        for t in tokens:
            if t not in '/+-*':
                stack.append(int(t))
            else:
                num = stack.pop()
                if   t == '+': stack[-1]+=  num
                elif t == '-': stack[-1]-=  num
                elif t == '*': stack[-1]*=  num
                else         : stack[-1]= int(stack[-1]/num)    

        return stack[0]
```

思路：
- 太简单了，都是同一套思路，唯一不太一样的是，python如何把字符串变成运算符
- 用了字典和lambda输入，lambda是用来创建匿名函数的关键字，匿名函数是一种不需要def语句来定义的函数，通常用于简单的功能性操作。

```
ex:
    lambda arguments: expression
    add = lambda x,y : x + y
    print(add(1,2)) # 3


operator_dict = {"+": lambda x, y: x+y,
                 "-": lambda x, y: x-y,
                 "*": lambda x, y: x*y,
                 "/": lambda x, y: int(x/y)}

``` 