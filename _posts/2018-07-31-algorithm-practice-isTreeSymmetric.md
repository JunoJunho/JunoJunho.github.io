---
layout: post
title: Is Tree Symmetric
subtitle: Solution for IsTreeSymmetric
gh-repo: junojunho/JunoJunho.github.io
gh-badge: [star, fork, follow]
tags: [Algorithm]
---

## Problem name: isTreeSymmetric

Problem description is [here](https://app.codesignal.com/interview-practice/task/tXN6wQsTknDT6bNrf/solutions).

```python
#
# Definition for binary tree:
# class Tree(object):
#   def __init__(self, x):
#     self.value = x
#     self.left = None
#     self.right = None
def isTreeSymmetric(t):
    if t is None or (t.left is None and t.right is None):
        return True
    
    if t.left is None and t.right is not None:
        return False
    if t.left is not None and t.right is None:
        return False
    
    left_cursor = t.left
    right_cursor = t.right
    return isEqual(left_cursor, right_cursor)
    
def isEqual(left, right):
    # value 
    # shape
    if left is None or right is None:
        return left is None and right is None
    return left.value == right.value and isEqual(left.left, right.right) and isEqual(left.right, right.left) 
```

## Time Complexity

```
O(N) # N is the length of BinaryTree
```

## Solving Strategy

The symmetric condition means that left subtree of root node is the mirror image of the right subtree of root node.

Then, we can recursively decide this tree is symmetric using two conditions, left node of left subtree is same as right node of right subtree, and right node of left subtree is same as left node of right subtree. If one node is empty, which is None, then the mirror image of the node is also empty. Furthermore, the value of two nodes also need to same.

Since all condition is concatenate using `and` operator, one condition is `false`, final result is `false`, which means that the tree is not symmetric.