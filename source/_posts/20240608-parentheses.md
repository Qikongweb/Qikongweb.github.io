---
title: 20240608_parentheses
date: 2024-06-07 14:22:55
tags:
---

## 判断有效括号串 #20

思路：
- 如果只有一种括号的话，可以统计左括号出现的次数，遇到左括号加1，遇到右括号减1，但是对于有多种括号的情况，就不能只统计次数了，例如“（【）】”虽然次数相等，显然是无效的
- 所以这用的是栈，有顺序的，左括号推入，右括号，找最近的推入是不是匹配

```python
class Solution:
    def isValid(self, s: str) -> bool:
        d = {'(':')','{':'}','[':']'}
        q = []
        for ele in s:
            if ele in d:
                q.append(d[ele])
            elif not q or q[-1] != ele:
                return False
            else:
               
                t = q.pop()
                
        return len(q) == 0
```

## 平衡括号串 一 #921

思路：
- 我用的是栈，只在右括号出现时统计，统计的变量只有一个，左括号出现时只是往里面push，右括号出现时有几种情况
    1. 非空栈，最后一个不是），那么需求加1，并pop出来，接着往前再找，用while，直到找到栈里也是）
    2. 空栈，需要次数加1
    3. 非空栈，并且最后一个是），pop出来最后一个元素
- 第1种情况实际上是找左括的需求次数，2，3种情况，是第一种情况的延续，第一种情况处理完之后的记过就是要不就是空栈，要不就是最后一个等于），那么就是可以执行下面两种操作了
- 题解里的解法是单纯的统计次数，对应左括号需要多少次数，对应右括号需要多少次数。因为这个题是只有单一的括号，所以不用考虑括号的顺序，比较】）哪个在前，只统计次数就比较简单

```python
class Solution:
    def minAddToMakeValid(self, s: str) -> int:

        need = 0
        stack = []

        for i in s:
            if i == '(':
                stack.append(')')
            else:
                while stack and stack[-1] != i:
                    need += 1
                    stack.pop()
                if not stack:
                    need += 1
                    continue
                else:
                    stack.pop()
        
        return need + len(stack)


        # 第二种解法：
        left_need = 0
        right_need = 0

        for i in s:
            if i == "(":
                right_need += 1
            else:
                right_need -= 1
                if right_need == -1:
                    left_need += 1
                    right_need = 0
                
        return left_need + right_need
```

## 平衡括号串 二 #1541

思路：
- 这道题因为也是只有一种括号，用了统计次数的方法，从上一个题继承来的
- 有点困难，不太理解，判断need是否能被2整除,这里是一个难点
- 例如"(()))()))"，在遍历这一组括号的时候，如果没有判断need%2的话，只统计次数，那么恰好有3个左括号和6个右括号，就不会用补充括号，但是，这道题是需要补充3个括号，为什么呢？因为合法性，也就是())这个顺序是要保证的，所以不能只在右括号上做统计。
- need%2是如何生效的呢？上面的例子，当遍历到第三个左括号的时候，就出现need%2==1，说明对右括号的需求为奇数，如何把对右括号的需要变成偶数，所以这时res加1，意思是插入一个右括号，然后对need（右括号的需要）减1。保证对右括号的需求必须是偶数。
- 我有一点理解了，如果need%2==1的时候，说明在前面就对右括号有需求，这里已经遍历到了（，那么如果不把前面的右括号补齐的话，前面就会存在不合法的括号，need在后面会随时变化，被减去，那么如果后面的右括号又有剩余的正好把前面的不合法的右括号补齐了，统计的数就不准确了。所以要在出现（的时候，把前面的所有括号都补齐，一把一结账，哈哈。

```python
class Solution:
    def minInsertions(self, s: str) -> int:

        res = 0  # 记录插入的次数
        need = 0 # 右括号的需求量
        for i in s:
            if i == '(':
                need += 2
                if need % 2 == 1: # 难点：
                    res += 1
                    need -= 1
            else:
                need -= 1
                if need == -1: # 加一个左括号，要加两个右括号
                    res += 1
                    need = 1
               
        return res + need
```

## 32 最长有效括号

思路：
- 之前的括号的题，都是把各种不同的左括号推入，但是这道题求的是长度，所以不是把左括号推入，是把index推入
- 如果遇到右括号的时候，只要stack不为空，就pop出来，出来的就是index，因为stack里面一定就是左括号的index
- 而如果是空的话，就是没有配对的左括号
- 对于这道题有两种解法，一种是dp，一种是非dp，但是很简便的一个解法，很有意思

模版
```python
stack = []
for i in range(len(s)):
    if s[i] == '(':
        # 遇到左括号，记录索引
        stack.push(i)
    else:
        # 遇到右括号
        if stack:
            # 配对的左括号对应的索引，[leftindex,i]是一个合法的括号字串
            leftIndex = stack.pop()
            # 这个合法括号字串的长度，这里的长度只是统计一对合法的长度，如果多个合法括号字串连在一起，会形成一个更长的合法括号字串，但是这里是没办法统计的
            l = 1 + i - leftIndex
        else:
            # 没有配对的左括号

```

对于dp的解法，上面的是我所知道到到i为止的有效括号的长度，如果是我知道前一个位置leftIndex的有效括号长度，是不是就是可以和当前的长度加起来，这就是dp的思路
- dp定义：以结果为导向，dp表示以s[i-1]结尾的最长合法括号字串的长度
- 初始化dp[0] = 0，表示以i=1为结尾的最长合法括号字串的长度
- 递归公式 dp[i] = i - leftIndex + dp[leftIndex] + 1

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        # dp
        dp = [0] * (len(s)+1 )# 记录以s[i-1]为结尾的最长合法括号字串长度
        dp = [0] * len(s) # 如果是这种定义的话
        # 这里为什么是i-1？而不是i
        stack = []
        res = 0

        for i in range(len(s)):

            if s[i] == '(':
                stack.append(i)
            else:
                # 遇到右括号
                if stack:
                    leftIndex = stack.pop()
                    # 原因是这里的长度加上的是leftIndex前一个为结尾的最长合法括号字串的长度，
                    l = i - leftIndex + 1 + dp[leftIndex]
                    l = i - leftIndex + 1 + dp[leftIndex-1]
                    res = max(l,res)
                    dp[i+1] = l
                    dp[i] = l
        return res
```

这里的非dp解法特别巧妙

```python
        res = 0
        stack = [-1]

        for i in range(len(s)):
            if s[i] == '(':
                stack.append(i) # 推入此时的index
            else:
                stack.pop()  #这里为什么先pop出来？因为留下来的才是前一个非法括号的index，为了pop合理，stack初始化的时候就是里面加一个-1
                if not stack: # if popped -1, add a new start index，或者【】也是属于这里的
                    stack.append(i)
                else:
                    res = max(res, i-stack[-1]) # update the length of the valid substring
        return res
```

