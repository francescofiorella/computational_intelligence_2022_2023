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

The main project folder contains the original `quarto` library, provided by Andrea Calabrese, along with the `quarto.ipynb` file, that contains the implementation of the hardcoded strategies and the main genetic algorithm.<br>
The file `ga_2.ipynb` contains a previous (less successful) implementation of the GA.<br>
The `minmax_rl` folder contains a modified version of the library (`quarto_edited`) which eliminates all the loops in the game by memorizing the remaining pieces and the remaining available coordinates in the board. Also, the `minmax_rl.ipynb` file contains an implementation of the minmax strategy, and two version of a reiforcement learning strategy.

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

You can find the implementation at the end of `ga_2.ipynb` file, in which you can find a table with the results too.

## An agent using minmax
