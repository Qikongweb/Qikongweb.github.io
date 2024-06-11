---
title: 20240605_monotonic_stack
date: 2024-06-06 09:45:54
tags:
---

## Monotonic Stack #232

思路：
- 这道题难点是如果求一个窗口内的最大值呢？不要用forloop因为时间复杂度是O(N)
- 也不能用大顶堆，因为虽然有排序，但是每次pop出来可能是中间的元素，就没办法pop出来了
- 这里我用了单调栈，加入到栈的规则就是凡是推入进来的数比前面的数大，就自动把前面的踢掉，如果小于前面的数就自动加在尾部，这样的话，前面的最大值，自从加入进来就一直是窗口里的最大值，所有比他小的元素都不可能成为最大值，所以都不需要保留，那么只要这个元素还在它就是一直是窗口的最大值，一直到它被pop出去。
- 分三步走，先pop出去一个数，条件是移动到第k位，并且等于q的最大值，然后加入进来一个，最后拿出来最大值

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:

        # Monotonic Stack
        q = deque()
        res = []
        for i in range(len(nums)):
            # pop
            if i > k - 1 and nums[i-k] == q[0]:
                q.popleft()

            # push
            while q and q[-1] < nums[i]:
                q.pop()
            q.append(nums[i])


            # get the max
            if i >= k - 1:
                # print(q)
                res.append(q[0])

        return res
```

## 347 Top k Frequent Elements

思路：
- 用的是小顶堆，python里用的是heap，heappush,heappop
- 为什么用小顶堆，是求的最大的前k个值，因为每次都是pop出去最小值，而大顶堆求的是前k个最小的值
- heap默认就是小顶堆，如果用heap如果模拟大顶堆呢？那么就是每次推元素进去的时候加一个负号，拿出来再把负号去掉

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        m = {}
        for n in nums:
            m[n] = m.get(n,0) + 1
        res = []
        # 用小顶堆，每次把最小值弹出去，只维护前k个元素
        # 把map里所有的元素都导入到小顶堆里面，python用的是heap，heap默认就是小顶堆
        for key,val in m.items():
            heapq.heappush(res,(val,key))
            if len(res) > k:
                # 如果长度大于k的时候，就要弹出栈顶元素了
                heapq.heappop(res)
        # 最后整理结果
        # print(res)
        result = [0] * k
        for i in range(k):
            result[i] = heapq.heappop(res)[1]
        return result
```

## 155 Min Stack

思路：
- 用了两个数组，记录了一个是正常的栈，一个是最小值的栈，每次推入一个数的时候就是在末尾记录了最小值，下一次推入的是和最后一个值比较再推入一个最小值，那么最小值的栈应该是相等于正常的栈的长度

```python
class MinStack:

    def __init__(self):
        self.stack = []
        self.minStack = []

    def push(self, val: int) -> None:
       
        self.stack.append(val)
        if self.minStack:
            self.minStack.append(min(val,self.minStack[-1]))
        else:
            self.minStack.append(val)

    def pop(self) -> None:
       
        self.stack.pop()
        self.minStack.pop()

    def top(self) -> int:
        return self.stack[-1]
        
    def getMin(self) -> int:
        
        return self.minStack[-1]
```

## 基于单调栈的衍生题：去重复字母 #316， #1081

题目要求：
题目的要求总结出来有三点：

- 要求一、要去重。

- 要求二、去重字符串中的字符顺序不能打乱 s 中字符出现的相对顺序。

- 要求三、在所有符合上一条要求的去重字符串中，字典序最小的作为最终结果。

思路很重要：
- 第一个想到的是单调栈的思想，用的是单调递增栈，例如“bcabc“，遇到小的字母就把前面的字母都pop出去，应用完单调递减栈之后就可以得到abc，这样就满足了1，2个要求
- 遇到一个问题就是如果是这样的例子“bcab”，应用完单调栈的结果就是ab，但是正确的结果是cab，也就是在pop出去之前要知道后面还会不会有这个字母了，如果有的话，可以pop出去，这样可以满足字母顺序，如果没有不能pop出去
- 如何知道后面有没有这个字母了呢？当然是在循环之前就对每个字母做了次数的统计，每次循环到一个字母，次数减1，这样就可以知道后面还有没有这个字母了
- 第二个细节问题，就是如果stack里面已经有相同的字母了，那还要不要pop出去呢？当然是不用了，直接跳过，因为在stack里面的字母一定是按顺序排出来了，例如，a在stack里面，那么再碰见a就可以直接跳过了。
- 那么如何判断a是不是在里面了呢？又要用到一个boolean的数组，记录，stack里面是不是有了a

我写的代码
```python
class Solution:
    def smallestSubsequence(self, s: str) -> str:
        # 单调栈
        stack = []
        boolean = [False] * 26  # 记录stack里面出现的字母
        
        m = {} # 记录每个字母出现的次数，也可以用上面的arr来表示
        for char in s:
            m[char] = m.get(char, 0) + 1
        # print(m)
        # 单调栈出来了
        for i in range(len(s)):
            
            m[s[i]] -= 1
            if boolean[ord(s[i]) - ord('a')]:
                continue
            while stack and ord(s[i]) < ord(stack[-1]) and m[stack[-1]] > 0:
                # res[stack[-1]] = s[i]
                boolean[ord(stack[-1]) - ord('a')] = False
                stack.pop()
            stack.append(s[i])  # 这里的单调栈，我没有像之前那样push进去index，而且直接加字母，因为返回的结果是字符串
            boolean[ord(s[i]) - ord('a')] = True

        
        return "".join(stack)


```

例子的代码，
```c
String removeDuplicateLetters(String s) {
    Stack<Character> stk = new Stack<>();

    // 维护一个计数器记录字符串中字符的数量
    // 因为输入为 ASCII 字符，大小 256 够用了
    int[] count = new int[256];
    for (int i = 0; i < s.length(); i++) {
        count[s.charAt(i)]++;
    }

    boolean[] inStack = new boolean[256];
    for (char c : s.toCharArray()) {
        // 每遍历过一个字符，都将对应的计数减一
        count[c]--;

        if (inStack[c]) continue;

        while (!stk.isEmpty() && stk.peek() > c) {
            // 若之后不存在栈顶元素了，则停止 pop
            if (count[stk.peek()] == 0) {
                break;
            }
            // 若之后还有，则可以 pop
            inStack[stk.pop()] = false;
        }
        stk.push(c);
        inStack[c] = true;
    }

    StringBuilder sb = new StringBuilder();
    while (!stk.empty()) {
        sb.append(stk.pop());
    }
    return sb.reverse().toString();
}
```