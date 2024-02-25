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