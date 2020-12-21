---
title: Algorithm practice - Treasure Island 1
excerpt: Level up my Coding Skill
toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview]
---

Description
-------

You have a map that marks the location of a treasure island. 

Some of the map area has jagged rocks and dangerous reefs. Other areas are safe to sail in.

There are other explorers trying to find the treasure. So you must figure out a shortest route to the treasure island.

Assume the map area is a two dimensional grid, represented by a matrix of characters.

You must start from the top-left corner of the map and can move one block up, down, left or right at a time.

The treasure island is marked as ‘X’ in a block of the matrix. ‘X’ will not be at the top-left corner.

Any block with dangerous rocks or reefs will be marked as ‘D’. You must not enter dangerous blocks. You cannot leave the map area.

Other areas ‘O’ are safe to sail in. The top-left corner is always safe.

Output the minimum number of steps to get to the treasure.

```
e.g.
Input
[
[‘O’, ‘O’, ‘O’, ‘O’],
[‘D’, ‘O’, ‘D’, ‘O’],
[‘O’, ‘O’, ‘O’, ‘O’],
[‘X’, ‘D’, ‘D’, ‘O’],
]
```

```
Route is (0, 0), (0, 1), (1, 1), (2, 1), (2, 0), (3, 0) 
The minimum route takes 5 steps.
```

Solution
-----------

```Python
import sys


def solution(treasure_map):
    # Start from left corner
    start_x = len(treasure_map)
    start_y = len(treasure_map[0])
    treasure_x = 0
    treasure_y = 0
    distance_map = []
    for row in treasure_map:
        distance_row = []
        for col in row:
            if col == 'O':
                distance_row.append(1)
            elif col == 'D':
                distance_row.append(1000000)  # Big enough
            elif col == 'X':
                distance_row.append(1)
        distance_map.append(distance_row)
    distance_map[0][0] = 0
    '''
    for all position:
    cursor = current_position
    if cursor is D:
    pass
    cur_distance_from_upper_left = min(from_left_up, from_up, from_right_up, from_left)
    return X 
    '''
    for x in range(start_x):
        for y in range(start_y):
            cursor = treasure_map[x][y]
            if cursor == 'D':
                continue
            elif cursor == 'X':
                treasure_x = x
                treasure_y = y
            else:
                candidate = []
                if x != 0:  # up pass
                    candidate.append(distance_map[x - 1][y])  # from_up
                if y != 0:
                    candidate.append(distance_map[x][y - 1])  # from_left
            if len(candidate) != 0 and candidate != 'D':
                distance_map[x][y] = min(candidate) + distance_map[x][y]
    return distance_map[treasure_x][treasure_y]


if __name__ == "__main__":
    input_map = []
    while True:
        l = sys.stdin.readline().strip()
        if l == ']':
            break
        elif l == '[':
            continue
        li = l.split(',')
        input_map.append(list(
            filter(None, [str(x.replace('[', '').replace(']', '').replace("'", "").strip()) for x in li])))
    print(input_map)
    print(solution(input_map))

'''
[
['O', 'O', 'O', 'O'],
['D', 'O', 'D', 'O'],
['O', 'O', 'O', 'O'],
['X', 'D', 'D', 'O'],
]
'''


```

Short Description
----------

- Approach
  - I took some time to parse input and strip for handy data.
  - I need to shortest path for 'X' sign.
  - When the loop found solution, we need to keep at moment to find whether there is another solution more closer to duration.
  - If all loop are traversed, the final list is solution.