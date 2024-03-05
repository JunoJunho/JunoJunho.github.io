---
title: 28\. Remove Duplicates from Sorted Array
excerpt: Leet Code Top Interview 150

toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview, Algorithm]
last_modified_at: 2024-03-04T23:40:00
---

Leet code link can be found [here](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/?envType=study-plan-v2&envId=top-interview-150)

Problem
--------

Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same. Then return the number of unique elements in nums.

Consider the number of unique elements of nums to be k, to get accepted, you need to do the following things:

Change the array nums such that the first k elements of nums contain the unique elements in the order they were present in nums initially. The remaining elements of nums are not important as well as the size of nums.
Return k.
Custom Judge:

The judge will test your solution with the following code:

```
int[] nums = [...]; // Input array
int[] expectedNums = [...]; // The expected answer with correct length

int k = removeDuplicates(nums); // Calls your implementation

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}
```
If all assertions pass, then your solution will be accepted.

 

Example 1:
```
Input: nums = [1,1,2]
Output: 2, nums = [1,2,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

Example 2:

```
Input: nums = [0,0,1,1,1,2,2,3,3,4]
Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

Constraints:

```
1 <= nums.length <= 3 * 104
-100 <= nums[i] <= 100
nums is sorted in non-decreasing order.
```

Solution
-----------

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:

        '''
        Approach
        1. Have two cursor (1 for place hodler to swap if there is duplicate, 1 for traverse the list)
        2. If cursor faced duplicate, keep proceeding until it faces different.
        3. Since we know already it is different, we may want to swap first cursor's next position with second cursor's value
        4. There will be meaningless operations if the list has all different one, but assignment will be done at the same position and it will be constant operation and time complexity will be dominated by the length of given list
        '''

        if len(nums) == 1:
            return 1

        ptr0 = 0
        ptr1 = 1

        while ptr1 < len(nums):
            if nums[ptr0] != nums[ptr1]:
                nums[ptr0+1] = nums[ptr1]
                ptr0 += 1
            ptr1 += 1
        return ptr0 + 1
```
