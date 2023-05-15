# League Of Legends Soul Analysis
This is a project for DSC80 where we clean and perform exploratory data analysis, assess missingness, and conduct permutation tests. This website stands as a report of our findings.

**Names:** Zoe Ludena and Johel Tutak

## Introduction

Our dataset is on all of the professional League of Legends games that have taken place in 2023. The dataset contains 12 rows per game, one row per player and 2 rows of summary statistics (one for each team). Furthermore, there are over 100 columns of nearly all the data you could collect on a league of legends match. Our question is "Which dragon soul increases a team's win rate the most?". Although the data does not explicitly collect information on souls, it is Missing by Design, meaning we can collect the data from other columns, namely the columns associated with each dragon type, and columns containing how many dragons each team took.

Fellow League of Legends players may be interested in which dragon would increase their chances of winning. As League of Legends continues to add more dragons with different effects people may become curious as to which would benefit them the most. Through this experiment they can know which dragons and souls should be prioritized.

The columns we are interested in to answer, "Which dragon soul increases a team's win rate the most?" are: `"result"`, `"dragons"`, `"opp_dragons"`, `"elementaldrakes"`, `"opp_elementaldrakes"`, `"infernals"`, `"mountains"`, `"clouds"`, `"oceans"`, `"chemtechs"`, and `"hextechs"`. We chose these columns because together they showed us the result of the match, the number of dragons taken, and what kinds of dragons appeared. This allowed us to discover which dragon soul appeared in each game. When we cleaned our data to isolate the games where soul appeared we had 12 columns, with our addition of `"soul_type"`, which we calculated, and 1873 rows.

**Descriptions of Columns**
- `"result"` is `True` when the team won the game and `False` otherwise
- `"dragons"` is the number of dragons (including elemental and elder dragons) taken on the ally team
- `"opp_dragons"` is the number of dragons (including elemental and elder dragons) taken on the enemy team
- `"elementaldrakes"` is the number of dragons (not elder) that was taken on the ally team
- `"opp_elementaldrakes"` is the number of dragons (not elder) that was taken on the enemy team
- `"infernals"` is the number of infernal dragons taken by the ally team
- `"mountains"` is the number of mountain dragons taken by the ally team
- `"clouds"` is the number of cloud dragons taken by the ally team
- `"oceans"` is the number of ocean dragons taken by the ally team
- `"chemtechs"` is the number of chemtech dragons taken by the ally team
- `"hextechs"` is the number of hextech dragons taken by the ally team
- `"soul_type"` is the type of soul - it can be infernal, mountain, cloud, oceans, chemtech, or hextech

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

We first decided to only keep the relevant columns: `'result'`, `'dragons'`,  `'opp_dragons'`, `'elementaldrakes'`, `'opp_elementaldrakes'`,  `'infernals'`, `'mountains'`, `'clouds'`, `'oceans'`, `'chemtechs'`, `'hextechs'`, `'dragons (type unknown)'`, and only the relevant rows. Relevant rows are where information on dragons was entered and there were 4 elemental dragons taken by the same team (this is the requirement to get the soul). Note that this indirectly makes it one row per game, as only the 2 summary statistics rows contain dragon information, and only at most one of those rows can have 4 or more dragons. From here, we realized that we cannot work with data where `‘dragons (type unknown)’` was the recorded datatype because the type of dragon was not recorded (making it impossible to calculate soul). So we removed this column and any rows where `‘dragons (type unknown)’` was not null. After this, we added the column `‘soul_type’`, which recorded which type of soul was taken, and converted the results column from 0 and 1 to a series of booleans.

**Here is the head of our `souls` DataFrame:**

| result   |   dragons |   opp_dragons |   elementaldrakes |   opp_elementaldrakes |   infernals |   mountains |   clouds |   oceans |   chemtechs |   hextechs | soul_type   |
|:---------|----------:|--------------:|------------------:|----------------------:|------------:|------------:|---------:|---------:|------------:|-----------:|:------------|
| True     |         4 |             3 |                 4 |                     3 |           0 |           0 |        0 |        4 |           0 |          0 | oceans      |
| True     |         4 |             3 |                 4 |                     2 |           0 |           0 |        0 |        0 |           1 |          3 | hextechs    |
| True     |         4 |             1 |                 4 |                     1 |           1 |           0 |        0 |        0 |           0 |          3 | hextechs    |
| True     |         4 |             0 |                 4 |                     0 |           1 |           0 |        1 |        0 |           0 |          2 | hextechs    |
| True     |         4 |             0 |                 4 |                     0 |           0 |           1 |        1 |        0 |           0 |          2 | hextechs    |

