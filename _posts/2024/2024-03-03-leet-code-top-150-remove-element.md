---
title: 27\. Remove Element
excerpt: Leet Code Top Interview 150

toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview, Algorithm]
last_modified_at: 2024-03-03T22:43:00
---

Leet code link can be found [here](https://leetcode.com/problems/remove-element/description/?envType=study-plan-v2&envId=top-interview-150)

Problem
--------

Given an integer array nums and an integer val, remove all occurrences of val in nums in-place. The order of the elements may be changed. Then return the number of elements in nums which are not equal to val.

Consider the number of elements in nums which are not equal to val be k, to get accepted, you need to do the following things:

Change the array nums such that the first k elements of nums contain the elements which are not equal to val. The remaining elements of nums are not important as well as the size of nums.
Return k.

Custom Judge:

The judge will test your solution with the following code:

```
int[] nums = [...]; // Input array
int val = ...; // Value to remove
int[] expectedNums = [...]; // The expected answer with correct length.
                            // It is sorted with no values equaling val.

int k = removeElement(nums, val); // Calls your implementation

assert k == expectedNums.length;
sort(nums, 0, k); // Sort the first k elements of nums
for (int i = 0; i < actualLength; i++) {
    assert nums[i] == expectedNums[i];
}
```
If all assertions pass, then your solution will be accepted.

 
```
Example 1:

Input: nums = [3,2,2,3], val = 3
Output: 2, nums = [2,2,_,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 2.
It does not matter what you leave beyond the returned k (hence they are underscores).

Example 2:

Input: nums = [0,1,2,2,3,0,4,2], val = 2
Output: 5, nums = [0,1,4,0,3,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums containing 0, 0, 1, 3, and 4.
Note that the five elements can be returned in any order.
It does not matter what you leave beyond the returned k (hence they are underscores).
``` 

Constraints:
```
0 <= nums.length <= 100
0 <= nums[i] <= 50
0 <= val <= 100
```

Solution
-----------

```Python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        '''
        Approach
        1. Have 2 cursors (front, back)
        2. Front cursor will proceed the by 1 and back cursor will proceed backward by 1
        3. Move front by 1 and check whether it is val
        4. if it is val, swap with backward cursor and move backward cursor by 1
        5. if they are crossed, break
        6. return first cursor number

        Edge case
        1. If all values are same with val -> back cursor will keep proceeding till 0 and return
        2. Everything is different from val -> it will swap, and it will be O(N)
        '''

        f_cursor = 0
        b_cursor = len(nums) - 1
        while f_cursor <= b_cursor:
            if nums[f_cursor] == val:
                tmp = nums[f_cursor]
                nums[f_cursor] = nums[b_cursor]
                nums[b_cursor] = tmp
                b_cursor -= 1
            else:
                f_cursor += 1
        
        return f_cursor
```
