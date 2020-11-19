---
title: 2020 Kick Start Round H - Retype
excerpt: Google Kick Start 2020 Round H Problem A

toc: true
toc_sticky: true
tags: [Python, Coding Interview]
last_modified_at: 2020-11-18T22:24:00
---

Problem
--------

After spending many hours studying for programming competitions, you decided to take a rest and play video games. You are currently playing an adventure game called Quick Start.

This game has N levels, and you are currently on the K-th level. Unfortunately, you just realized that to beat the boss at the final level, you will need a special sword, which can be picked up at level S. You have already completed that level, but you forgot to pick up the sword at that level.

Now you want to pick up the sword and finish the game in the least amount of time possible, and for that you have two options:

Restart the game and complete all levels again, starting from level 1.
Move to previous levels until you reach level S, pick up the sword and complete all the remaining levels, starting from level S.
Every time you enter a level you have to exit it, either by completing it and going to the next level or by moving to a previous level or by finishing / exiting the game. Exiting any level takes 1 minute. That means, for example, that it took you L minutes to complete the first L levels.

Your task is to discover which option would result in the least amount of total time to finish the game (including the time you have already spent).

Input
--------

The first line of the input gives the number of test cases, T. T test cases follow.

The first (and only) line of each test case contains three integers N, K and S: the number of levels in the game, the current level you are in, and the level where you have to pick up the sword, respectively.

Output
--------

For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is the least amount of total time to finish the game.

Limits
-------

Time limit: 20 seconds.

Memory limit: 1 GB.

- 1 ≤ T ≤ 100.
- 1 ≤ S < K < N.

Test Set 1
N ≤ 1000.

Test Set 2
N ≤ 10^9.

Solution
-----------

```Python
t = int(input())  # read a line with a single integer
for i in range(1, t + 1):
    s = input()

    N, K, S = map(int, s.split())

    # Initial
    total_time_taken = K - 1

    # Case 1: if it is the fastest way to start from initial stage
    # total_time_taken += N + 1 (restart 1 min + N minutes for completing until N th level)

    # Case 2: if take back step is more faster than start from scratch
    # total_time_taken += (K - S) + (N - S + 1) (N-S level to complete + S level)

    # Mixed
    total_time_taken += N + 1 if (K + N - 2 * S + 1) >= (N + 1) else (K + N - 2 * S + 1)

    print("Case #{}: {}".format(i, total_time_taken))

```
