---
title: 169\. Major Element
excerpt: Leet Code Top Interview 150

toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview, Algorithm]
last_modified_at: 2024-03-07T21:27:00
---

Leet code link can be found [here](https://leetcode.com/problems/majority-element/description/?envType=study-plan-v2&envId=top-interview-150)

Problem
--------

Given an array nums of size n, return the majority element.

The majority element is the element that appears more than ⌊n / 2⌋ times. You may assume that the majority element always exists in the array.

 
Example 1:

```
Input: nums = [3,2,3]
Output: 3
```

Example 2:
```
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```
 

Constraints:

```
n == nums.length
1 <= n <= 5 * 104
-109 <= nums[i] <= 109
```

Solution
-----------

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        # Majority means the list always faces the value of majority at middle position
        return sorted(nums)[len(nums)//2]
```
