# League of Legends Match Outcome Predictor

## Introduction

Aside from the mainstream success of the animated series Arcane, League of Legends (LoL) has stood as a prominent video game in its own right. Two teams of five players against each other in a strategic contest to destroy the opposing team's Nexus. While teams and players control most in-game decisions, one uncontrollable factor is the side of the map teams are assigned at the start of a match.  Teams play on either the "red" or "blue" side, a distinction that significantly impacts strategy through differences in draft pick order and map layout. These differences shape gameplay and, in professional matches where every advantage matters, potentially decide outcomes. 

As such, this project aims to answer this question: is there a competitive edge on starting either red or blue side? Understanding the impact of map-side asymettry offers viewers deeper insights into this factor influences game outcomes at the highest levels of play, helping them refine their expectations and analyses. To address this, the project utilizes a dataset from Oracle's Elixir, built with 2024 professional match data. It provides a relevant source of data as it captures detailed gameplay statistics in thousands of pro-play games, such as individual player performance, team performance and time-specific in-game metrics.

The dataset has 115152 rows, with every 12 rows representing a match between two teams -- each team has 6 rows of data, 1 for each player and 1 for overall team summary. The relevant columns that will be used for all of this project are as follows:

`gameid`: unique identifier for each game.

`side`: either 'Red' or 'Blue', indicating where the team starts the game  

`result`: outcome of a match; '0' indicates defeat, '1' indicates victory

`golddiffat20`: difference in total gold earned between teams at the 20-minute mark; indicative of strength in terms of economy

`xpdiffat20`: difference in experience points earned between teams at the 20-minute mark; indicative of strength in terms of levels

`csdiffat20`: difference in creep score (CS) between teams at the 20-minute mark; contributes to overall gold and XP advantage

`killsat20`: number of kills by the team at the 20-minute mark

`assistsat20`: number of assists by team at the 20-minute mark

`deathsat20`: number of deaths by team at the 20-minute mark

`opp_killsat20`: opposing team's counterpart of killsat20

`opp_assistats20`: opposing team's counterpart of assistsat20

`opp_deathsat20`: opposing team's counterpart of deathsat20

`datacompleteness`: either 'partial' or 'complete'; 'complete' rows contain these 20-minute mark summary statistics


## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
To prepare the dataset for analysis, the following data preprocessing steps were implemented:

#### Column Selection and Transformation:
> Only the relevant columns mentioned above were retained for the analysis. The columns `killsat20`, `assistsat20`, `deathsat20`, and their corresponding opponent counterparts were excluded. Instead, these variables were transformed into new columns: `killsdiffat20`, `assistsdiffat20`, and `deathsdiffat20`.

#### Filtering Team-Level Data:
>  Each game consists of 12 rows, including 10 rows representing individual player statistics and 2 rows for overall team statistics (one row per team). To focus on team-level performance, only the two team-level rows per game were retained, while individual player statistics were filtered out.

#### Ensuring Game Completion:
> Games were verified to be complete by checking that each gameid was associated with exactly two rows: one row where the result column equals 0 and the other where the result column equals 1. Games that did not meet this criterion were excluded.

#### Filtering Based on Team Representation:
> To ensure meaningful analysis on a team-by-team basis, only teams that had participated in at least five games were retained in the dataset. Teams with fewer than five games were removed.

#### Creating a Subset for Complete Data:
> A final subset of the dataset was created where datacompleteness was marked as "complete." This subset will be used for subsequent analyses. As some games did not reach the 20-minute mark, these games were dropped to maintain consistency in the analysis of 20-minute metrics.

Below is the head of the cleaned dataset, including the last step as well.

| gameid           | teamname            | side   |   result | datacompleteness   |   golddiffat20 |   xpdiffat20 |   csdiffat20 |   killsdiffat20 |   assistsdiffat20 |   deathsdiffat20 |
|:-----------------|:--------------------|:-------|---------:|:-------------------|---------------:|-------------:|-------------:|----------------:|------------------:|-----------------:|
| LOLTMNT99_132542 | BoostGate Esports   | Blue   |        1 | complete           |           4248 |         2138 |           50 |               5 |                11 |               -5 |
| LOLTMNT99_132542 | Dark Passage        | Red    |        0 | complete           |          -4248 |        -2138 |          -50 |              -5 |               -11 |                5 |
| LOLTMNT99_135042 | Misa Esports        | Blue   |        0 | complete           |           1760 |         1394 |           66 |              -2 |                -4 |                2 |
| LOLTMNT99_135042 | FUT Esports         | Red    |        1 | complete           |          -1760 |        -1394 |          -66 |               2 |                 4 |               -2 |
| LOLTMNT99_138196 | NASR eSports Turkey | Blue   |        0 | complete           |           1969 |         -738 |          -16 |               4 |                 4 |               -4 |

