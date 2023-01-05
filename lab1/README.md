# Lab 1: Set Covering

Given a number $N$ and some lists of integers $P = (L_0, L_1, L_2, ..., L_n)$,<br>
determine, if possible, $S = (L_{s_0}, L_{s_1}, L_{s_2}, ..., L_{s_n})$<br>
such that each number between $0$ and $N-1$ appears in at least one list<br>
$∀n ∈ [0, N-1] ∃i : n ∈ L_{s_i}$<br>
and that the total numbers of elements in all $L_{s_i}$ is minimum.

## The search method

The `search(...)` method, initially developed by prof. Squillero, then adapted to this study case, is unique and called several times during the later algorithm evaluation:
 - The `state` variable contains the set of elements already processed;
 - The `is_valid(...)` function checks if the new picked list contains at leas one element which is not included in the current state;
 - The `possible_action(...)` function returns the next valid list that can be processed;
 - The `result(...)` function unify the current set with the new list, returning the resulting state;
 - The considered `unit_cost` is a lambda that always returns the length of the next list.
 - The `GOAL` is an array that contains all the number from 0 to N, and it is used in the `goal_test(...)` method to understand if the goal was reached or not.

## Applied algorithm

During the lab Breath first, Depth first and A* algorithms were used: basically they just call the `search(...)` method by keeping all the arguments unchanged, except for `priority_function` and `unit_cost`.

### Breath first

The priority function returns the current length of the state.<br>
This algorithm is able to find solutions for $N < 20$ in a reasonable time.

### Depth first

The priority function returns the negative of the current length of the state.<br>
The algorithm is able to find solutions in a short time for each N.

### A*

Initially, the heuristic function returned the sum between the current length of the state $len$ and the difference between $N$ and $len$. This represents the distance between the goal (which has length N) and the current state.<br>
The algorithm was able to find solutions for each N, but it is too slow and not so efficient.

After the peer reviews, the heuristic function was changed from `N - len(state)` to `int(N / (N + 1 - len(state)))`, as suggested by [Simone Cosimo](https://github.com/simocosimo) in his [review](https://github.com/francescofiorella/computational_intelligence_2022_2023/issues/2). The results were extremely better than the previous version; however, the algorithm became unable to perform the search for $N ≥ 100$ in a reasonable time.<br>
As a consequence, the old heuristic was kept for $N ≥ 100$, while the new one was used for $N < 100$.

## Result comparison

For each algorithm, a pair of number of elements $L_s$ (the weight) and the number of visited nodes is reported.

|   N   | Breath First | Depth First |   A* (old)   | A* (new)  |
| :---: | :----------: | :---------: | :----------: | :-------: |
|   5   |    5, 32     |    5, 17    |    5, 21     |   5, 32   |
|  10   |   10, 743    |   11, 62    |   12, 116    |  10, 743  |
|  20   |  23, 15935   |   28, 74    |   30, 100    | 23, 15935 |
|  100  |      -       |  173, 1603  |  214, 2163   |     -     |
|  500  |      -       | 1304, 10778 | 1489, 14368  |     -     |
| 1000  |      -       | 2893, 24238 | 3717, 255965 |     -     |

## Later modifications

After reading the reviews and following the professor's suggestions, I did a couple of modifications:
- The README file was updated.
- In `search(...)` the `path` of the solutions is not stored anymore, along with the `parent_state`. It is not useful and it just occupy memory.
- The A* heuristic function was changed.
