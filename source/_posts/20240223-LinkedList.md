---
title: 20240223 LinkedList
date: 2024-02-23 09:36:46
tags:
---

## 链表

### 1. 两两交换链表中的节点 #16

```python
dummy_head = ListNode(-1)
dummy_head.next = head
cur = dummy_head

while cur.next is not None and cur.next.next is not None:
    temp = cur.next
    next_temp = temp.next.next
    cur.next = cur.next.next
    cur.next.next = temp
    temp.next = next_temp
    cur = cur.next.next
return dummy_head.next
            
```
思路：这个题是一定要画图的，画完图我可以写出来。附图

### 2. 删除链表倒数第N个节点，双指针 #19

## 有虚拟头节点的方法
```python
dummy_head = ListNode(0, head)
slow = dummy_head
fast = dummy_head

for _ in range(0, n+1):
    fast = fast.next
while fast is not None:
    fast = fast.next
    slow = slow.next
slow.next = slow.next.next

return dummy_head.next
```

## 没有虚拟头节点的方法

```python
p1 = head
p2 = head

for i in range(n):
    print(i)
    p1 = p1.next

if p1 is None:
    return head.next
else:
    while p1.next:
        p1 = p1.next
        p2 = p2.next

if p2.next:
    p2.next = p2.next.next

return head
```
思路: 这个题的思路是双指针，一个指针先走到N的位置，一个指针从起点开始，这时候两个指针同时向后走，这样两个指针之间相差N，一直到第一个指针先走到None，第二个指针就是到了链表的倒数第N个节点的前一个节点，就可以删除第N个节点了
***注意***
- 要想删除第N个节点，指针一定要定位在N前面的一个节点，才能删掉第N个节点
- 两个方法，一个是有虚拟头节点，一个是没有，写法上有虚拟头节点的更容易些，没有的话判断条件更多。推荐有虚拟头节点，移动的步数就是N+1

### 3. 链表是否相交 #160

```python
p1 = headA
p2 = headB
while p1 != p2:
    if p1 is None:
        p1 = headB
    else:
        p1 = p1.next
    if p2 is None:
        p2 = headA
    else:
        p2 = p2.next


return p1 
```

思路： 把两个链表拼在一起，如果有相交点，会找到，headA+headB 和 headB+headA, 他们的长度一定是一样的，那么他们如果中间没有相交，到最后也一定会相交，都是None的时候
- 一个指针从链表1开始，另一个指针从链表2开始。当第一个指针到达链表1的末尾时，我们将其重定向到链表2的头部。同样的，当第二个指针到达链表2的末尾时，我们将其重定向到链表1的头部。最终，两个指针会相遇，这就是它们的交点。
- 如果有交点的话，就是两个节点的内存地址一样，所以后续的值一定一样，这样其实就是前面的长度可能会不一样，也就是有一个先到交点位置，有一个后到交点位置，但是如果是A+B 和B+A的时候，就是肯定同时到交点位置

### 4. 环形链表，判断有否有环，找到环的起点，快慢指针 #142

思路：
- 判断链表里是否有环，一定要用双指针，快慢指针，快指针走两步，慢指针走一步。如果在非None的时候，快慢指针相等的话，就说明有环，否则，就是一直到None了。
- 这里while的条件一定要注意的是，判断快指针和快指针的next不为空，不用判断慢指针
- 当相等的时候，把其中一个指针指到head起点，然后再同时往后移动一步，当再次相等的时候就是相交的起点。
- 要怎么证明呢？

```python
fast = head
slow = head
while fast is not None and fast.next is not None:
    fast = fast.next.next
    slow = slow.next
    if(fast == slow):
        index1 = fast
        index2 = head
        while index1 != index2:
            index1 = index1.next
            index2 = index2.next
        return index1
return None
```

思路： 程序非常简单，原理证明推理过程比较复杂
- 1. 定义一个快指针一个慢指针，每次快指针移动两步，慢指针移动一步，如果有相遇，就存在环，没有相遇就不存在环
- 2. 从相遇点找环的起点，相遇时，把快指针放在head，之后快慢指针每次移动一步，再次相交的点就是环的起点


## python 语法补充
```python
dummy_head = ListNode(0, head)

两个是一个意思

dummy_head = ListNode(0)
dimmy_head.next = head
```

## 补充证明过程：
1. 为什么有环的时候，快慢指针在环里一定会相遇？
- 首先，在环里的时候，是快指针追慢指针的一个过程，以什么速度追慢指针呢？慢指针走一步，快指针走两步，所以快指针以相对速度是1的速度在追慢指针，也可以说快指针式相对慢指针一个节点一个节点去靠近，所以一定不会有交叉错过的情况。假如快指针一次移动三个节点，有可能出现错过的情况
- 事实上，快指针走够至少一圈就肯定能追上慢指针了（因为步长为2，慢为1），所以慢指针必定没走够一圈
- 因为快指针一开始肯定在慢指针的前边啊，如果想要相遇，快指针需要跑到慢指针的“后边”去追慢指针

2. 如果找环的起点？怎么证明
<!-- 画图 -->
![环的定义变量](https://code-thinking-1253855093.file.myqcloud.com/pics/20220925103433.png)

```python
# 第一次相遇时，slow指针走过的节点数为
x + y
# fast指针走过的节点数：n是多少圈，y+z是一圈内的节点数
x + y + n*(y+z)
# 条件，快指针走的步数是慢指针的2倍，出现一个等式
2*(x+y) = x + y +n*(y+z)
x + y = n * (y + z)
x = n(y+z)-y
x = (n-1)(y+z) + z

```
- 这个公式说明什么呢？

先拿n为1的情况来举例，意味着fast指针在环形里转了一圈之后，就遇到了 slow指针了。
x = z
这就意味着，从头结点出发一个指针，从相遇节点 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点。
那么 n如果大于1是什么情况呢，就是fast指针在环形转n圈之后才遇到 slow指针。

- 假设极端情况，慢指针走了一圈了，因为快指针是它的2倍速，所以快指针一定也走了两圈了，那么中间他们一定会相遇，所以慢指针一定是在一圈内被快指针追上了

3. 为什么第一次在环中相遇，slow的 步数 是 x+y 而不是 x + 若干环的长度 + y 呢？

- 首先slow进环的时候，fast一定是先进环来了。

- 如果slow进环入口，fast也在环入口，那么把这个环展开成直线，就是如下图的样子：
![把环拉成直线](https://code-thinking-1253855093.file.myqcloud.com/pics/2021031816515727.png)

那么fast指针走到环入口3的时候，已经走了k + n 个节点，slow相应的应该走了(k + n) / 2 个节点。

因为k是小于n的（图中可以看出），所以(k + n) / 2 一定小于n。

也就是说slow一定没有走到环入口3，而fast已经到环入口3了。

这说明什么呢？

在slow开始走的那一环已经和fast相遇了