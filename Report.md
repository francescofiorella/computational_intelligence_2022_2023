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

The priority function returns the sum between the current length of the state "len" and the difference between N and len.
This represents the distance between the goal (which has length N) and the current state.
The algorithm is able to find solutions for each N, but it is too slow and not so efficient.

### Result comparison

For each algorithm, a pair of number of elements "Ls" (the weight) and the number of nodes is reported.

| N    | Breath First | Depth First | A*           |
| :--: | :----------: | :---------: | :----------: |
| 5    | 5, 32        | 5, 17       | 5, 21        |
| 10   | 10, 743      | 11, 62      | 12, 116      |
| 20   | 23, 15935    | 28, 74      | 30, 100      |
| 100  | -            | 173, 1603   | 214, 2163    |
| 500  | -            | 1304, 10778 | 1489, 14368  |
| 1000 | -            | 2893, 24238 | 3717, 255965 |

## Reviews

Link to my reviews:
- https://github.com/FrancescoSorrentino/Computational-Intelligence---22-23/issues/2
- https://github.com/simocosimo/computational-intelligence/issues/2

# Lab 2: Set Covering with Evolution Algorithm

## The genetic choices

<b>Individual: Candidate solution</b><br>
An individual is a set of used lists. Every list is represented by an unsigned integer, formed by the juxtaposition of 0's (if that number is not in the list) and 1's (if it is in the list). Ex. [0, 2, 3] becomes 01101 = 13.

<b>Population: Set of candidate solutions</b><br>
The population is represented by a list of the processed indivisuals (represented by the unsigned integers).

<b>Fitness: Ability to solve the problem</b><br>
Calculate $(2^N - 1) - (list1 | list2 | ...)$ -> (<i>'|'</i> is a <i>'bitwise or'</i>)<br>
Then, count the number of 1's of its binary representation.
It counts the numbers that are missing in the set covering.

<b>Generations: Sequence of steps</b><br>
Random changes, Evaluation, Next generation.

<b>Cross-over</b><br>
Given two sets of numbers, take a random part of the first one, and a random part of the second one; if the resulting list is empty discard it, and merge the two initial set removing the duplicates.

<b>Mutation</b><br>
Given a set of numbers (individual), remove one of them and insert an element from the list of all numbers.

## Results

The algirithm is relative fast with $N<=1000$ (it takes ~15 sec at maximum).
Above 1000 it begins to be reall slow (almost 15 mins for $N = 5000$), and the conversion between lists and numbers (list_to_int(...)) probably slows down a lot the creation of the population.

The results are encouraging, however the algorithm requires to be run several times to get an optimal result.

| N     | Weight | Number of generations | Population size | Offspring size |
| :---: | :----: | :-------------------: | :-------------: | :-------------:|
| 5     | 5      | 45/100                | 5               | 2              |
| 10    | 12     | 100                   | 10              | 3              |
| 20    | 27     | 1000                  | 20              | 7              |
| 100   | 261    | 1000                  | 20              | 7              |
| 500   | 1796   | 1000                  | 20              | 7              |
| 1000  | 3953   | 5000                  | 30              | 10             |
| 5000  | 29808  | 10000                 | 30              | 10             |

## Reviews

Link to my reviews:
