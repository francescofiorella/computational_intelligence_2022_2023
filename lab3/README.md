# Lab 3: Policy Search

The following lab develops some agents able to play Nim, with an arbitrary number of rows and an upper bound on the number of objects that can be removed in a turn (a.k.a., subtraction game).

## The Nim game

From [Wikipedia](https://en.wikipedia.org/wiki/Nim):

Nim is a mathematical game of strategy in which two players take turns removing (or "nimming") objects from distinct heaps or piles. On each turn, a player must remove at least one object, and may remove any number of objects provided they all come from the same heap or pile. The goal of the game is to take the last object.

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

> In this implementation, the nim sum is calculated as the bitwise xor of the rows.

## An agent using fixed rules

### The random strategy

The hardcoded agent can use a set of fixed strategies: the first one consists in removing a random number of sticks from a random row, if the row has one or more sticks.

### The nim sum strategy

The second strategy consists in calculating the nim sum of the current board:
- if it is not zero, evaluate some random moves until a zero nim sum is found.
- if it is zero, perform a random move.

### The not so smart strategy

This strategy emulates an human (that can make errors), and consists in using the nim sum strategy with a 70% probability (by default) or the random strategy otherwise.

## The match

The `play_nim` function takes some parameters:
- `n` indicates the number of rows on the board.
- `first_strategy` indicates the method used by the first agent to make a move.
- `second_strategy` indicates the method used by the second agent to make a move.
- `who_starts` indicates who makes the first move; it is an even number (tipically 0) to indicate the first agent and an odd number (tipically 1) to indicate the second agent.
- `first_num_law` and `second_num_law` are two special parameters that will be used for the evolved rules.

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

### Some human-like strategies

New strategies have been defined: they are based on feasible human moves and each of them can be executed with five variations, by removing either one, half, one-third, two-third or all the sticks in the chosen row.

The strategies are:
- shortest row
- longest row
- first row
- last row
- middle row

### The tournament

During the tournament, each strategy (with their variation) is used in a fixed number of matches against the random strategy. Each match is repeated twice, alternating the starting strategy.

At the end, all the winning ratios are compared and a winner is decreed.

### Evaluation phase

The winning strategy is evaluated again: new matches are played against all the other strategies. At the end, the winning ratio is calculated; if it is high enough, then the strategy is the best possible (so far).

### Results

The next table shows the obtained results after the tournament. A Nim board with $N = 5$ was chosen, and 100 matches (repeated twice because of the different starter) were disputed for each strategy.

|   Strategy   | Stick number | Winning ratio |
| :----------: | :----------: | :-----------: |
| Shortest Row |     All      |     0.849     |
| Shortest Row |      1       |     0.395     |
| Shortest Row |     Half     |     0.35      |
| Shortest Row |     1/3      |     0.405     |
| Shortest Row |     2/3      |     0.585     |
| Longest Row  |     All      |     0.435     |
| Longest Row  |      1       |     0.49      |
| Longest Row  |     Half     |     0.505     |
| Longest Row  |     1/3      |     0.57      |
| Longest Row  |     2/3      |     0.505     |
|  First Row   |     All      |     0.67      |
|  First Row   |      1       |     0.41      |
|  First Row   |     Half     |     0.475     |
|  First Row   |     1/3      |     0.495     |
|  First Row   |     2/3      |     0.515     |
|   Last Row   |     All      |     0.53      |
|   Last Row   |      1       |     0.54      |
|   Last Row   |     Half     |     0.495     |
|   Last Row   |     1/3      |     0.475     |
|   Last Row   |     2/3      |     0.54      |
|  Middle Row  |     All      |     0.46      |
|  Middle Row  |      1       |     0.505     |
|  Middle Row  |     Half     |     0.435     |
|  Middle Row  |     1/3      |     0.425     |
|  Middle Row  |     2/3      |     0.545     |

The winning strategy is removing all the sticks from the shortest row, with a winning ratio of 0.849 (84.9% of games won). It represents the best human-like strategy.

After the evaluation phase, this strategy was confirmed as the best one with an overall winning ratio of 0.9.

## An agent using evolved rules

#### Individual
An individual is formed by a set of four genes, each gene contains the strategy to use in a certain phase of the game.

#### Gene
A gene is formed by a tuple of `(rule, law)`, previously defined in the human-like strategies.

#### Fitness
The fitness is obtained by summing the winning ratio of the individual against the random strategy and the best human strategy, using boards with 2, 3, 4, and 5 rows.

#### Cross-over
Given two individual, merge them by taking a random $num$ of genes of the first one and `NUM_ROWS - num` genes of the second one.

#### Mutation
Replace a random gene of the individual with another random gene.

### The evolved strategy
The evolved strategy exploits the four genes of the individual.<br>
As we said, each gene represents a rule:
- The first rule is used if the board contains less then a quarter of the total sticks.
- The second rule is used if the board contains between a quarter and half of the total sticks.
- the third rule is used if the board contains between half and three-quarters of the total sticks.
- the fourth rule is used if the board contains more then three-quarters of the total sticks.

### Results

After 500 generations, the algorithm found that the best strategy is composed by the following rules:
- Shortest row, by removing all the sticks from the board
- Shortest row, by removing all the sticks from the board
- Shortest row, by removing one-third of the sticks from the board
- Longest row, by removing one stick from the board

Moreover, some matches against the random and the best human-like strategies were played to further evaluate the evolved strategy: the results are reported in the table below.

|   N   | Opponent's Strategy | Win Ratio |
| :---: | :-----------------: | :-------: |
|   2   |       Random        |   0.54    |
|   3   |       Random        |   0.68    |
|   4   |       Random        |   0.82    |
|   5   |       Random        |   0.71    |
|   2   |     Best human      |    0.5    |
|   3   |     Best human      |    1.0    |
|   4   |     Best human      |    1.0    |
|   5   |     Best human      |    1.0    |

## An agent using minmax

The minmax strategy works as follow:

I am maximizing my possible reward, while my opponent is minimizing my possible reward (so maximizing his reward).

When it's my turn:
- Calculate all the possible moves.
- Calculate all the possible moves for my opponent (for each of my moves)
- Keep on calculating, until all the possible combinations end in someone winning.
  - Alpha beta pruning was included in the implementation, along with depth limitation.
- Pick the path that maximize my reward.
- Perform the chosen move.

### The evaluation

Several matches were played to evaluate the minmax strategy:
- Against the random strategy
- Against the best human-like strategy
- Against the evolved strategy
- Against nim sum

### Results

The minmax strategy was able to win against all the previous strategy; however, when playing against the nim sum, it loses if the minmax is not starting with $N \ in \ [2, 3, 5]$, and it loses when the minmax is starting with $N = 4$.

After adding the cache system, the minmax strategy became very fast, so it was able to play with $N \ in \ [2, 3, 4, 5]$ in a few milliseconds. Moreover, the depth cut was removed and results were far better.

It is not possible to win against the nim sum strategy as first player for $N \ in \ [2, 3, 5]$.

Considering $N = 4$, since the nim sum of a full board is equal to 0, it's not possible to win against the nim sum strategy as second player. On the other hand, the winning ratio against the nim sum strategy as first player is always 1.0.

The results are reported in the table below:

|   N   |    Opponent's Strategy     | Winning Ratio |
| :---: | :------------------------: | :-----------: |
|   2   |           Random           |     0.92      |
|   3   |           Random           |      1.0      |
|   4   |           Random           |      1.0      |
|   5   |           Random           |      1.0      |
|   2   |         Best human         |      1.0      |
|   3   |         Best human         |      1.0      |
|   4   |         Best human         |      1.0      |
|   5   |         Best human         |      1.0      |
|   2   |          Evolved           |      0.5      |
|   3   |          Evolved           |      1.0      |
|   4   |          Evolved           |      1.0      |
|   5   |          Evolved           |      1.0      |
|   2   | Nim Sum (as second player) |      1.0      |
|   3   | Nim Sum (as second player) |      1.0      |
|   4   | Nim Sum (as second player) |      0.0      |
|   5   | Nim Sum (as second player) |      1.0      |
|   2   | Nim Sum (as first player)  |      0.0      |
|   3   | Nim Sum (as first player)  |      0.0      |
|   4   | Nim Sum (as first player)  |      1.0      |
|   5   | Nim Sum (as first player)  |      0.0      |

## An agent using reinforcement learning

### The reward

The agents gets a reward of 1 if the move he does is the winning one (the last move of the game), 0 otherwise.

### The learning phase

During the learning phase, the agent is trained against the random strategy. 10000 matches are performed and, at the end, the agent improves significantly.

### The opponents

The reinforcement learning algorithm has been trained against:
- Random strategy
- Best human-like strategy
- Evolved strategy
- Nim sum strategy
- Minmax strategy

### Results

The learning algorithm has revealed successful; in the end, each opponent has been beaten with a high ratio.<br>
Data has been collected before and after the training, during two evaluations which showed the great improvement of the agent during the learning phase.<br>
The order in which players start is meaningful, in fact the reinforcement learning agent has a winning ratio of 0.0 (against the evolved and the nim sum strategy) if he's not the one who's starting; the ratio is still high (0.7) against the random strategy.

|   N   | Opponent's strategy | First evaluation | Second evaluation |
| :---: | :-----------------: | :--------------: | :---------------: |
|   5   |       Random        |       0.43       |       0.89        |
|   5   |     Best human      |       0.16       |        1.0        |
|   5   |       Evolved       |       0.08       |        1.0        |
|   5   |       Nim Sum       |       0.0        |       0.84        |
|   5   |       Minmax        |       0.0        |        1.0        |

## The chosen policy

So far, the best policies are the Minmax and the Reinforcement Learning. The first one has the best performances (after the implementation of the cache system) and has an extremely high win ratio against the random, best human, evolved, and nim sum strategies.<br>
If we have enough time to train the agent, the RL strategy is the best option, as it is able to win against all the strategies, minmax included.

## Later modifications

After the reviews, I made some little modifications to improve the results:
- A wrapper function has been written to evaluate each strategy.
- The previous implementation of the evolved strategy was modified and adapted as a tournament, and now it is included in the first task.
- A new evolved strategy was implemented from scratch.
- The alpha beta pruning in the minmax strategy was modified and improved.
- A cache system was implemented in minmax that increased a lot the performances.
- A new set of matches were played to further evaluate all the strategies.
