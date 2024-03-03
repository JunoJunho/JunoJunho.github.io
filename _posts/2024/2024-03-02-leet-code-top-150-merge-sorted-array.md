---
title: 88. Merge Sorted Array
excerpt: Leet Code Top Interview 150

toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview, Algorithm]
last_modified_at: 2024-03-02T23:24:00
---

Problem
--------

You are given two integer arrays nums1 and nums2, sorted in non-decreasing order, and two integers m and n, representing the number of elements in nums1 and nums2 respectively.

Merge nums1 and nums2 into a single array sorted in non-decreasing order.

The final sorted array should not be returned by the function, but instead be stored inside the array nums1. To accommodate this, nums1 has a length of m + n, where the first m elements denote the elements that should be merged, and the last n elements are set to 0 and should be ignored. nums2 has a length of n.

 

```
Example 1:

Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.

Example 2:

Input: nums1 = [1], m = 1, nums2 = [], n = 0
Output: [1]
Explanation: The arrays we are merging are [1] and [].
The result of the merge is [1].

Example 3:

Input: nums1 = [0], m = 0, nums2 = [1], n = 1
Output: [1]
Explanation: The arrays we are merging are [] and [1].
The result of the merge is [1].
Note that because m = 0, there are no elements in nums1. The 0 is only there to ensure the merge result can fit in nums1.
```
 

Constraints:

```
nums1.length == m + n
nums2.length == n
0 <= m, n <= 200
1 <= m + n <= 200
-109 <= nums1[i], nums2[j] <= 109
```

Solution
-----------

```Python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """

        """
        Approach
        1. Sort decreasing order from the end in nums1 
        2. We know each one's length
        3. Take one from nums1 and nums2 and compare, the larger number will win if they are equal, nums2 will win (to take advantage of last step, merging the list)
        4. put at the end of nums1
        """

        n1_cursor = m - 1
        n2_cursor = n - 1
        for i in range(len(nums1) - 1, -1, -1):
            # To avoid example test case 3
            if n1_cursor < 0 or n2_cursor < 0:
                break
            # i will be the position we store
            if (
                nums1[n1_cursor] < nums2[n2_cursor]
                or nums1[n1_cursor] == nums2[n2_cursor]
            ):
                nums1[i] = nums2[n2_cursor]
                n2_cursor -= 1
            else:
                nums1[i] = nums1[n1_cursor]
                n1_cursor -= 1

        # End condition
        if n2_cursor >= 0:
            for i in range(n2_cursor, -1, -1):
                nums1[i] = nums2[n2_cursor]

```
