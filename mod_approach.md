---
title: Modeling approach
nav_include: 5
---

As stated in the problem statement, the main objective of this project is to predict outcome of the 64
matches played in World Cup 2018 using machine learning.
Section “Data” discusses using the multi class response variable to predict outcome.

# Baseline Model

The baseline model has the home_team and away_team. These are the 2 teams that played the
match. For each of these 2 teams, the following predictors are added:
1. FIFA ranking from June 7th, 2018(just before the inauguration of the games).
2. Coach salary: Coach’s Salary for each team.
3. Average player age for each team.
4. Aggregate Total number of goals each player scored for the team.
5. Average number of caps (International match participations of every players in the team averaged).
6. Number of wins of the last 20 games that preceded the the world cup for each team.
The x_train now has (6*2) features and y_train is the outcome of the match that will have value of either
the name of the winning team or ‘Draw’ if the outcome was a draw. Multiple models such as logistic regression,
Random Forest, Decision Tree, k-NN, LDA and QDA were built on the training set of data. Here’s a
table of the models and their corresponding accuracies on the validation set.

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Model</th>
      <th>CV_Accuracy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Random Forest</td>
      <td>0.6016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Decision Tree</td>
      <td>0.5864</td>
    </tr>
    <tr>
      <th>3</th>
      <td>KNN</td>
      <td>0.5404</td>
    </tr>
    <tr>
      <th>4</th>
      <td>LDA</td>
      <td>0.5205</td>
    </tr>
    <tr>
      <th>5</th>
      <td>QDA</td>
      <td>0.5126</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Logistic Regression</td>
      <td>0.4950</td>
    </tr>
  </tbody>
</table>
</div>

Later on we tested our models on ensemble methods and neural networks as well so that we get the best possible accuracy level.



# Our Model

The second model will be trained on features other than the 6 predictors used in the baseline model.
There are n predictors for each team (a set of n predictors for the home_team and set of n predictors
for the away_team). Using feature elimination x predictors were removed to avoid overfitting. With the
remaining predictors, models were built with accuracies as listed in the table below.

For our own model (34 features for each team):
- Number of players who played in top 5 european league in 2017-2018
- Number of players who played in the champions league 2017-2018
- Average Players Wage
- Average Players Value
- Average Players overall
- Number of wins for the 20 most recent matches
- Different players positions etc…
