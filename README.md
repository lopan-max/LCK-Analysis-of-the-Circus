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

| result | league | firstPick |
|---|---|---|
| 1 | LPL | True |
| 0 | LPL | False |
| 0 | LPL | True |
| 1 | LPL | False |
| 0 | LPL | True |

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
|---|---:|---:|
| LCK | 0.490090 | 0.509910 |
| LEC | 0.441176 | 0.558824 |
| LPL | 0.469565 | 0.530435 |
| LTA N | 0.488263 | 0.511737 |

This pivot table confirms the the results I found in the grouped bar chart in my bivariate analysis where the LEC and LPL have a pronounced difference in winrate between Red and Blue while the LTA N and LCK have a difference, but less pronounced.

## Assessment of Missingness
### MNAR Analysis
I believe that `opp_deathsat15` is MNAR. Since `opp_deathsat15` is a statistic recorded at 15 minutes, it is reasonable to think that its missingness may depend on the game state itself, such as whether the game ended before the 15-minute mark. Because the missingness seems tied to the underlying game processes, I believe it could be MNAR.

To better explain the missingness and possibly make it MAR, I would want additional information about the data collection process, such as whether `opp_deathsat15` is only recorded for games that reach 15 minutes, whether shortened games are excluded from the stat collection, or whether there were logging issues at that timestamp.

### Missingness Dependency
I tested whether the missingness of `opp_deathsat15` and `split` depended on `gamelength` using a permutation test.

`opp_deathsat15`: The observed difference in mean `gamelength` between non-missing and missing rows was 24.39, with a p-value of 0.018. This suggests that missingness in `opp_deathsat15` is associated with `gamelength`, so it is not MCAR with respect to `gamelength`.

<iframe
  src='assets/opp_deathsat15-missingness.html'
  width='800'
  height='600'
  frameborder='0'
></iframe>

`split`: The observed difference in mean `gamelength` between non-missing and missing rows was 59.15, with a p-value of 0.2085. Since this p-value is not small, there is no strong evidence that missingness in `split` depends on `gamelength`.

<iframe
  src='assets/split-missingness.html'
  width='800'
  height='600'
  frameborder='0'
></iframe>

## Hypothesis Testing
### Null and Alternative Hypotheses
For each region (`LTA N`, `LEC`, `LPL`, and `LCK`), I tested whether Blue side first pick is associated with a higher win rate.

- **Null hypothesis:** Within a given region, Blue side and Red side have the same win rate. Any observed difference is due to random chance.
- **Alternative hypothesis:** Within a given region, Blue side has a higher win rate than Red side.

### Test Statistic
I used the difference in mean win rate between Blue side and Red side within each region:

This is a good test statistic because it directly measures whether Blue side has an advantage in winning.

### Significance Level
Because I ran four separate tests, I used a Bonferroni correction where alpha is 0.0125.

This is a good choice because it controls the overall chance of a false positive across all four regions.

### P-values and Conclusion
The p-values from the permutation tests were:

- **LTA N:** 0.3489
- **LEC:** 0.0018
- **LPL:** 0.0078
- **LCK:** 0.2697

Thus, I reject the null hypothesis for LEC and LPL, but fail to reject it for LTA N and LCK.

### Final Interpretation
These results suggest that Blue side may provide a real win-rate advantage in LEC and LPL, but not in LTA N or LCK. This supports the idea that Blue side advantage may vary by region rather than being universal.

## Framing a Prediction Problem
### Prediction Problem:
Predict which major league a game was played in given in-game statistics and early-game performance metrics.

### Type
Multiclass classification

### Response Variable
`league`

We chose this because our hypothesis test demonstrated that regions play differently, specifically LPL and LEC have higher Blue side winrates. This suggests that regional playstyle differences are detectable from in-game stats.

### Features Used and Justification of Time of Prediction
We will use features that are either pre-game or statistics recorded at or before 15 minutes into the game. This is because 15 minutes usually marks the end of the early-game in a League of Legends match. 

Ex.: `firstPick`, `firstblood`, `firstdragon`, `golddiffat15`, `xpdiffat15`, `csdiffat15`, `killsat15`

### Evaluation Metric
We will use a macro F1-score over accuracy. Accuracy is misleading as the dataset is imbalanced across regions, such as number of games. Macro F1-score computes the F1-score for each class independently and averages them, meaning each region is weighted equally regardless of how many games it contributes. This is important for our problem because we care equally about correctly identifying games from all four regions, not just the most common one.

## Baseline Model
My baseline model is a multiclass logistic regression classifier that predicts the region (`league`) of a game. I used the following features:

- `firstPick`: nominal  
  This is a binary categorical variable indicating whether the team had first pick / Blue side. I encoded this feature using one-hot encoding.

