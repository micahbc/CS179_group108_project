# CS179_group108_project

## Get Started ##
* Import requirements from txt file
  * Run ```pip install -r requirements.txt``` in your terminal
* Run ```skill_predictor.ipynb```


## Data ##
The datasets (`train.csv`) and (`valid.csv`) contain historical StarCraft 2 match results. Each row
represents a single match between two players with the following columns:

| Column   | Description                                              |
|----------|----------------------------------------------------------|
| date     | Date of the match (MM/DD/YYYY)                           |
| player1  | Name of the first player                                 |
| result1  | Match result for player 1 ([winner] or [loser])          |
| score    | Game score (e.g. 2–1)                                    |
| player2  | Name of the second player                                |
| result2  | Match result for player 2 ([winner] or [loser])          |
| race1    | Race of player 1 (P=Protoss, T=Terran, Z=Zerg, R=Random) |
| race2    | Race of player 2 (P=Protoss, T=Terran, Z=Zerg, R=Random) |
| expansion| SC2 expansion the match was played on (WoL/HotS/LotV)    |
| venue    | Whether the match was played online or offline           |

Note: Some matches may have both players listed as [winner] in the case of a draw.
Scores use an en-dash (–) rather than a hyphen (-).

## Model ##
We model each player's skill as a latent Gaussian variable:

    s_i ~ N(μ_i, σ_i²)

The probability that player `i` beats player `j` is given by:

    P(i beats j) = sigmoid(μ_i - μ_j)

The posterior for each player is updated online after each observed match
using a Laplace approximation. μ is updated via gradient ascent on the
log-likelihood, and σ is updated using the observed Fisher information.
This means a player's uncertainty shrinks as more matches are observed.

The model is fit by making multiple passes over the training data until
the mean change in μ falls below a convergence threshold.

To predict a match's result, P(i beats j) is computed directly from the posterior
means, with values above 0.5 predicting a win for player i.
