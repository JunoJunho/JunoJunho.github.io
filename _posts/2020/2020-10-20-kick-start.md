---
title: 2020 Google Kick Start Round G - Kick_Start
excerpt: Google Kick Start 2020 Round G

toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview]
last_modified_at: 2020-10-20T22:09:00
---

Problem
--------

Ksenia is very fond of reading so she kicks off each day by reading a fragment from her favourite book before starting with the rest of her morning routine. A fragment is simply a substring of the text. Ksenia is somewhat superstitious and believes that her day will be lucky if the fragment she reads starts with the string KICK, then goes on with 0 or more characters, and eventually ends with the string START, even if the overall fragment makes little sense.

Given the text of the book, count the number of different lucky fragments that Ksenia can read before the book gets old and she needs to buy another one. Two fragments are considered to be different if they start or end at different positions in the text, even if the fragments read the same. Also note that different lucky fragments may overlap.

Input
--------

The first line of the input gives the number of test cases T. T lines follow, each containing a single string S consisting of upper case English letters only.

Output
--------

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the number of different lucky fragments in the text of this test case.

Limits
-------

Memory limit: 1 GB.
1 ≤ T ≤ 100.
S consists of upper-case English letters only.

Test Set 1

Time limit: 20 seconds.
1 ≤ |S| ≤ 1000.

Test Set 2
Time limit: 40 seconds.
1 ≤ |S| ≤ 105.

Solution
-----------

```Python
t = int(input())  # read a line with a single integer
for i in range(1, t + 1):
    s = input()

    num_of_luck = 0
    string_length = len(s)

    kick_index_list = []
    start_index_list = []

    for j in range(string_length - 4):
        if s[j:j + 4] == "KICK":
            kick_index_list.append(i)

        if s[j:j + 5] == "START":
            num_of_luck += len(kick_index_list)

    print("Case #{}: {}".format(i, num_of_luck))
```

Additional
----------

Please give me a comment for this solution is why wrong answer.

```Python
t = int(input())  # read a line with a single integer
for i in range(1, t + 1):
    s = input()

    num_of_luck = 0
    kick_index = 0
    start_index = 4
    string_length = len(s)

    kick_index_list = []
    start_index_list = []
    if string_length < 9:
        print("Case #{}: {}".format(i, 0))
        continue

    while kick_index != -1 or kick_index <= string_length - 4:
        kick_index = s.find("KICK", kick_index, string_length)
        if kick_index == -1:
            break
        kick_index_list.append(kick_index)
        kick_index += kick_index + len("kick")

    while start_index != -1 or start_index <= string_length:
        start_index = s.find("START", start_index, string_length)
        if start_index == -1:
            break
        start_index_list.append(start_index)
        start_index = start_index + len("START")

    for k in kick_index_list:
        for s in start_index_list:
            if k < s:
                num_of_luck += 1

    print("Case #{}: {}".format(i, num_of_luck))

```