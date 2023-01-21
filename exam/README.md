# Quarto

From [wikipedia](https://en.wikipedia.org/wiki/Quarto_(board_game)):

Quarto is a board game for two players invented by Swiss mathematician Blaise Müller. It is published and copyrighted by Gigamic.

The game is played on a 4×4 board. There are 16 unique pieces to play with, each of which is either:
- tall or short;
- red or blue (or a different pair of colors, e.g. light- or dark-stained wood);
- square or circular; and
- hollow-top or solid-top.

Players take turns choosing a piece which the other player must then place on the board. A player wins by placing a piece on the board which forms a horizontal, vertical, or diagonal row of four pieces, all of which have a common attribute (all short, all circular, etc.).

Quarto is distinctive in that there is only one set of common pieces, rather than a set for one player and a different set for the other. It is therefore an impartial game.

## The purpose and the structure

The purpose of this project is to develop some agents able to play quarto. Each agent aim to beat with a high winning ratio the random agent, which always takes a random piece, and always places a piece in a random position.

The main project folder contains the original `quarto` library, provided by Andrea Calabrese, along with the `quarto.ipynb` file, that contains the implementation of the genetic algorithm, the hardcoded, minmax, and reinforcement learning strategies.<br>
The file `quarto_failures.ipynb` contains a previous (less successful) implementation of the GA.

## An agent using fixed rules

### Some hardcoded strategies

The following hardcoded strategies try to simulate some human-like moves.<br>
They are divided in placing moves and choosing moves, that can be used in the various phases of the game.

### Placing criteria

The placing phase can be characterized by 11 different strategies:
- Place in the lowest row
- Place in the highest row
- Place in the middle row
- Place in the lowest column
- Place in the highest column
- Place in the middle column
- Place in the lowest coordinates
- Place in the highest coordinates
- Place in the middle coordinates
- Try to fill a row/column/diagonal
- Try not to fill a row/column/diagonal

### Choosing criteria

The choosing phase can be characterized by 4 different strategies:
- Choose a piece with the most uncommon properties
- Choose a piece with the most common properties
- Choose a piece with similar properties of the last chosen piece
- Choose a piece with different properties of the last chosen piece

### The hardcoded player

The agent takes as argument one of the placing criterias and one of the choosing criterias. In this way, this single class can be used to play by following all the hardcoded criterias.

### The tournament

An evaluation of all the rules was calculated after a tournament: all the combinations of the placing and choosing strategies fought against the random player. The hardcoded agent moved the first piece in half of the games and, at the end, a winner was decreed, along with a leaderboard based on the winning ratio.

### Results

After the tournament, I found that "Fill coordinates" seems to be the most successfull placing strategy, independently of the choosing strategy.

| Position |   Choose Strategy    |   Place Strategy    | Win ratio |
| :------: | :------------------: | :-----------------: | :-------: |
|    1     | Most different piece |  Fill coordinates   |   0.89    |
|    2     |  Most similar piece  |  Fill coordinates   |   0.775   |
|    3     |  Most common piece   |  Fill coordinates   |   0.77    |
|    4     | Most uncommon piece  |  Fill coordinates   |   0.755   |
|    5     | Most different piece |    Middle column    |   0.74    |
|    6     | Most different piece |    Lowest column    |   0.735   |
|    7     | Most different piece |   Highest column    |   0.73    |
|    8     | Most different piece |     Middle row      |   0.73    |
|    9     | Most uncommon piece  |   Highest column    |   0.705   |
|    10    | Most different piece |   Highest column    |   0.705   |
|    11    | Most different piece |     Lowest row      |    0.7    |
|    12    | Most uncommon piece  |    Middle column    |   0.69    |
|    13    | Most uncommon piece  |     Higher row      |   0.66    |
|    14    | Most different piece | Lowest coordinates  |   0.66    |
|    14    | Most uncommon piece  | Unfill coordinates  |   0.635   |
|    15    | Most uncommon piece  | Unfill coordinates  |   0.635   |
|    16    |  Most similar piece  |     Lowest row      |   0.635   |
|    17    | Most uncommon piece  |    Lowest column    |   0.63    |
|    18    |  Most common piece   |     Middle row      |   0.625   |
|    19    |  Most common piece   |    Lowest column    |   0.625   |
|    20    | Most different piece | Highest coordinates |   0.62    |
|    21    |  Most common piece   |    Middle column    |   0.615   |
|    22    | Most uncommon piece  |     Lowest row      |   0.615   |
|    23    | Most uncommon piece  | Lowest coordinates  |   0.61    |
|    24    | Most different piece | Middle coordinates  |   0.605   |
|    25    | Most uncommon piece  |     Middle row      |   0.585   |
|    26    |  Most similar piece  |     Middle row      |   0.58    |
|    27    |  Most similar piece  |    Middle column    |   0.58    |
|    28    |  Most similar piece  |    Lowest column    |   0.575   |
|    29    |  Most similar piece  |   Highest column    |   0.575   |
|    30    | Most uncommon piece  | Middle coordinates  |   0.565   |
|    31    |  Most similar piece  | Highest coordinates |   0.565   |
|    32    |  Most similar piece  |     Highest row     |   0.56    |
|    33    |  Most common piece   |     Highest row     |   0.555   |
|    34    |  Most common piece   |   Highest column    |   0.55    |
|    35    | Most uncommon piece  | Highest coordinates |   0.55    |
|    36    |  Most common piece   |     Lowest row      |   0.53    |
|    37    | Most different piece | Unfill coordinates  |   0.52    |
|    38    |  Most similar piece  | Lowest coordinates  |   0.51    |
|    39    |  Most common piece   | Lowest coordinates  |   0.49    |
|    40    |  Most similar piece  | Middle coordinates  |   0.47    |
|    41    |  Most similar piece  | Unfill coordinates  |   0.47    |
|    42    |  Most common piece   | Middle coordinates  |   0.45    |
|    43    |  Most common piece   | Highest coordinates |   0.44    |
|    44    |  Most common piece   | Unfill coordinates  |   0.435   |

<b>N.B.</b> Fill coordinates means that the player is trying to fill a row/column/diagonal with the highest number of pieces, before placing in any other part of the board.

## An agent using evolved rules

### Individual

An individual is formed by two lists:
- The first one contains four numbers that refer to the choosing phase.
- The second one contains four numbers that refer to the placing phase.

Each number represents the index of the strategy to be used in one of the phases of the game, knowing that the game has been divided in four phases:
- Less than a quarter of pieces placed on the board
- Less than a half of pieces placed on the board
- Less than three-quarters of pieces placed on the board
- More than three-quarters of pieces placed on the board

### Evolved strategy

It is a method that can be used both in the choosing and in the placing phase.<br>
It takes as argument one of the two lists of the individual, then generate a random number, and uses it to return an appropriate result (coordinates or piece index) based on the chosen strategy.

### The evolved player

The agent takes as argument an individual, and uses it to decide what strategy to use.

### Cross-over

Given two individual, merge them by taking a random $num$ of numbers of the first one and $NUM\_ROWS - num$ numbers of the second one, twice (for both the lists in the individuals).

### Mutation

Replace a random number of the individual with another random number, twice (for both the lists of the individuals).

### Fitness

The fitness is the winning ratio of the individual against the random agent in a fixed number of matches.<br>
The evolved player makes the first move in half of the games.

This function was too slow to be calculated with a high number of matches, so in the evolution phase the number of matches was kept low, and the fitness was a little inaccurate.

After the evolution, two more evaluations of the winning ratio were calculated on the best individual:
- The first evaluation was the fitness calculated on 10000 matches.
- The second evaluation was a tournament against all the hardcoded strategies.

### Results

After a (very slow) execution of the algorithm using different parameters, I found that the best combination (taking into account the time needed to calculate the best individual, the fitness accuracy, and the two further evaluations) was:
- 200 generations
- A population of 10 individual
- An offspring of 3 individual
- A mutation rate of 0.3
- 400 matches in the fitness function

The best individual (so far) is:
- Choose: [2, 3, 2, 3]
- Place: [6, 8, 5, 9]
- Fitness: 0.9
- Accurate fitness: 0.8637
- Tournament result: 0.7523

Note that the difference between the approximated fitness and the actual fitness is too high, for $accuracy\ in\ [100, 200]$; almost acceptable for $400$. Individuals with an approximated fitness of 0.9 resulted to be very bad in the further evaluations.

All the results are reported in the following table: the accurate fitness was reported (instead of the approximated one), and the first rows contain the most relevant parameter combinations.

| Generations | Population | Offspring | Mutation Rate | Fitness Accuracy | Fitness | Tournament Result | Evolution Time |
| :---------: | :--------: | :-------: | :-----------: | :--------------: | :-----: | :---------------: | :------------: |
|     200     |     10     |     3     |      0.3      |       400        | 0.8637  |      0.7523       |     6m 5s      |
|     100     |     30     |    10     |      0.3      |       200        | 0.8557  |      0.7301       |     5m 3s      |
|     100     |     10     |     3     |      0.3      |       100        | 0.7943  |      0.6848       |      49s       |
|     200     |     50     |    17     |      0.3      |       100        | 0.8544  |      0.7193       |     8m 52s     |
|     200     |     50     |    17     |  0.3 -> 0.1   |       100        | 0.8594  |      0.6524       |     8m 22s     |
|     200     |     50     |    17     |      0.4      |       100        | 0.8548  |      0.7304       |     8m 34s     |
|     100     |     10     |     3     |      0.3      |       1000       | 0.8109  |      0.6180       |     7m 26s     |
|     100     |     30     |    10     |      0.3      |       400        | 0.8570  |      0.7305       |    10m 20s     |
|     200     |     10     |     3     |      0.3      |       100        | 0.8459  |      0.7186       |     1m 42s     |
|     500     |     10     |     3     |      0.3      |       100        | 0.8087  |      0.7067       |     3m 59s     |
|     200     |     30     |    10     |      0.3      |       100        | 0.8605  |      0.7634       |     5m 17s     |
|     100     |     30     |    10     |      0.3      |       100        | 0.8266  |      0.7183       |     2m 34s     |

## A second version of the evolution algorithm

Before the strategy described above, I developed another strategy that revealed to be too inaccurate.

Here it is a short description of it; however, I'm not reporting the results in this file, because they are characterized by a very low winning ratio.

The individual is formed by two lists:
- The first one contains four numbers that refer to the choosing phase.
- The second one contains eleven numbers that refer to the placing phase.

Each number represents the probability that a certain strategy will be chosen.<br>
Note that we defined four choosing strategies and eleven placing strategies.

Cross-over:<br>
Given two individual, merge them by taking a random $num$ of propabilities of the first one and $NUM\_ROWS - num$ probabilities of the second one, twice (for both the lists in the individuals).

Mutation:<br>
Replace a random probability of the individual with another random probability, twice (for both the lists of the individuals).

The algorithm was slow, the fitness inaccurate, and the final results not good...

You can find the implementation in `quarto_failures.ipynb` file, in which you can find a table with the results too.

## An agent using minmax

Minmax is a simple algorithm that simulates all the possible moves and choose the best one.

The function can be called in four modes:
- Mode 0: the 1st player is placing a piece
- Mode 1: the 1st player is choosing a piece
- Mode 2: the 2nd player is placing a piece
- Mode 3: the 2bd player is choosing a piece

The 1st player is trying to minimize the reward, while the 2nd player s trying to maximize it.

The alpha-beta pruning strategy has been implemented along with a cache system, in order to decrease the number of calculations.

### The Minmax Agent

It uses exclusively the minmax strategy to compute the next move; the maximum depth is customizable.

### The Random-Minmax Agent

It performs random moves util 4 pieces are placed on the board, then uses minmax to calculate the next moves.<br>
In this way it's much faster and the maximum depth can be increased (it is customizable).

#### The evaluation

The agents are evaluated by setting up some matches against the random agent, the hardcoded agent with the best strategies from the tournament (most different piece, fill coordinates), and the evolved agent. At the end, the minmax agent was evaluated against the random-minmax agent.

## Results

The minmax strategy is very effective, it has high winning ratio against all the previous strategies. However, it is the slower strategy and a maximum depth must be implemented, adding the possibility to perform some bad moves.<br>
This is the reason why it still loses some games and it has a winning ratio of 0.0 against the hardcoded strategy (for a maximum depth less then 15): the results can be improved by incrementing the maximum depth.<br>
As a matter of fact, the random-minmax wins against the pure minmax becaouse of its higher maximum depth.<br>
Note that, with a powerful pc, the minmax will always win against the random-minmax.

The evaluation results are reported in the table below:

|  First agent  | Second Agent | Max depth | Winning ratio |
| :-----------: | :----------: | :-------: | :-----------: |
|    Minmax     |    Random    |    10     |      0.9      |
|    Minmax     |    Random    |    11     |      0.9      |
|    Minmax     |  Hardcoded   |    10     |      0.0      |
|    Minmax     |  Hardcoded   |    11     |      0.0      |
|    Minmax     |  Hardcoded   |    12     |      0.0      |
|    Minmax     |  Hardcoded   |    13     |      0.0      |
|    Minmax     |  Hardcoded   |    14     |      0.0      |
|    Minmax     |  Hardcoded   |    15     |      1.0      |
|    Minmax     |   Evolved    |    10     |      1.0      |
|    Minmax     |   Evolved    |    11     |      1.0      |
| Random-Minmax |    Random    |    12     |      1.0      |
| Random-Minmax |  Hardcoded   |    12     |      0.7      |
| Random-Minmax |   Evolved    |    12     |      0.8      |
| Random-Minmax |    Minmax    |  12 - 10  |      0.7      |
| Random-Minmax |    Minmax    |  12 - 11  |      0.6      |

## An agent using reinforcement learning

Quarto was solved both with a G-learning and a Q-learning approach; however, these to agents weren't so effective as solving quarto game with reinforcement learning would require a lot of data, computational power and time to train.

### The state

The `get_state(...)` function is an attempt to decrease the amounth of data needed to train the agent. It tries to generalize the quarto board by dividing it in four squares and sorting them. In this way, two board with the same pieces placed in different (but equivalent) positions have the same state.

### Pickle

The `Pickle` library is another attempt to improve the algorithm. It has been used to store the learning dictionaries after a fixed number of matches and later loading them, in order to resume the learning phase in an intermediate state.<br>
However, the library has not been used in the later stages of the agent development as it requires a lot of time to write and read large data from disk.

### G-Table Approach

The G-table is a dictionary with entries representing states of the environment. Each cell in the table contains a value that represents the estimated long-term reward that the agent can expect to receive starting from that state.

The agent starts with a random or arbitrary G-table and then updates the values in the table based on its experiences. The agent updates the value for the current state using the following update rule:

$G(s) = G(s) + α(r + γ*G(s'))$

where s is the current state, r is the reward received, s' is the next state, α is the learning rate, and γ is the discount factor.

This process continues for many episodes, allowing the agent to learn the optimal policy for the environment over time. The G-table provides the agent with a way to represent the learned knowledge, so the agent can use this knowledge to make decisions in the future.

In this implementation, the agent generates two G-table, one for the placing phase, and one for the choosing phase.

#### The reward

The agent gets a reward of -2 if the opponent win, 1 if I win, -1 if it's a draw, 0 otherwise.

### Q-Table Approach

A Q-table is a dictionary with entries composed by a tuple of two values: the first one represents the states of the environment and the second one represents the actions that the agent can take. Each cell in the table contains a value that represents the estimated long-term reward that the agent can expect to receive by taking a particular action in a particular state.

The agent starts with a random or arbitrary Q-table, and then updates the values in the table based on its experiences. At each step, the agent selects the action with the highest value in the Q-table for the current state, and then receives a reward or penalty based on the action it took. The agent then updates the value for that state-action pair using the following update rule:

$Q(s, a) = Q(s, a) + α(r + γ*max Q(s', a') - Q(s, a))$

where s is the current state, a is the action taken, r is the reward received, s' is the next state, a' is the action taken in next state, α is the learning rate, and γ is the discount factor.

This process continues for many episodes, allowing the agent to learn the optimal policy for the environment over time. The Q-table provides the agent with a way to represent the learned knowledge, so the agent can use this knowledge to make decisions in the future.

In this implementation, the agent generates two Q-table, one for the placing phase, and one for the choosing phase.

#### The reward

During the placing phase the agent gets a reward of 1 if the game ends, 0 otherwise; meanwhile, during the choosing phase, all the possible next moves of the opponent are calculated, and the agent gets a reward of -1 for each move that leads to a lost game, 0 otherwise.

### The learning phase

In both the G-learning and Q-learning implementations, the learning phase is characterized by 10000 matches played by the agent against the random strategy; however, this number of matches is too low to obtain a well-trained agent.

### Results

Both the G-Agent and the Q-Agent were evaluated against the random agent, and the results are reported in the following table, that reports two evaluations (two win ratio), one before the learning phase and one after it.

| RL Approach | First Evaluation | Second evaluation |
| :---------: | :--------------: | :---------------: |
|   G-Table   |      0.499       |       0.523       |
|   Q-Table   |      0.478       |       0.506       |

The agents did not learn after 10000 matches and the time and data needed to perform more training is too large.

Particularly, the G-learning approach is slower than the other one, as the state table doesn't stop to grow and the performances slow down a lot.

Below, are reported some data about 23000 matches that show how the G-tables don't stop growing, the time needed for the computation increases, and the win ratio remains stable to 0.5.

#### G length

| Train n° | G_place | G_choose |  Time  | Win ratio |
| :------: | :-----: | :------: | :----: | :-------: |
|    0     |    0    |    0     |   -    |     -     |
|    1     |  50951  |  480879  |  13s   |   0.46    |
|    2     |  99623  |  946438  |  13s   |   0.49    |
|    3     | 145333  | 1384110  |  16s   |   0.49    |
|    4     | 189238  | 1804848  |  15s   |   0.47    |
|    5     | 232551  | 2213670  |  14s   |   0.43    |
|    6     | 274858  | 2604325  |  14s   |   0.52    |
|    7     | 316579  | 2988546  |  15s   |   0.40    |
|    8     | 358542  | 3370745  |  16s   |   0.44    |
|    9     | 400620  | 3748392  |  17s   |   0.53    |
|    10    | 441668  | 4116272  |  24s   |   0.56    |
|    11    | 482988  | 4480636  |  24s   |   0.52    |
|    12    | 524043  | 4844538  |  21s   |   0.49    |
|    13    | 565350  | 5209891  |  36s   |   0.44    |
|    14    | 606903  | 5566562  |  41s   |   0.55    |
|    15    | 648557  | 5916521  |  57s   |   0.48    |
|    16    | 689831  | 6264341  | 2m 32s |   0.49    |
|    17    | 731141  | 6617331  | 1m 25s |   0.51    |
|    18    | 772615  | 6963659  | 2m 1s  |   0.52    |
|    19    | 814104  | 7314337  | 3m 31s |   0.52    |
|    20    | 855315  | 7662992  | 2m 29s |   0.53    |
|    21    | 896494  | 8014891  | 3m 17s |   0.48    |
|    22    | 937698  | 8361264  | 3m 50s |   0.52    |
|    23    | 983032  | 8746893  | 9m 26s |   0.55    |

N.B. Each train runs 1000 matches.

## The chosen policy

So far, the best policies are the Evolved one and the Minmax. The first one is a valid compromise between performance and effectiveness, while the second one has potentially the best effectiveness, although it has poor performances with a high maximum depth.

So, if we need to play Quarto without any delay, the `EvolvedPlayer` is the best option; while, without any time limit the `MinmaxPlayer` with no maximum depth limit represent the perfect choice.<br>
If a medium delay is acceptable, the `MinmaxPlayer` with a maximum depth of 15 is the best option: it takes 5m 25s (on my pc) to complete the first match against the random agent, and 1m 54s from the second one (as it stores most of the states in the cache).
