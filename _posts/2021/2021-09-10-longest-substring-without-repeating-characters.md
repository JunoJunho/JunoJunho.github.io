---
title: Longest Substring Without Repeating Characters
excerpt: Leet Code Medium #3.

toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview, Algorithm]
last_modified_at: 2021-09-10T22:04:00
---

Problem
--------

Given a string s, find the length of the longest substring without repeating characters.

Example 1:

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

Example 2:

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

Example 3:

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

Example 4:

```
Input: s = ""
Output: 0
```

Constraints:

- 0 <= s.length <= 5 * 104
- s consists of English letters, digits, symbols and spaces.


Solution
-----------

```Python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        candidate_sol = []
        candi_set = set()
        max_len = 0
        for i in range(len(s)):
            new_s = s[i:]
            if max_len > len(new_s):
                break
            for c in new_s:
                if c not in candi_set:
                    candi_set.add(c)
                else:
                    candidate_sol.append(len(candi_set))
                    candi_set.clear()
                    break
            if len(candi_set) != 0:
                candidate_sol.append(len(candi_set))
            candi_set.clear()
            max_len = max(candidate_sol) if max(candidate_sol) > max_len else max_len
        return max_len
```
