---
title: Data
nav_include: 3
---

1. Using scraping techniques, data to start the analysis was built. (BeautifulSoup was used for
scraping the data):

- FIFA rankings were scraped from the official FIFA website (www.fifa.com).
- The complete squad data detailing countries that qualified for the world cup (32 countries
in total) and the selected players from each country (23 players in total) were scraped
from Wikipedia page.
- Additional data was scraped to build a dataset of the actual games that were played in
2018 (the games that needs to be predicted by the model) and the actual results. In
machine learning terms, these are the x-test and the y-test sets.
- Additionally data was scraped to create a new predictor of the 32 national teams’ coaches
and their respective salaries.

2. For the training set, a set of previously played games with the known outcome was used. Dataset
from www.sofifa.com (Kaggle), which included all the matched played since 1872 with the final
score, was evaluated. The important information for our analysis included in the dataset were the
team names, their home scores, away scores, city/country in which the match was played, and a
flag indicating the neutrality of the match(obtained from FIFA.com). From the same dataset
x_train, a tuplet of the Home and Away team names, and y_train, the outcome of the match
between those two team is computed. y_train is later transformed to 3-class classification where
class “0” denotes the home team won, class “1” denotes the match result was a draw and “2” if
the away team won. Also for the x_train was filtered to only the games played after 1990 and to
the national teams that qualified at the world cup 2018. That gave us a training set of 3564 games
to train our future models with.
3. The third data source used was players’ data, which was downloaded from www.sofifa.com. This
dataset contains all the various attributes about each player (Name, Age, Nationality, Overall,
Potential, Acceleration, Agility and Balance). We grouped this dataset by the nationality of the
players and averaged them for every existing column. This dataset will be used later on to create
our own feature/predictors to predict the outcome of games without relying on the FIFA ranking.
