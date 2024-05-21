---
title: 202405013-linked-list
date: 2024-05-14 04:38:30
tags:
---


## 203 remove linked list element

思路：
- 如果这个元素不是要删的元素，next等于这个元素，并且移动指针到这个元素
- 如果这个元素是要删的元素，next等于这个元素的下一个元素，不移动指针，也就是next会一直变，直到出现第一种情况才移动指针

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeElements(self, head: Optional[ListNode], val: int) -> Optional[ListNode]:
        # 如果出现一直是要删除的元素，怎么办，不是马上指向下一个，而是用双指针
        # 如果头节点就是要删除的元素呢？创建一个dummy node，然后把头节点当成是第二个节点删除
        dummy_node = ListNode(-1)
        pointer = dummy_node
        while head:
            if head.val is not val:
                # 不是要删除的节点，指向这个节点
                pointer.next = head
                pointer = pointer.next
            else:
                pointer.next = head.next
            head = head.next
        return dummy_node.next
```

## 206 reverse linked list

思路：
- 很简单，就是双指针，把每个元素的指针反转
```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        pre = None
        current = head
        while current:

            next_node = current.next
            current.next = pre
            pre = current
            current = next_node

        return pre
```

## 24 Swap node in pairs

思路：
- 做链表的时候，如果想处理当前节点的指向，一定要从前一个节点开始操作，也就是前一个节点的指向变成当前节点的下一个节点，那么当前节点就可以空出来了，对吧？
- 重点：如果要把两个节点相交换，那么开始的位置一定是前一个节点，也就是每次循环的时候，指针都指向的是要交换的两个节点的前一个节点
- 关键点：因为交换之后会改变指针方向，那么把之前的节点需要保存下来，保存哪个节点？好好想想。
- 细节问题：就是循环的终止条件是什么？我创建了一个dummy节点之后
    - 终止条件一定是当前节点的后两个要交换的两个节点不为空，那么就分奇数还是偶数，奇数的话，就是要交换的节点的第二个节点为空，那么就终止，偶数的话，要交换的第一个节点就为空，也终止
    - 另外一个问题就是如果本身head就是空呢？因为我们有了头节点，所以dummy的next就是head，也符合终止条件的判断，那么就是不用在前面去判断head是不是空了。太神了

画图如下：
![swap nodes steps](./202405013-linked-list/swap%20nodes.jpg)

了解了思路，代码一气呵成
```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:

        dummy = ListNode(-1)
        dummy.next = head
        cur = dummy
        while cur.next and cur.next.next:
            node1 = cur.next
            node2 = node1.next.next
            cur.next = node1.next
            cur.next.next = node1
            node1.next = node2
            cur = cur.next.next

        return dummy.next
```

## 19 Remove Nth node from end of list

思路：
- 双指针的经典应用，如果要删除倒数第n个节点，让fast移动n步，然后让fast和slow同时移动，直到fast指向链表末尾。删掉slow所指向的节点就可以了。
- 这道题也是需要一个dummy node放在head之前，那么就可以处理只有一个，节点和两个节点的情况了。
    - 例如只有一个节点，n=1，cur1到node的时候，cur2还在dummy，cur2.next = cur2.next.next就可以用了
- 细节：另外这道题也考察，我是吧cur2移动n还是n+1

```python
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        dummy = ListNode(-1)
        dummy.next = head
        cur1 = dummy # 指针用来做n的计数的
        cur2 = dummy

        for _ in range(n): #如果这里是n+1
            cur1 = cur1.next
        
        while cur1 and cur1.next: #如果上面是n+1，这里只需要判断cur1不是空就好
            cur2 = cur2.next
            cur1 = cur1.next
        cur2.next = cur2.next.next
        return dummy.next
```