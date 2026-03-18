# Is Blue Side Actually Better? Examining Regional Playstyle Differences and Side Selection Advantage in Professional League of Legends

**By:** Loren Pan

## Introduction
### Project Question:
Across the four major regions (LTA N, LEC, LPL, LCK) does having first pick on Blue side increase win rate?

Blue side advantage has been a long-standing topic in the competitive League of Legends scene, with various analysts and players arguing that it provides a significant competitive advantage. In this analysis, if we were to find that across all four regions, first pick leads to higher winrates, this would suggest that side selection plays a meaningful role in competitive balance and decision making. On the other hand, if this effect varies by region, then this could reveal important differences in regional playstyle.

### Dataset Information
This project used a cleaned subset of the 2025 League of Legends esports match data from Oracle's Elixir. After filtering the dataset to the four major leagues (`LTA N`, `LEC`, `LPL`, and `LCK`) alongside other data cleaning, the resulting working dataframe contains 3758 rows with 157 columns. Of those 157 columns, the ones we used in this study are:

- `result`: Whether a team won (1 = yes, 0 = no)
- `league`: Region the team belongs to (LTA N, LEC, LPL, LCK)
- `firstPick`: Whether a team had first pick in draft (1 = yes, 0 = no)

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning Steps
The raw Oracle's Elixir match dataset contains both team-level and player-level rows. Because my analysis is focused on team-level outcomes in the four major regions, I first cleaned the dataframe by removing rows where `playerid` was present.

Next, I converted various binary indicator columns from `0`/`1` into boolean values (`True`/`False`). These columns were generated as event indicators in the original dataframe, so making them boolean values makes the dataframe easier to work with.

I then filtered the dataframe to keep only rows where the `league` column contained the four major regions.

Finally, I dropped any columns that were entirely missing values since they don't contribute any information to the question I'm asking.

### First Five Rows of the Cleaned Dataframe
|   result | league   | firstPick   |
|---------:|:---------|:------------|
|        1 | LPL      | True        |
|        0 | LPL      | False       |
|        0 | LPL      | True        |
|        1 | LPL      | False       |
|        0 | LPL      | True        |

### Univariate Analysis
<iframe
  src='assets/distribution-of-game-length.html'
  width='800'
  height='600'
  frameborder='0'
></iframe>

This is a histogram of game length. The distribution is right-skewed, with most games clustering between roughly 1600 and 2100 units.

### Bivariate Analysis


## Assessment of Missingness

## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis
