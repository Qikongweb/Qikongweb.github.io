---
title: 20240301 stack&queue#1
date: 2024-02-29 14:20:30
tags:
---


## 栈与队列

### 1. 用栈实现队列 #232

```python 
class MyQueue:

    def __init__(self):
        self.stack_in = []
        self.stack_out = []

    def push(self, x: int) -> None:
        self.stack_in.append(x)

    def pop(self) -> int:
        if len(self.stack_out) == 0:
            for _ in range(0,len(self.stack_in)):
                self.stack_out.append(self.stack_in.pop())
        return self.stack_out.pop()

    def peek(self) -> int:
        result = self.pop()
        self.stack_out.append(result)
        return result

    def empty(self) -> bool:
        if len(self.stack_in)== 0 and len(self.stack_out)==0:
            return True
        else:
            return False
```

思路： 
- 用栈实现列队，主要注意的是需要有两个栈，入口栈是负责加入元素进来，出口栈是负责pop出最后一个元素，从入口栈一个一个导入到出口栈。

### 2. 用队列实现栈 #225

解法一： Python 的库 queue.Queue
```python
import queue
class MyStack:
    
    def __init__(self):
        self.queue_default = queue.Queue()

    def push(self, x: int) -> None:
        self.queue_default.put(x)
        for _ in range(0, self.queue_default.qsize()-1):
            x = self.queue_default.get()
            self.queue_default.put(x)

    def pop(self) -> int:
        return self.queue_default.get()

    def top(self) -> int:
        result = self.queue_default.get()
        self.push(result)
        return result

    def empty(self) -> bool:
        return self.queue_default.qsize() == 0
  
```
解法二： Python库里面的 collection.deque

```python
class MyStack:
    
    def __init__(self):
        self.deque_ori = deque()

    def push(self, x: int) -> None:
        self.deque_ori.append(x)
        for _ in range(0, len(self.deque_ori)-1):
            x = self.deque_ori.popleft()
            self.deque_ori.append(x)

    def pop(self) -> int:
        return self.deque_ori.popleft()

    def top(self) -> int:
        result = self.deque_ori.popleft()
        self.push(result)
        
        return result
        return self.deque_ori[0]

    def empty(self) -> bool:
        return len(self.deque_ori) == 0

```

思路：
- 栈是先进后出，队列是先进先出，为了实现栈，可以只用一个队列，把size-1的元素从左面出来，再推进右面。

**python 语法：**
python里的queue.Queue VS collection.deque
- 在Python里，queue.Queue主要是为了线程间通信，作为“队列”只是附带的功能。而collections.deque就是个容器，和dict，list类似。

- 如果只是想用一个简单的队列，可能从名字上看上去“Queue”更合适。当然用是可以用的，不过，Queue相比deque有个坏处：慢不少。Queue是很高级的同步设施，有例如get_nowait，join等同步用接口，该阻塞就阻塞，该返回就返回。而deque只是个容器。其实从类名也有所反映，Queue是大写的首字母；而deque是和list, dict等一样是小写的首字母。

Queue：put和get

deque：append和popleft

**仔细讲讲deque，python里经常用到的**
- Python中的标准库collections中有一个deque，该对象与list列表相似。这里的“双向”指的是deuqe的结构使用双向链表，它提供了两端都可以操作的序列，这意味着，我们可以在序列前后都执行添加或删除。大多操作与List相同，如访问元素，求序列长度等，同样deque序列中的元素类型也不唯一。

- 相比于list实现的队列，deque实现拥有更低的时间和空间复杂度。list实现出队（pop）和插入（insert）时的空间复杂度大约为O(n)，deque在出队（pop）和入队（append）时的时间复杂度是O(1)。

使用q=deque()代替q=list()，因为q.popleft()效率比q.pop(0)高
这是因为：列表实现是基于数组的。pop(0)从列表中删除第一个项，它需要左移len(lst) - 1个项来填补空白。 deque()实现使用双向链表。因此无论deque有多大，deque.popleft()都需要一个常量的操作数。
即deque.popleft()：T(n)=O(1)，而list.pop(0)：T(n)=O(n)

- 基本操作：
    1. deque入队和出队
        - append(item)，添加一个数据到队列的尾部。与列表的append()方法功能相似。
        - appendleft(item)，添加一个数据到队列的头部。与append()的添加方向相反。

    2. 可迭代对象入队
        - append([1,2,3])，添加一个数据到队列的尾部。与列表的append()方法功能相似。
        - appendleft([1,2,3])，添加一个数据到队列的头部。与append()的添加方向相反。
    
    3. 指定位置插入数据
        - insert(index, item)，在队列中的指定位置插入一个数据，index为指定的位置索引。

    4. deque 类中实现了队列两端的出队方法
        - pop()，将队列尾部的数据弹出，并作为返回值。
        - popleft()，将队列头部的数据弹出，并作为返回值。

    5. deque的copy方法
        - copy()，拷贝队列。拷贝之后，对原队列进行操作，不会影响到拷贝出来的队列。这个方法要在高于Python3.5的版本才有。


