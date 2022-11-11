# Lab 1: Set Covering

## Applied algorithm

During the lab Breath first, Depth first and A* algorithms were used.
These implies a unique search function, initially developed by professor Squillero, then adapted to this study case:
 - The state variable contains the set of elements already processed;
 - The is_valid(...) function checks if the new picked list contains at leas one element which is not included in the current state;
 - The possible_action(...) function returns the next valid list that can be processed;
 - The result(...) function unify the current set with the new list, returning the resulting state;
 - The considered unit_cost function always returns the length of the next list.

### Breath first

The priority function returns the current length of the state.
This algorithm is able to find solutions for N < 20 in a reasonable time.

### Depth first

The priority function returns the negative of the current length of the state.
The algorithm is able to find solutions for each N.

### A*

The heuristic function returns the sum between the current length of the state "len" and the difference between N and len.
This represents the distance between the goal (which has length N) and the current state.
The algorithm is able to find solutions for each N, but it is too slow and not so efficient.

## Result comparison

For each algorithm, a pair of number of elements "Ls" (the weight) and the number of visited nodes is reported.

| N    | Breath First | Depth First | A*           |
| :--: | :----------: | :---------: | :----------: |
| 5    | 5, 32        | 5, 17       | 5, 21        |
| 10   | 10, 743      | 11, 62      | 12, 116      |
| 20   | 23, 15935    | 28, 74      | 30, 100      |
| 100  | -            | 173, 1603   | 214, 2163    |
| 500  | -            | 1304, 10778 | 1489, 14368  |
| 1000 | -            | 2893, 24238 | 3717, 255965 |

## Later modifications

After reading the reviews and following the professor's suggestions, I did a couple of modifications:
- The README file was updated.
- The path is not stored anymore, along with the parent_state. It is not useful and it just occupy memory.
- I changed the A* heuristic function from $N - len(state)$ to $int(N / (N + 1 - len(state)))$, as suggested by Simone Cosimo in his review. The results were extremely better than the previous version; however, the algorithm became unable to perform the search for $N ≥ 100$ in a reasonable time.
- As a consequence of the previous argument, I used the new heuristic for $N < 100$ and the old one for $N ≥ 100$.

The new obtained results are:

| N    | A* (old)     | A* (new)     |
| :--: | :----------: | :----------: |
| 5    | 5, 21        | 5, 32        |
| 10   | 12, 116      | 10, 743      |
| 20   | 30, 100      | 23, 15935    |
| 100  | 214, 2163    | -            |
| 500  | 1489, 14368  | -            |
| 1000 | 3717, 255965 | -            |
