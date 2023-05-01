---
title: Reverse Nodes in K Groups
excerpt: Solution for reverseNodesInKGroups
toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Algorithm]
---

## Problem name: isTreeSymmetric

Problem description is [here](https://app.codesignal.com/interview-practice/task/XP2Wn9pwZW6hvqH67).

```python
# Definition for singly-linked list:
# class ListNode(object):
#   def __init__(self, x):
#     self.value = x
#     self.next = None
#
def reverseNodesInKGroups(l, k):
    head = l
    dummy = ListNode(-1)
    dummy.next = head

    cur, cur_dummy = head, dummy
    length = 0

    while cur:
        next_cur = cur.next
        length = (length + 1) % k

        if length == 0:
            next_dummy = cur_dummy.next
            reverse(cur_dummy, cur.next)
            cur_dummy = next_dummy

        cur = next_cur

    return dummy.next

def reverse(begin, end):
    first = begin.next
    cur = first.next

    while cur != end:
        first.next = cur.next
        cur.next = begin.next
        begin.next = cur
        cur = first.next 
```

## Time Complexity

```
O(N + nK) # N is the length of list, n = N/K, K is group size
```

## Solving Strategy

The key how to solve this problem is to remember previous node and next node of each K groups. 

When reverse function is done, last node of the group is front, which is adjacent to just previous node of the group. First node of the group is now last, which is connected with first node of next group.