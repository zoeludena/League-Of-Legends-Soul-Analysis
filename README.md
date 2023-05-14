# League Of Legends Soul Analysis
This is a project for DSC80 where we clean and perform exploratory data analysis, assess missingness, and conduct permutation tests. This website stands as a report of our findings.

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