### Univariate Analysis
<iframe
  src="assets/uni_red.html"
  width="600"
  height="400"
  frameborder="0"
></iframe> The histogram displays the distribution of win rates for teams playing on the red side. While the frequency of win rates peaks around 50%, an interesting secondary peak emerges within the 35-45% win rate range, suggesting that starting on the red side may present a disadvantage.

<iframe
  src="assets/uni_blue.html"
  width="600"
  height="400"
  frameborder="0"
></iframe> The histogram displays the distribution of win rates for teams playing on the blue side. The frequency of win rates peaks around 50%, but there is a noticeable increase in frequencies within the 55-65% win rate range, especially when compared against red side's distribution. This pattern suggests that starting on the blue side may offer a slight advantage.

### Bivariate Analysis
<iframe
  src="assets/bi_violin.html"
  width="600"
  height="400"
  frameborder="0"
></iframe> The violin plot displays the distribution of team win rates for blue and red sides in a more comparable fashion. The blue side demonstrates a slightly higher median and a broader distribution above 50%, while the red side presents a more symmetrical spread centered around the 50% mark; this trend again suggests that blue side may have the competitve edge.

### Interesting Aggregates

| Side   |   min_value |   max_value |   percentile_25 |   mean_value |   median_value |   percentile_75 |
|:-------|------------:|------------:|----------------:|-------------:|---------------:|----------------:|
| Blue   |           0 |           1 |          0.3636 |       0.4822 |         0.5000 |          0.6250 |
| Red    |           0 |           1 |          0.3267 |      0.42857 |         0.4340 |          0.5556 |

The table highlights that the blue side has consistently higher values for the 25th percentile, mean, median, and 75th percentile in terms of win rate. This consistent trend across multiple statistical measures reinforces the idea that teams starting on the blue side tend to perform better overall.

### Imputation
No missing values were filled in. There were only two scenarios for missing values: (1) missing 20-minute data when the `datacompleteness` was partial, and (2) missing 20-minute data when games ended before 20 minutes. In the first scenario, attempting to fill in missing values using statistics from other games would create frameworks that oversimplify the unique and unpredictable dynamics of each game. In second scenario, imputation would not be effective as the data did not occur in the first place, i.e., it was missing not at random. As such, dropping these rows were more appropriate than imputation. 

## Framing a Prediction Problem
With evidence pointing to map side influencing game outcomes, we can explore a prediction problem: can we accurately predict a game’s result based on the starting side, alongside other in-game statistical metrics? Specifically, we aim to predict a game's outcome at the 20-minute mark, using the team's starting side and their accumulated in-game statistics up to that point.

The classifier being built performs binary classification, with the response variable being `outcome`, as that determines if a team won the match. Accuracy was chosen as the evaluation metric because it provides a clear measure of the model's performance and is suitable for this dataset, which has balanced classes with one win and one loss per game. Since there is no specific emphasis on minimizing false positives or false negatives, metrics such as AUROC or F1-score are not prioritized. 

## Baseline Model
The baseline model was trained using logistic regression using the following features: `side`, `killsdiffat20`,`deathsdiffat20`, `assistsdiffat20`. `side` is a nominal feature, for which one hot encoding was performed upon it. The other features were quantative, and were left as is. The dataset was split 70:30 and training and testing.

After fitting the model, the baseline model achieved an accuracy of **0.7356** on the test set. While this result is a reasonable starting point, the model has significant room for improvement. One limitation of the model is the reliance on kills, assists, and deaths as primary predictors. Although these statistics capture direct combat outcomes, they are not always the strongest indicators of match success -- match outcomes are often influenced by broader factors, such as gold differences and objective control. Additionally, the lack of scaling for quantative features may have led to suboptimal performance.

In the next section, we will refine this model by expanding the feature set to include more strategic metrics, as well as applying appropriate scaling to the quantitative features.

## Final Model
The final model adds three new quantative features: `xpdiffat20`, `golddiffat20`, and `csdiffat20`. These features provide a more comprehensive view of a team's mid-game state, as they more closely capture resource-based advantages critical to determining match outcomes.

The final model continues to use logistic regression, and now also uses StandardScaler to scale all quantitative features. Additionally, hyperparameter tuning was conducted using GridSearchCV to identify the optimal settings. The best hyperparameters selected were C=10, penalty='l2', and solver='lbfgs'.

These enhancements provide the model with a more comprehensive understanding of the data, leading to better predictive accuracy and a stronger alignment with the underlying mechanics of match outcomes compared to the baseline model. This is reflected in the final model's accuracy of **0.7818** on the test set, demonstrating a clear improvement over the baseline model.