### Univariate Analysis:
<iframe src="assets/univariate.html" width=800 height=600 frameBorder=0></iframe>
This is a pie chart that shows the percentage of wins for the team that got soul. You can notice teams are more likely to win when they have a soul.

### Bivariate Analysis:
<iframe src="assets/bivariate.html" width=800 height=600 frameBorder=0></iframe>
This is a bar chart that shows the percentage of wins by the specific soul type. Dragons like `"chemtechs"` or `"infernals"`, which are damage increases appear to be less helpful than `"clouds"` or `"hextechs"` which provide utility.

### Interesting Aggregates:
This `groupby()` highlights the mean number of dragons when teams lose or win. The first row is when the team loses and the second row is when the team wins. You can see the distribution between winning or losing with a particular soul. For instance you are much more likely to lose with `"chemtechs"` than win with it and you are more likely to win with `"hextechs"` than lose with them.

| result   |   infernals |   mountains |   clouds |   oceans |   chemtechs |   hextechs |
|:---------|------------:|------------:|---------:|---------:|------------:|-----------:|
| False    |    0.616438 |    0.732877 | 0.678082 | 0.671233 |    0.876712 |   0.424658 |
| True     |    0.640417 |    0.670527 | 0.675738 | 0.672264 |    0.675159 |   0.665895 |

## Assessment of Missingness

### NMAR Analysis
Yes, we believe that many columns in our data are Not Missing At Random (NMAR) due to the way this data is collected. Each game has 12 columns, a column for each of the team players and an additional column for each of the two teams. Many stats are missing for the player rows and not missing for the team rows, and vice versa. This makes the missingness of the rows dependent on the type of columns (and therefore many of the other columns). However, the correlation is not 1 to 1, meaning you cannot infer one column's missingness exactly from anothers, meaning it is not missing by design. Furthermore, only certain leagues tracked data on the elemental drakes, while others did not. `“clouds”`, one of the drakes, we speculate will have its missingness dependent on the `“league”` column based on this fact.

Additional data we want to collect to make the missingness MAR is the elemental dragon data from all the matches played in leagues that did not record that data. This would allow us to fill out the columns missing due to the league not collecting dragon data, and make the missingness not depend on the league.

### Missingness Dependency
**Missingness of `"clouds"` does depend on `"league"`**
We wanted to determine if `"league"` and `"clouds"` were Missing at Random or Missing Completely at Random.

Here is the observed distribution when `"clouds"` was missing:

| league   |     clouds |
|:---------|-----------:|
| CBLOL    | 0.0230796  |
| CBLOLA   | 0.0238489  |
| EBL      | 0.0142324  |
| EL       | 0.00788553 |
| EM       | 0.0267339  |
| ESLOL    | 0.0138478  |
| GL       | 0.0171174  |
| GLL      | 0.0138478  |
| HC       | 0.0132708  |
| HM       | 0.0140401  |
| LAS      | 0.00615456 |
| LCK      | 0.0482748  |
| LCK CL   | 0.0492365  |
| LCO      | 0.0123091  |
| LCS      | 0.0236566  |
| LDL      | 0.106858   |
| LEC      | 0.0330807  |
| LFL      | 0.0213486  |
| LFL2     | 0.0219256  |
| LHE      | 0.0113475  |
| LJL      | 0.0292341  |
| LLA      | 0.018656   |
| LMF      | 0.0165404  |
| LPL      | 0.0874716  |
| LPLOL    | 0.0134631  |
| LVP DDH  | 0.0165404  |
| MSI      | 0.00615456 |
| NACL     | 0.094434   |
| NEXO     | 0.0144247  |
| NLC      | 0.0140401  |
| PCS      | 0.0269262  |
| PGN      | 0.0140401  |
| PRM      | 0.0221179  |
| SL       | 0.0386583  |
| TCL      | 0.0151941  |
| UL       | 0.0215409  |
| VCS      | 0.0315421  |
| VL       | 0.016925   |

