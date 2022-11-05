# Lab 2

<b>Individual: Candidate solution</b><br>
Set of used lists. Every list is represented by an unsigned integer, formed by the juxtaposition 0's (if that number is not in the list) and 1's (if it is in the list). Ex. [0, 2, 3] becomes 01101 = 13

<b>Population: Set of candidate solutions</b><br>
List of the lists (represented by the unsigned integers)

<b>Fitness: Ability to solve the problem</b><br>
Calculate $(2^N - 1) - (list1 | list2 | ...)$ -> (<i>'|'</i> is a <i>'bitwise or'</i>)<br>
Then, count the number of 1's of its binary representation.

<b>Generations: Sequence of steps</b><br>
Random changes, Evaluation, Next generation

<b>Cross-over</b><br>
Given two sets of numbers merge them, removing the duplicates

<b>Mutation</b><br>
Given a set of numbers, change one element of the set with one by the list of all numbers

| N | Number of fitness function calls | Number of generations | Population size | Offspring size |
| :---: | :--: | :--: | :--: | :--:|
| 5 | 9 | 3 | 10 | 3 |
| 10 | 15 | 4 | 10 | 3 |
| 20 | 29 | 5 | 10 | 5 |
| 100 | 46 | 9 | 10 | 5 |
| 500 | 82 | 16 | 10 | 5 |
| 1000 | 116 | 23 | 10 | 5 |