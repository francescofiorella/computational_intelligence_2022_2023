# Lab 2: Set Covering with Evolution Algorithm

Given a number $N$ and some lists of integers $P = (L_0, L_1, L_2, ..., L_n)$,<br>
determine, if possible, $S = (L_{s_0}, L_{s_1}, L_{s_2}, ..., L_{s_n})$<br>
such that each number between $0$ and $N-1$ appears in at least one list<br>
$∀n ∈ [0, N-1] ∃i : n ∈ L_{s_i}$<br>
and that the total numbers of elements in all $L_{s_i}$ is minimum.

## The genetic choices

### Individual: Candidate solution

An individual is a set of used lists. Every list is represented by an unsigned integer, formed by the juxtaposition of 0's (if that number is not in the list) and 1's (if it is in the list)<br>
Ex: for N = 5, [0, 2, 3] becomes 01101 = 13.

### Population: Set of candidate solutions

The population is represented by a list of the processed individuals (represented by the unsigned integers).

### Fitness: Ability to solve the problem

Calculate $(2^N - 1) - (list1 | list2 | ...)$ where <i>'|'</i> is a <i>'bitwise or'</i>.<br>
Then, count the number of 1's of its binary representation.
It counts the numbers that are missing in the set covering.

### Cross-over

Given two sets of numbers, take a random part of the first one, and a random part of the second one; if the resulting list is empty discard it, and merge the two initial set removing the duplicates.

### Mutation

Given a set of numbers (individual), remove one of them and insert an element from the list of all numbers.

## Evolution Algorithm

The evolution algorithm starts from the initial population and repeats for `NUM_GENERATIONS` times the following steps:
- Random changes
- Evaluation
- Next generation

It ends when a perfect solution has been found (its fitness is equal to the problem size) or after `NUM_GENERATIONS` generations.

## Results

The algorithm is relative fast with $N≤1000$ (it takes ~15 seconds at maximum).
Above 1000 it begins to be really slow (almost 15 minutes for $N = 5000$); this is probably caused by the conversion between lists and numbers (`list_to_int(...)`) that slows down a lot the creation of the population.

The results are encouraging, however the algorithm requires to be run several times to get an optimal result.

| N     | Weight | Number of generations | Population size | Offspring size |
| :---: | :----: | :-------------------: | :-------------: | :-------------:|
| 5     | 5      | 45 on 100             | 5               | 2              |
| 10    | 10     | 98 on 5000            | 50              | 17             |
| 20    | 24     | 5000                  | 50              | 17             |
| 100   | 220    | 5000                  | 50              | 17             |
| 500   | 1627   | 5000                  | 50              | 17             |
| 1000  | 3648   | 5000                  | 50              | 17             |
| 5000  | 28493  | 10000                 | 50              | 17             |

## Later modifications

After the reviews, I made some little modifications to improve the results:
- For each N, I changed the population size to higher number, and the offspring size was set to about one third of the population.
- The number of generation was changed to an higher number (5000) so that more solutions can be found and compared.
