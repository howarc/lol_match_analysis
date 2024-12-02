# Introduction

Aside from the mainstream success of the animated series Arcane, League of Legends (LoL) has stood as a prominent video game in its own right. Two teams of five players against each other in a strategic contest to destroy the opposing team's Nexus. While teams and players control most in-game decisions, one uncontrollable factor is the side of the map teams are assigned at the start of a match.  Teams play on either the "Red" or "Blue" side, a distinction that significantly impacts strategy through differences in draft pick order and map layout. These differences shape gameplay and, in professional matches where every advantage matters, potentially decide outcomes. 

As such, this project aims to answer this question: is there a competitive edge on starting either Red or Blue side? Understanding the impact of map-side asymettry offers viewers deeper insights into this factor influences game outcomes at the highest levels of play, helping them refine their expectations and analyses. To address this, the project utilizes a dataset from Oracle's Elixir, built with 2024 professional match data. It provides a relevant source of data as it captures detailed gameplay statistics in thousands of pro-play games, such as individual player performance, team performance and time-specific in-game metrics.

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


# Data Cleaning and Exploratory Data Analysis

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
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/uni_blue.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


