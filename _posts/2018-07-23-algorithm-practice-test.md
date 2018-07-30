---
layout: post
title: Restore Binary Tree
subtitle: Solution for Restore Binary Tree
gh-repo: junojunho/JunoJunho.github.io
gh-badge: [star, fork, follow]
tags: [Algorithm]
---

## Problem name: restoreBinaryTree

Problem description is [here](https://app.codesignal.com/interview-practice/task/AaWaYxi8gjtbqgp2M).

```python
#
# Definition for binary tree:
# class Tree(object):
#   def __init__(self, x):
#     self.value = x
#     self.left = None
#     self.right = None
def restoreBinaryTree(inorder, preorder):
    if len(preorder) == 0:
        return None
    root = Tree(preorder[0])
    
    pivot = find_pivot(inorder, root)
    if pivot is None:
        return None
    
    left_subtree = inorder[:pivot]
    right_subtree = inorder[pivot+1:]
    
    if len(left_subtree) == 1:
        root.left = Tree(left_subtree[0])
    elif len(left_subtree) == 0:
        root.left = None
    else:
        root.left = restoreBinaryTree(left_subtree, preorder[1:len(left_subtree)+1])
    
    if len(right_subtree) == 1:
        root.right = Tree(right_subtree[0])
    elif len(right_subtree) == 0:
        root.right = None
    else:
        root.right = restoreBinaryTree(right_subtree, preorder[len(left_subtree)+1:])
    
    return root
    
def find_pivot(pre, root):
    for i in range(len(pre)):
        if pre[i] == root.value:
            return i

```


## Time Complexity

```
O(N) # N is the length of BinaryTree
```

## Solving Strategy

Root node is first node of preorder node because traversal order of preorder algorithm is ROOT - LEFT - RIGHT. When we find which node is root node, then we can distinguish left and right subtree from the root node. In this way, we can recursively build binary tree.