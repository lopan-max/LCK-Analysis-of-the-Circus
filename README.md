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
<iframe
  src='assets/distribution-of-winrate-by-side.html'
  width='800'
  height='600'
  frameborder='0'
></iframe>

This is a grouped bar chart of win rate by side across the 4 major leagues. Across all leagues, the blue side consistently has a slightly higher win rate than the red side. This advantage is most pronounced in the LEC and LPL, while LTA N and LCK show only a minimal difference.

### Pivot Table
| league | Red | Blue |
|:-------|------:|-----:|
| LCK    | 0.490090 | 0.509910 |
| LEC    | 0.441176 | 0.558824 |
| LPL    | 0.469565 | 0.530435 |
| LTA N  | 0.488263 | 0.511737 |

This pivot table confirms the the results I found in the grouped bar chart in my bivariate analysis where the LEC and LPL have a pronounced difference in winrate between Red and Blue while the LTA N and LCK have a difference, but less pronounced.

## Assessment of Missingness
### MNAR Analysis
I believe that `opp_deathsat15` is MNAR. Since `opp_deathsat15` is a statistic recorded at 15 minutes, it is reasonable to think that its missingness may depend on the game state itself, such as whether the game ended before the 15-minute mark. Because the missingness seems tied to the underlying game processes, I believe it could be MNAR.

To better explain the missingness and possibly make it MAR, I would want additional information about the data collection process, such as whether `opp_deathsat15` is only recorded for games that reach 15 minutes, whether shortened games are excluded from the stat collection, or whether there were logging issues at that timestamp.

### Missingness Dependency


## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis
