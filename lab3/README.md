# Lab 3: Policy Search

The following lab develops some agents able to play Nim, with an arbitrary number of rows and an upper bound on the number of objects that can be removed in a turn (a.k.a., subtraction game).

## The game

From [Wikipedia](https://en.wikipedia.org/wiki/Nim):

Nim is a mathematical game of strategy in which two players take turns removing (or "nimming") objects from distinct heaps or piles. On each turn, a player must remove at least one object, and may remove any number of objects provided they all come from the same heap or pile. The goal of the game is to to take the last objec.

### The nim sum

From [Unicatt](https://dmf.unicatt.it/~paolini/divulgazione/mateappl/nim/nim.html):

In a game of nim, you need to be able to compute a nim sum, that characterizes the configuration of the game.

Here's how to do it:

- Express the number of objects in each nim heap as a binary number, with the only digits being 0 and 1.
- Fill out the smaller binary numbers with '0's on the left, if necessary, so that all the numbers have the same number of digits.
- Sum the binary numbers, but do not carry.
- Replace each digit in the sum with the remainder that results when the digit is divided by 2.
- This yields the nim sum.
- To win at nim, always make a move, when possible, that leaves a configuration with a nim sum of 0. If you cannot do this, your opponent has the advantage, and you have to depend on his or her committing an error in order to win.
    - Note that if the configuration you are given has a nim sum not equal to 0, there always is a move that creates a new configuration with a nim sum of 0. However, there are usually also moves that will yield configurations that give nim sums not equal to 0, and you need to avoid making these.
    - Also note that if you are given a configuration that has a nim sum of 0, there is no move that will create a configuration that also has a nim sum of 0.

In this implementation, the nim sum is calculated as the bitwise xor of the rows.

## An agent using fixed rules

### The random strategy

The hardcoded agent can use a set of fixed strategies: the first one consists in removing a random number of sticks from a random row, if the row has one or more sticks.

### The nim sum strategy

The second strategy consists in calculating the nim sum of the current board:
- if it is not zero, evaluate some random moves until a zero nim sum is found.
- if it is zero, perform a random move.

### The not so smart strategy

This strategy emulates an human (that can make errors), and consists in using the nim sum strategy with a 70% probability (by default) or the random strategy otherwise.

## The match: 

The play_nim function takes four parameters:
- `n` indicates the number of rows on the board.
- `first_strategy` indicates the method used by the first agent to make a move.
- `second_strategy` indicates the method used by the second agent to make a move.
- `who_starts` indicates who makes the first move; it is an even number (tipically 0) to indicate the first agent and an odd number (tipically 1) to indicate the second agent.

The game was played in three version:
- In the first one the second agent uses the random strategy.
- In the second one the second agent uses the nim sum strategy.
- In the third one, the second agent uses the not so smart strategy.
In all the versions, the first agent uses the nim sum strategy; moreover, each version was executed two times, alternating the first and the second agent as the first player to make a move.

### Results

| Game number | Number of rows | First agent's strategy | Second agent's strategy |   Starter    |        Winner        |
| :---------: | :------------: | :--------------------: | :---------------------: | :----------: | :------------------: |
|      1      |       5        |        Nim Sum         |         Random          | First agent  |     First agent      |
|      2      |       5        |        Nim Sum         |         Random          | Second agent |     First agent      |
|      3      |       5        |        Nim Sum         |         Nim Sum         | First agent  |     First agent      |
|      4      |       5        |        Nim Sum         |         Nim Sum         | Second agent |     Second agent     |
|      5      |       5        |        Nim Sum         |      Not So Smart       | First agent  |     First agent      |
|      6      |       5        |        Nim Sum         |      Not So Smart       | Second agent | First agent (mostly) |

When the first agent starts, the second one cannot win in any case.

It is possible that in game number 2 the second agent wins; however it is highly unlikely, because all its (random) moves should have a nim sum equal to 0.

## An agent using evolved rules
