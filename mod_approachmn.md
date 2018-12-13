---
title: Modeling approach
nav_include: 5
---

As stated in the problem statement, the main objective of this project is to predict outcome of the 64
matches played in World Cup 2018 using machine learning.

# Baseline Model or  FIFA Ranking based Model

The baseline model has the home_team and away_team. These are the 2 teams that played the
match. For each of these 2 teams, the following predictors are added:
1. FIFA ranking from June 7th, 2018(just before the inauguration of the games).
2. Coach salary: Coach’s Salary for each team.
3. Average player age for each team.
4. Aggregate Total number of goals each player scored for the team.
5. Average number of caps (International match participations of every players in the team averaged).
6. Number of wins of the last 20 games that preceded the the world cup for each team.
The x_train now has (6*2) features and y_train is the outcome of the match that will have value of either
the name of the winning team or ‘Draw’ if the outcome was a draw.

Multiple models such as logistic regression,
Random Forest, Decision Tree, k-NN, LDA and QDA were built on the training set of data.
Later on, we tested our models on ensemble methods as well so that we get the best possible accuracy level.



# Our Model or NON-FIFA Ranking Based Model

The second model will be trained on features other than the 6 predictors used in the baseline model.
There are n predictors for each team (a set of n predictors for the home_team and set of n predictors
for the away_team). Using feature elimination x predictors were removed to avoid overfitting. With the
remaining predictors, models were built with accuracies as listed in the table below.

For our own model (69 features for each team):
- Number of players who played in top 5 European leagues in 2017-2018
- Number of players who played in the champions league 2017-2018
- Average Players Wage
- Average Players Value
- Average Players overall
- Number of wins for the 20 most recent matches
- Different players positions etc…