Here is the observed distribution when `"clouds"` was not missing:

| league   |     clouds |
|:---------|-----------:|
| CBLOL    | 0.0286465  |
| CBLOLA   | 0.0296013  |
| EBL      | 0.0176653  |
| EL       | 0.00978754 |
| EM       | 0.0331821  |
| ESLOL    | 0.0171879  |
| GL       | 0.0212461  |
| GLL      | 0.0171879  |
| HC       | 0.0164717  |
| HM       | 0.0174266  |
| LAS      | 0.00763905 |
| LCK      | 0.0599188  |
| LCK CL   | 0.0611124  |
| LCO      | 0.0152781  |
| LCS      | 0.0293626  |
| LEC      | 0.0410599  |
| LFL      | 0.026498   |
| LFL2     | 0.0272141  |
| LHE      | 0.0140845  |
| LJL      | 0.0362855  |
| LLA      | 0.0231559  |
| LMF      | 0.02053    |
| LPLOL    | 0.0167104  |
| LVP DDH  | 0.02053    |
| MSI      | 0.00763905 |
| NACL     | 0.117212   |
| NEXO     | 0.017904   |
| NLC      | 0.0174266  |
| PCS      | 0.0334209  |
| PGN      | 0.0174266  |
| PRM      | 0.0274529  |
| SL       | 0.0479828  |
| TCL      | 0.0188589  |
| UL       | 0.0267367  |
| VCS      | 0.0391502  |
| VL       | 0.0210074  |

Our observed statistic was: 0.09716505750663537

Our p-value was: 0.0

Here is the empirical distribution of the test statistic:
<iframe src="assets/dist_league.html" width=800 height=600 frameBorder=0></iframe>

**Missingness of `"clouds"` does not depend on `"opp_towers"`**
We wanted to determine if `"opp_towers"` and `"clouds"` were Missing at Random or Missing Completely at Random.

Here is the observed distribution when `"clouds"` was missing:

|   opp_towers |    clouds |
|-------------:|----------:|
|            0 | 0.0528504 |
|            1 | 0.0938242 |
|            2 | 0.107482  |
|            3 | 0.112233  |
|            4 | 0.0599762 |
|            5 | 0.0261283 |
|            6 | 0.0356295 |
|            7 | 0.054038  |
|            8 | 0.0908551 |
|            9 | 0.133017  |
|           10 | 0.140736  |
|           11 | 0.0932304 |

Here is the observed distribution when `"clouds"` was not missing:

|   opp_towers |    clouds |
|-------------:|----------:|
|            0 | 0.0541895 |
|            1 | 0.0896395 |
|            2 | 0.113392  |
|            3 | 0.104321  |
|            4 | 0.0577704 |
|            5 | 0.0358081 |
|            6 | 0.025185  |
|            7 | 0.0525185 |
|            8 | 0.0840296 |
|            9 | 0.145142  |
|           10 | 0.127954  |
|           11 | 0.11005   |

Our observed statistic was: 0.045874161997630926

Our p-value was: 0.151

Here is the empirical distribution of the test statistic:
<iframe src="assets/opp_tower_url.html" width=800 height=600 frameBorder=0></iframe>

## Hypothesis Testing
**Null Hypothesis:** The distribution of souls in games where the team with soul won is the same as the distribution of souls in games where the team with the soul lost.

**Alternate Hypothesis:** The distribution of souls in games where the team with soul won is different than the distribution of souls in games where the team with the soul lost.

**Test Statistic:** We will be using the Total Variation Distance (TVD).
- We chose to use TVD because we are using categoricial data, more specifically whether two sample distributions came from the same distribution.

**Significance Level:** 5% AKA 0.05
- We chose this significance level because it is the standard

**p-value:** 0.1225
- We did 10,000 simulations

**Conclusion:** We fail to reject the null hypothesis. Meaning we believe that the distributions are the same.

Since we failed to reject the null hypothesis this means there is little evidence that the distribution of souls in games where the team won with soul is different from the distribution of souls in games where the team won with soul. We believe this means that souls have statistically different power levels because the proportion of wins and losses seems the same.