- `killsat15`: quantitative  
  This is a numeric count of kills at 15 minutes. I left this feature as is.

- `golddiffat15`: quantitative 
  This is a numeric measure of gold difference at 15 minutes. I left this feature as is.

To prepare the data for the model, I used a sklearn Pipeline with a ColumnTransformer:
- `firstPick` was one-hot encoded using `OneHotEncoder(drop='if_binary', handle_unknown='ignore')`
- `killsat15` and `golddiffat15` were passed through without additional encoding

### Model Performance
I evaluated the model using macro F1 score since the classes are imbalanced and I care equally about each region.

- Training macro F1: 0.2272
- Test macro F1: 0.2270

### Is the Model Good?
I do not think this baseline model is very good. Even though the training and test scores are almost identical, both scores are quite low, which suggests that the model is not learning strong patterns from these features. The similar train and test performance does suggest that the model is not badly overfitting, but overall it is still a weak classifier and leaves a lot of room for improvement.

## Final Model
Compared to the baseline model, my final model added more features that better capture early-game performance and overall game tempo, which are both relevant to differences in regional playstyles.

### Features Used
The final model uses the following features:

- `firstPick`
- `firstdragon`
- `firsttower`
- `killsat15`
- `golddiffat15`
- `ckpm`
- `wpm`
- `vspm`

I also added two engineered features:

- `early_aggression` = `killsat15` × `golddiffat15`
- `vision_efficiency` = `wpm` / (`ckpm` + 1)

`early_aggression` captures how dominant a team was early. A team that has both more kills and a gold lead is more dominant than either stat can individually show. For example, a team could have a high gold lead from farming CS without fighting, or high kills without translating it to other gold leads. Multiplying the two creates a single feature that only scores high when a team is winning in both combat and economy at 15 minutes. This could be useful in distinguishing between regions who are known for "bloodier" games, such as the LPL, and regions who are less so, such as the LCK.

`vision_efficiency` captures how well a team prioritizes vision relative to how "bloody" the game was. Dividing `wpm` by `ckpm` controls for game pace and isolates a team's tendency to ward regardless of how "bloody" the game is. LCK teams are historically known for good vision control even in slower games, so this ratio should be higher for LCK than for regions like LPL where high `ckpm` would pull the ratio down. This makes it a meaningful regional differentiator beyond what `wpm` alone captures.

Finally, while there are concerns of collinearity , these fears don't actually pan out. Our regularization via C applies some regularization and also in practice, our final model performed better than our baseline model, which indicates that collinearity didn't severly hurt our model.

### Model Selection
I used logistic regression and selected the final model with GridSearchCV using 5-fold cross-validation. I tuned the regularization hyperparameter `C`, and the best value was:
- `C = 10`

This choice means the model performed best with relatively weaker regularization, allowing it to fit the data more flexibly.

### Performance
The final model improved on the baseline model:

- Baseline test macro F1: 0.2270
- Final test macro F1: 0.3226

The final model also achieved a train macro F1 of 0.3537, which is reasonably close to the test score and suggests it generalizes fairly well.

### Conclusion
I believe the final model is better than the baseline because it uses a richer set of features that better reflect early-game behavior and strategic style, and because the hyperparameter was selected using cross-validation rather than chosen arbitrarily. The improvement in test macro F1 shows that these additions helped the model generalize better to unseen data.

## Fairness Analysis
### Group X and Group Y
I compared the model’s performance on two groups of regions:

- Group X: `LPL` and `LEC`
- Group Y: `LCK` and `LTA N`

I chose these groups because my hypothesis test found that Blue side advantage was statistically significant for `LPL` and `LEC`, but not for `LCK` and `LTA N`. This makes them a natural comparison for checking whether the model behaves differently across regions.

### Evaluation Metric
I used macro F1 score as the evaluation metric. This is appropriate because it weights each region equally, which matters in this dataset since the classes are not perfectly balanced.

### Hypotheses
- Null hypothesis: The model’s macro F1 score is the same for Group X and Group Y. Any observed difference is due to chance.
- Alternative hypothesis: The model’s macro F1 score is lower for Group X than for Group Y.

### Test Statistic
I used the difference in macro F1 score between the two groups.

### Significance Level
I used a significance level alpha of 0.05.

### P-value and Conclusion
The observed values were:

- Group X macro F1: 0.1088
- Group Y macro F1: 0.2800
- Observed difference: -0.1712
- P-value: 0.0

Since the p-value is less than 0.05, I reject the null hypothesis. This suggests that the model performs significantly worse on `LPL`/`LEC` games than on `LCK`/`LTA N` games. In other words, the model is not fair across these two groups under my chosen fairness metric.
