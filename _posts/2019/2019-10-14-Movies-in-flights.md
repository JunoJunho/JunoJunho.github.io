---
title: Algorithm practice - Movies in Flights
excerpt: Level up my Coding Skill
toc: true
toc_sticky: true
categories:
  - Algorithm
tags: [Python, Coding Interview]
---

Description
-------

You are on a flight and wanna watch two movies during this flight.

You are given int[] movie_duration which includes all the movie durations.

You are also given the duration of the flight which is d in minutes.

Now, you need to pick two movies and the total duration of the two movies is less than or equal to (d - 30min).

Find the pair of movies with the longest total duration. If multiple found, return the pair with the longest movie.

```
e.g.
Input
movie_duration: [90, 85, 75, 60, 120, 150, 125]
d: 250
```

```
[90, 125]
90min + 125min = 215 is the maximum number within 220 (250min - 30min)
```

Solution
-----------

```Python
def solution(movies, duration):
    if duration <= 30:
        return []
    objective = duration - 30
    # If same one is occur, longest first, then let's find longest first
    answer_movies = []
    # List ascending order
    movies.sort(reverse=True)
    for i in range(0, len(movies) - 2):
        cur_answer_list = [movies[i]]
        for j in range(i + 1, len(movies) - 1):
            if cur_answer_list[0] + movies[j] <= objective:  # Got ya!
                cur_answer_list.append(movies[j])
                break
        if sum(answer_movies) < sum(cur_answer_list):
            answer_movies = cur_answer_list.copy()
    return answer_movies


if __name__ == "__main__":
    movie_duration = [90, 85, 75, 60, 120, 150, 125]
    d = 250
    print(solution(movie_duration, d))

```

Short Description
----------

- Approach
  - At first, I think I need to sort the given list to select largetst movie duration (since the condition is longest movie is required if same answer exists, that is why the if condition `sum(answelsr_movies) < sum(cur_answer_list)` does not contain euqal sign)
  - From the largest one, pick and traverse the list to find less than objective, which is duration - 30.
  - When the loop found solution, we need to keep at moment to find whether there is another solution more closer to duration.
  - If all loop are traversed, the final list is solution.