---
title: Problem Statement
nav_include: 2
---
Motivation
Various sports are played around the world. Fan clubs passionately cheer for their teams. Arguably
predicting winners at these games is common across sports. Teams that have been winning
historically or recently have higher chances of winning. However, no consecutive world cup has the

same set of players. Taking into consideration all the data that is available to us, factors that impact
outcome of games can be determined.
Objective
The main objective of this project is to predict the outcome of all 64 games played in the World Cup
2018. The following three-step approach is implemented to arrive at this objective:
1. Explore all the features that play a role in the outcome of the games. In detail, analyze the
significant predictors.
2. Build a baseline model based on the FIFA ranking for national teams before the actual game.
(Ranking on June 7th, 2018 was used) In addition to the FIFA ranking predictors, few other
predictors were used to predict the outcome of each of the games.
3. Build a second model with predictors other than the FIFA ranking to predict the games played
at world cup.
4. Compare the two models in terms which model predicts the outcome correctly. (Since results
from the World Cup is available at the time of this project)

5. Build a dataset with all the available variables for the teams, players. Using Explanatory Data
Analysis analyze the impact of these variables to the outcome. Analyze any correlation
among the variables themselves.
6. Compare the 2 models obtained in step 2 and step 3.
Challenges and Limitations
This problem statement contains factors that the machine-learning model cannot account for. The
model built will be just as good as the data used to build it, since the data will not represent these
criteria, our end model will not be able to predict based on these factors either. These variables are
as follows:
1. Unfair referees at a match
2. Fluctuating variables such as weather on the day of the game, fans’ sentiments, which are not
easy to quantify.
3. Being a human based activity, physiological effects of players playing on the world’s biggest
stage are in many cases are not quantifiable