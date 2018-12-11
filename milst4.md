---
title: Results
nav_include: 6
---





## Results of our baseline model

In this section, we display the results of our baseline model and we look for the best Hyper paramters for each model tried. The model to be tried include:

- Mutinomial logistic Regression
- kNN
- Decision Tree
- Linear Discriminant Analysis / Quadratic Linear Discriminant Analysis
- Random Forest
- Neural Network






## Classification methods





## Logisitic regression - Baseline model


# finding top 2 components



    number of components that explain at least 90% of the variance= 8



![png](milst4_files/milst4_54_1.png)


### train set



    'conf matrix of logisitic regression'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>52</td>
      <td>32</td>
      <td>48</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>612</td>
      <td>1346</td>
      <td>460</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>322</td>
      <td>220</td>
      <td>472</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the model logistic regression multinomial model is: 0.5246913580246914


### test set

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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Morocco</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Portugal</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>France</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of logisitic regression of the baseline model on the first test set'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>0</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>7</td>
      <td>16</td>
      <td>11</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>2</td>
      <td>1</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the model logistic regression multinomial model on test set 1 is: 0.5208333333333334



    'conf matrix of logistic regression of the baseline model of the second test set'



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
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Team 1</th>
      <td>7</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>2</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the baseline model with logistic regression on test set 2 is: 0.625


## KNN - baseline model

#Pick the best k of the model



    Best K is 5.


###train set


    'conf matrix of the KNN model of the train set on the baseline model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>546</td>
      <td>288</td>
      <td>242</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>330</td>
      <td>1202</td>
      <td>260</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>110</td>
      <td>108</td>
      <td>478</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of the knn model of the train set on the baseline model: 0.6245791245791246
    accuracy score of the knn model Validation Set on the baseline model: 0.5518964059749552


### World Cup 2018 - Group  Phase games




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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Morocco</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Portugal</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>France</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of KNN of the baseline model on the first test set'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>3</td>
      <td>1</td>
      <td>7</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>4</td>
      <td>14</td>
      <td>8</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>2</td>
      <td>2</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the baseline model with KNN on the first test set is: 0.5


### World Cup 2018 - Knockout Games


    'conf matrix of KNN of the baseline model on the second test set'



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
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Team 1</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>7</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the baseline model with KNN on second  test set is: 0.4375


## Decision tree-Baseline Model








![png](milst4_files/milst4_69_0.png)


Best depth is 16



## Linear Discriminant Analysis (LDA)-Baseline model

### Train Data Set



    'conf matrix of lda on the baseline model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>64</td>
      <td>40</td>
      <td>58</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>600</td>
      <td>1326</td>
      <td>440</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>322</td>
      <td>232</td>
      <td>482</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of lda on baseline model: 0.5252525252525253
    accuracy score of lda on baseline model Validation Set: 0.5247027017801897


### World Cup 2018 - Group  Phase games



    'conf matrix of the lda model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>4</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>1</td>
      <td>14</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>4</td>
      <td>1</td>
      <td>20</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of the lda model: 0.7916666666666666
    accuracy score of the lda model Validation Set: 0.5247027017801897


### World Cup 2018 - Knockout Games


    accuracy score of lda on baseline model of the second test set: 0.0


## Quadratic Discriminant Analysis (QDA)

### Training Data Set

    'conf matrix of qda on baseline model train set'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>240</td>
      <td>182</td>
      <td>186</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>482</td>
      <td>1198</td>
      <td>344</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>264</td>
      <td>218</td>
      <td>450</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of qda on baselin model train set: 0.5297418630751964
    accuracy score of qda on baseline model Validation Set: 0.5103846653394701


### World Cup 2018 - Group  Phase games


    'conf matrix of the qda model'

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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>1</td>
      <td>2</td>
      <td>7</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>5</td>
      <td>13</td>
      <td>8</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>3</td>
      <td>2</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of the qda model: 0.4375
    accuracy score of the qda model Validation Set: 0.5103846653394701


### World Cup 2018 - Knockout Games


    accuracy score of qda on baseline model second test: 0.5625
    accuracy score of qda baseline model on Validation Set: 0.5103846653394701


# Ensemble methods

In this section we try different types of Ensembles methods:
- Random Forest
- Bagging
- Boosting

## Random Forest- baseline model

### train set

    accuracy score for the random forest model: 0.6728395061728395


### test set 1


    accuracy score for the random forest test1 on baseline model: 0.6666666666666666


### test set 2


    accuracy score for the random forest test2 on baseline model: 0.1875


##Bagging


    train bootstrapped table:

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
      <th>Bootstrap-Model_1</th>
      <th>Bootstrap-Model_2</th>
      <th>Bootstrap-Model_3</th>
      <th>Bootstrap-Model_4</th>
      <th>Bootstrap-Model_5</th>
      <th>Bootstrap-Model_6</th>
      <th>Bootstrap-Model_7</th>
      <th>Bootstrap-Model_8</th>
      <th>Bootstrap-Model_9</th>
      <th>Bootstrap-Model_10</th>
      <th>...</th>
      <th>Bootstrap-Model_36</th>
      <th>Bootstrap-Model_37</th>
      <th>Bootstrap-Model_38</th>
      <th>Bootstrap-Model_39</th>
      <th>Bootstrap-Model_40</th>
      <th>Bootstrap-Model_41</th>
      <th>Bootstrap-Model_42</th>
      <th>Bootstrap-Model_43</th>
      <th>Bootstrap-Model_44</th>
      <th>Bootstrap-Model_45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training-Row_1</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Training-Row_2</th>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Training-Row_3</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Training-Row_4</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Training-Row_5</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>



    test1 bootstrapped table:


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
      <th>Bootstrap-Model_1</th>
      <th>Bootstrap-Model_2</th>
      <th>Bootstrap-Model_3</th>
      <th>Bootstrap-Model_4</th>
      <th>Bootstrap-Model_5</th>
      <th>Bootstrap-Model_6</th>
      <th>Bootstrap-Model_7</th>
      <th>Bootstrap-Model_8</th>
      <th>Bootstrap-Model_9</th>
      <th>Bootstrap-Model_10</th>
      <th>...</th>
      <th>Bootstrap-Model_36</th>
      <th>Bootstrap-Model_37</th>
      <th>Bootstrap-Model_38</th>
      <th>Bootstrap-Model_39</th>
      <th>Bootstrap-Model_40</th>
      <th>Bootstrap-Model_41</th>
      <th>Bootstrap-Model_42</th>
      <th>Bootstrap-Model_43</th>
      <th>Bootstrap-Model_44</th>
      <th>Bootstrap-Model_45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Test-Row_1</th>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Test-Row_2</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_3</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Test-Row_5</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>



    test2 bootstrapped table:



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
      <th>Bootstrap-Model_1</th>
      <th>Bootstrap-Model_2</th>
      <th>Bootstrap-Model_3</th>
      <th>Bootstrap-Model_4</th>
      <th>Bootstrap-Model_5</th>
      <th>Bootstrap-Model_6</th>
      <th>Bootstrap-Model_7</th>
      <th>Bootstrap-Model_8</th>
      <th>Bootstrap-Model_9</th>
      <th>Bootstrap-Model_10</th>
      <th>...</th>
      <th>Bootstrap-Model_36</th>
      <th>Bootstrap-Model_37</th>
      <th>Bootstrap-Model_38</th>
      <th>Bootstrap-Model_39</th>
      <th>Bootstrap-Model_40</th>
      <th>Bootstrap-Model_41</th>
      <th>Bootstrap-Model_42</th>
      <th>Bootstrap-Model_43</th>
      <th>Bootstrap-Model_44</th>
      <th>Bootstrap-Model_45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Test-Row_1</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_2</th>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Test-Row_3</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Test-Row_4</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_5</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>



    bagging model, training set accuracy: 0.5359147025813692
    bagging model, test-1 set accuracy: 0.4166666666666667
    bagging model, test-2 set accuracy: 0.4375



![png](milst4_files/milst4_97_0.png)


## Boosting


![png](milst4_files/milst4_99_0.png)



![png](milst4_files/milst4_101_0.png)




    Depth = 1: best test set 1 accuracy at 1 estimators.
    train set accuracy score: 0.6041666666666666

    Depth = 2: best test set 1 accuracy at 342 estimators.
    train set accuracy score: 0.6041666666666666

    Depth = 3: best test set 1 accuracy at 356 estimators.
    train set accuracy score: 0.6041666666666666

    Depth = 4: best test set 1 accuracy at 673 estimators.
    train set accuracy score: 0.6666666666666666

    Depth = 1: best test set 2 accuracy at 14 estimators.
    train set accuracy score: 0.6875

    Depth = 2: best test set 2 accuracy at 38 estimators.
    train set accuracy score: 0.625

    Depth = 3: best test set 2 accuracy at 18 estimators.
    train set accuracy score: 0.5625

    Depth = 4: best test set 2 accuracy at 51 estimators.
    train set accuracy score: 0.5625



## Neural Network


### Training Data Set





    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    dense_30 (Dense)             (None, 200)               2600      
    _________________________________________________________________
    dense_31 (Dense)             (None, 3)                 603       
    =================================================================
    Total params: 3,203
    Trainable params: 3,203
    Non-trainable params: 0
    _________________________________________________________________





    the loss of this model is: 0.2612320419012095
    the accuracy of this model is: 0.6470258137593767


### regularized neural network train set



    Model 2
    the loss of this model is: 0.2612320419012095
    the accuracy of this model is: 0.6470258137593767


### World Cup 2018 - Group  Phase games



    The accuracy score of test set 1 of this neural net is: 0.4583333333333333


### World Cup 2018 - Knockout Games



    The accuracy score of test set 2 of this neural net is: 0.625


# Model comparison

In this Section we apply 5-fold Cross Validation to all the models designed above in order to fund which ones has the best Training and Validation Accuracy


    Mean CV Accuracy Score by Model:



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


### Coefficient analysis-Logistic regression



![png](milst4_files/milst4_116_1.png)




    <Container object of 1 artists>




![png](milst4_files/milst4_117_1.png)



# Second Model built with non FIFA - Ranking Features



# Results



## Logistic regression- Second model

### Training Data Set


    'conf matrix of logisitic regression of our model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>188</td>
      <td>106</td>
      <td>124</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>532</td>
      <td>1306</td>
      <td>372</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>266</td>
      <td>186</td>
      <td>484</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the model logistic regression multinomial model is: 0.5549943883277216


### World Cup 2018 - Group  Phase games



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>France</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of logisitic regression of our model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>2</td>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>6</td>
      <td>16</td>
      <td>9</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>1</td>
      <td>1</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of the model logistic regression multinomial model on test set of our model is: 0.5416666666666666


finding top 2 components



    number of components that explain at least 90% of the variance= 10



![png](milst4_files/milst4_137_1.png)


## KNN model- Second model

### Training Data Set



    'conf matrix of the KNN model of our model'

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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>560</td>
      <td>296</td>
      <td>254</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>294</td>
      <td>1178</td>
      <td>270</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>132</td>
      <td>124</td>
      <td>456</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score of the knn model training set: 0.6156004489337823


### World Cup 2018 - Group  Phase games


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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Iran</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>France</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of KNN of our model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>4</td>
      <td>4</td>
      <td>8</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>3</td>
      <td>12</td>
      <td>5</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>2</td>
      <td>1</td>
      <td>9</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the model KNN model on test set is: 0.5208333333333334


### World Cup 2018 - Knockout  Phase games



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>France</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Russia</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Croatia</td>
      <td>Denmark</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brazil</td>
      <td>Brazil</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of KNN of our model'



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
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Team 1</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>6</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score or the model KNN model on test set is: 0.25


## *** DECISION TREE FOR OUR MODEL***

### *** Data Training Set ***




![png](milst4_files/milst4_148_0.png)





Best depth: 18



### *** World Cup 2018 - Group  Phase games ***


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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Iran</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>France</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Tree of our own model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>5</td>
      <td>3</td>
      <td>7</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>2</td>
      <td>13</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>2</td>
      <td>1</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score for our own model with Decision Tree on test set is: 0.6041666666666666


### World Cup 2018 - Group Phase games


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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>France</td>
      <td>Argentina</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Russia</td>
      <td>Spain</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Croatia</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brazil</td>
      <td>Brazil</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Tree of our own model'



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
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Team 1</th>
      <td>3</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>6</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>


    Accuracy score for our own model with Decision Tree on test set is: 0.25



## LDA-Our model

###*** Training Data Set ***



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mexico</td>
      <td>Argentina</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Draw</td>
      <td>Nigeria</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Uruguay</td>
      <td>Colombia</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Poland</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Tree of our own model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>240</td>
      <td>146</td>
      <td>158</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>484</td>
      <td>1264</td>
      <td>342</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>262</td>
      <td>188</td>
      <td>480</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score for our own model with LDA  on train is: 0.5566778900112234


###*** World Cup 2018 - Group  Phase games ***



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>France</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Lda of our own model on test 1'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>2</td>
      <td>0</td>
      <td>9</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>6</td>
      <td>16</td>
      <td>8</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>1</td>
      <td>1</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score for our own model with LDA  on test set 1 is: 0.4791666666666667



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>France</td>
      <td>France</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Russia</td>
      <td>Spain</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Croatia</td>
      <td>Denmark</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brazil</td>
      <td>Brazil</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Tree of our own model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>0</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>0</td>
      <td>5</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>0</td>
      <td>2</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score for our own model with LDA  on test set 2 is: 0.4375


##  QDA

## train set



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mexico</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Draw</td>
      <td>Senegal</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Poland</td>
      <td>Poland</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Tree of our own model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>166</td>
      <td>216</td>
      <td>110</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>314</td>
      <td>718</td>
      <td>214</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>506</td>
      <td>664</td>
      <td>656</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score for our own model with QDA  on train is: 0.43209876543209874


## group phase- test1



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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Iran</td>
      <td>Iran</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Draw</td>
      <td>Portugal</td>
    </tr>
    <tr>
      <th>4</th>
      <td>France</td>
      <td>Australia</td>
    </tr>
  </tbody>
</table>
</div>



    'conf matrix of Tree of our own model'



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
      <th>Actual Draw</th>
      <th>Actual Team 1</th>
      <th>Actual Team 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Predicted Draw</th>
      <td>5</td>
      <td>3</td>
      <td>7</td>
    </tr>
    <tr>
      <th>Predicted Team 1</th>
      <td>2</td>
      <td>13</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Predicted Team 2</th>
      <td>2</td>
      <td>1</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>


    accuracy score for our own model with qda  on test set 1 is: 0.4583333333333333


## group phase-test2




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
      <th>Observed</th>
      <th>Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>France</td>
      <td>France</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uruguay</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Russia</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Croatia</td>
      <td>Denmark</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brazil</td>
      <td>Brazil</td>
    </tr>
  </tbody>
</table>
</div>



##*** Random FOREST  -  Second Model***

###*** Training Data set ***


    accuracy score for the random forest model: 0.6745230078563412


###*** World Cup 2018 - Group  Phase games ***



    accuracy score for the random forest Test 1 model: 0.5208333333333334



    accuracy score for the random forest Test 2 model: 0.1875


##*** Bagging - Second Model ***



    train bootstrapped table:



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
      <th>Bootstrap-Model_1</th>
      <th>Bootstrap-Model_2</th>
      <th>Bootstrap-Model_3</th>
      <th>Bootstrap-Model_4</th>
      <th>Bootstrap-Model_5</th>
      <th>Bootstrap-Model_6</th>
      <th>Bootstrap-Model_7</th>
      <th>Bootstrap-Model_8</th>
      <th>Bootstrap-Model_9</th>
      <th>Bootstrap-Model_10</th>
      <th>...</th>
      <th>Bootstrap-Model_36</th>
      <th>Bootstrap-Model_37</th>
      <th>Bootstrap-Model_38</th>
      <th>Bootstrap-Model_39</th>
      <th>Bootstrap-Model_40</th>
      <th>Bootstrap-Model_41</th>
      <th>Bootstrap-Model_42</th>
      <th>Bootstrap-Model_43</th>
      <th>Bootstrap-Model_44</th>
      <th>Bootstrap-Model_45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Training-Row_1</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Training-Row_2</th>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Training-Row_3</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Training-Row_4</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Training-Row_5</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>



    test1 bootstrapped table:



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
      <th>Bootstrap-Model_1</th>
      <th>Bootstrap-Model_2</th>
      <th>Bootstrap-Model_3</th>
      <th>Bootstrap-Model_4</th>
      <th>Bootstrap-Model_5</th>
      <th>Bootstrap-Model_6</th>
      <th>Bootstrap-Model_7</th>
      <th>Bootstrap-Model_8</th>
      <th>Bootstrap-Model_9</th>
      <th>Bootstrap-Model_10</th>
      <th>...</th>
      <th>Bootstrap-Model_36</th>
      <th>Bootstrap-Model_37</th>
      <th>Bootstrap-Model_38</th>
      <th>Bootstrap-Model_39</th>
      <th>Bootstrap-Model_40</th>
      <th>Bootstrap-Model_41</th>
      <th>Bootstrap-Model_42</th>
      <th>Bootstrap-Model_43</th>
      <th>Bootstrap-Model_44</th>
      <th>Bootstrap-Model_45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Test-Row_1</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Test-Row_2</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_3</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_4</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Test-Row_5</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>



    test2 bootstrapped table:



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
      <th>Bootstrap-Model_1</th>
      <th>Bootstrap-Model_2</th>
      <th>Bootstrap-Model_3</th>
      <th>Bootstrap-Model_4</th>
      <th>Bootstrap-Model_5</th>
      <th>Bootstrap-Model_6</th>
      <th>Bootstrap-Model_7</th>
      <th>Bootstrap-Model_8</th>
      <th>Bootstrap-Model_9</th>
      <th>Bootstrap-Model_10</th>
      <th>...</th>
      <th>Bootstrap-Model_36</th>
      <th>Bootstrap-Model_37</th>
      <th>Bootstrap-Model_38</th>
      <th>Bootstrap-Model_39</th>
      <th>Bootstrap-Model_40</th>
      <th>Bootstrap-Model_41</th>
      <th>Bootstrap-Model_42</th>
      <th>Bootstrap-Model_43</th>
      <th>Bootstrap-Model_44</th>
      <th>Bootstrap-Model_45</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Test-Row_1</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_2</th>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Test-Row_3</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Test-Row_4</th>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Test-Row_5</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 45 columns</p>
</div>




```

# get majority to generate combined predictions
y_hat_train_maj_om = (np.mean(bagging_train,axis=1)>.5).astype(int)
y_hat_test_maj1_om = (np.mean(bagging_test1,axis=1)>.5).astype(int)
y_hat_test_maj2_om = (np.mean(bagging_test2,axis=1)>.5).astype(int)


bagged_accuracy_train_om = accuracy_score(y, y_hat_train_maj_om)
bagged_accuracy_test1_om = accuracy_score(yt1, y_hat_test_maj1_om)
bagged_accuracy_test2_om = accuracy_score(yt2, y_hat_test_maj2_om)


print("bagging model, training set accuracy:", bagged_accuracy_train_om)
print("bagging model, test-1 set accuracy:", bagged_accuracy_test1_om)
print("bagging model, test-2 set accuracy:", bagged_accuracy_test2_om)

```


    bagging model, training set accuracy: 0.5359147025813692
    bagging model, test-1 set accuracy: 0.4375
    bagging model, test-2 set accuracy: 0.4375


## Boosting-Second  model



```
n_estimators = 800
learning_rate = 0.05

x,y = prepare_data(x_train,y_train,result2_std)
ab = AdaBoostClassifier(base_estimator=DecisionTreeClassifier(max_depth=3),
                            n_estimators=n_estimators, learning_rate=learning_rate)
ab.fit(x, y)
xt,yt = prepare_data(x_test1,y_test1,result2_std)
xt2,yt2 = prepare_data(x_test2,y_test2,result2_std)
ab_scores_train = list(ab.staged_score(x, y))
ab_scores_test = list(ab.staged_score(xt, yt))
ab_scores_test2 = list(ab.staged_score(xt2, yt2))

x_plot = range(1, n_estimators+1) # just so we plot x-values of 1-800 instead of 0-799

plt.plot(x_plot, ab_scores_train, label='Training Set')
plt.plot(x_plot, ab_scores_test, label='Test Set1')
plt.plot(x_plot, ab_scores_test2, label='Test Set2')
plt.title('Adaboost Model: Accuracy Score by Number of Estimators/Iterations')
plt.xlabel('Number of Estimators/Iterations')
plt.ylabel('Accuracy')
plt.legend();
```



![png](milst4_files/milst4_180_0.png)




```
# List of lists of the staged predictions
staged_scores_train = []
staged_scores_test = []
staged_scores_test2 = []

for depth in range(1,5):
  ab = AdaBoostClassifier(base_estimator=DecisionTreeClassifier(max_depth=depth),
                            n_estimators=n_estimators, learning_rate=learning_rate)
  ab.fit(x, y)

  ab_scores_train = list(ab.staged_score(x, y))
  ab_scores_test = list(ab.staged_score(xt, yt))
  ab_scores_test2 = list(ab.staged_score(xt2, yt2))

  staged_scores_train.append(ab_scores_train)
  staged_scores_test.append(ab_scores_test)
  staged_scores_test2.append(ab_scores_test2)

```




```
fig, axs = plt.subplots(2,2,figsize=(10,8), sharey=True)

for i, ax in enumerate(axs.ravel()):
  ax.plot(x_plot, staged_scores_train[i], label='Training Set')
  ax.plot(x_plot, staged_scores_test[i], label='Test Set 1')
  ax.plot(x_plot, staged_scores_test2[i], label='Test Set 2')
  ax.set_title('Depth = {}'.format(i+1))
  ax.set_xlabel('N_estimators')
  ax.set_ylabel('Accuracy Score')
  ax.legend();

plt.tight_layout()
plt.suptitle('Adaboost Models: Accuracy Score for Models with Different Base Tree Depths, \
by Number of Estimators/Iterations', x=.5, y=1.02);
```



![png](milst4_files/milst4_182_0.png)




```
staged_scores_train_array = np.array(staged_scores_train)

for i, depth_scores in enumerate(staged_scores_train_array):
  depth = i+1
  idx = (np.argmax(depth_scores))
  print("Depth = {}: best train set accuracy at {} estimators.".format(depth, idx+1))
  print("train set accuracy score: {}\n".format(depth_scores[idx]))
```


    Depth = 1: best train set accuracy at 766 estimators.
    train set accuracy score: 0.5420875420875421

    Depth = 2: best train set accuracy at 782 estimators.
    train set accuracy score: 0.6167227833894501

    Depth = 3: best train set accuracy at 748 estimators.
    train set accuracy score: 0.6655443322109988

    Depth = 4: best train set accuracy at 440 estimators.
    train set accuracy score: 0.6689113355780022





```
staged_scores_test1_array = np.array(staged_scores_test1)

for i, depth_scores in enumerate(staged_scores_test1_array):
  depth = i+1
  idx = (np.argmax(depth_scores))
  print("Depth = {}: best train set accuracy at {} estimators.".format(depth, idx+1))
  print("train set accuracy score: {}\n".format(depth_scores[idx]))
```


    Depth = 1: best train set accuracy at 1 estimators.
    train set accuracy score: 0.6041666666666666

    Depth = 2: best train set accuracy at 342 estimators.
    train set accuracy score: 0.6041666666666666

    Depth = 3: best train set accuracy at 356 estimators.
    train set accuracy score: 0.6041666666666666

    Depth = 4: best train set accuracy at 673 estimators.
    train set accuracy score: 0.6666666666666666





```
staged_scores_test2_array = np.array(staged_scores_train)

for i, depth_scores in enumerate(staged_scores_test2_array):
  depth = i+1
  idx = (np.argmax(depth_scores))
  print("Depth = {}: best train set accuracy at {} estimators.".format(depth, idx+1))
  print("train set accuracy score: {}\n".format(depth_scores[idx]))
```


    Depth = 1: best train set accuracy at 766 estimators.
    train set accuracy score: 0.5420875420875421

    Depth = 2: best train set accuracy at 782 estimators.
    train set accuracy score: 0.6167227833894501

    Depth = 3: best train set accuracy at 748 estimators.
    train set accuracy score: 0.6655443322109988

    Depth = 4: best train set accuracy at 440 estimators.
    train set accuracy score: 0.6689113355780022



##*** Neural Networks - Second Model ***

###** Training Data Set  **



```
x,y = prepare_data(x_train,y_train,result2_std)
y_nn = []
for value in y:
  if value == 0:
    y_nn.append([1,0,0])
  elif value == 1:
    y_nn.append([0,1,0])
  else:
    y_nn.append([0,0,1])

y_nn = np.array(y_nn)

model_nn_om = Sequential([
    Dense(200, input_shape=(x.shape[1],), activation='relu'),
    # Dense(100, activation='relu'),
    # Dense(50, activation='relu'),
    Dropout(0.2),
    Dense(3, activation='linear')
])

#model_nn_om.add(Dropout(0.2))
model_nn_om.compile(loss='mean_absolute_error',
              optimizer='adam',metrics=['acc'])

model_nn_om.summary()

model_nn_om.fit(x, y_nn,
          epochs=10*32,
          batch_size=32,
          validation_split=.2,
          verbose=False)
```


    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    dense_35 (Dense)             (None, 200)               27800     
    _________________________________________________________________
    dropout_19 (Dropout)         (None, 200)               0         
    _________________________________________________________________
    dense_36 (Dense)             (None, 3)                 603       
    =================================================================
    Total params: 28,403
    Trainable params: 28,403
    Non-trainable params: 0
    _________________________________________________________________





    <keras.callbacks.History at 0x7f271de569b0>





```
model_nn_om.evaluate(x, y_nn, verbose=False)[1]
```





    0.6341189675191972



###*** World Cup 2018 - Group  Phase games ***



```
x,y = prepare_data(x_test1,y_test1,result2_std)
y_nn = []
for value in y:
  if value == 0:
    y_nn.append([1,0,0])
  elif value == 1:
    y_nn.append([0,1,0])
  else:
    y_nn.append([0,0,1])

y_nn = np.array(y_nn)

ypred_proba_nn = model_nn_om.predict(x)

ypred_nn = []
for value in ypred_proba_nn:
  if value[0] == 1:
    ypred_nn.append(0)
  elif value[1] == 1:
    ypred_nn.append(1)
  else:
    ypred_nn.append(2)

ypred_nn = np.array(ypred_nn)

print(accuracy_score(ypred_nn,y))

```


    0.4583333333333333


## World Cup 2018 -knockout phase



```
x,y = prepare_data(x_test2,y_test2,result2_std)
y_nn = []
for value in y:
  if value == 0:
    y_nn.append([1,0,0])
  elif value == 1:
    y_nn.append([0,1,0])
  else:
    y_nn.append([0,0,1])

y_nn = np.array(y_nn)

ypred_proba_nn = model_nn_om.predict(x)

ypred_nn = []
for value in ypred_proba_nn:
  if value[0] == 1:
    ypred_nn.append(0)
  elif value[1] == 1:
    ypred_nn.append(1)
  else:
    ypred_nn.append(2)

ypred_nn = np.array(ypred_nn)

print(accuracy_score(ypred_nn,y))

```


    0.4375


#*** Model Comparison on Accuracies - Second Models***

In this section:
- We compared the Training and Validation Accuraries of all the models tried in both with FIFA Ranking and non-FIFA Rankings



```
models = [reg2, tree_clf_om,qda_om, KNN2,rfml_om]
labels = ['Logistic Regression' ,'Decision Tree','qda','KNN','Random Forest']
x,y = prepare_data(x_train,y_train,result2_std)
cv_scores = []
for model, label in zip(models, labels):
  cv_score = cross_val_score(model, x, y, scoring='accuracy', cv=7).mean()
  cv_scores.append({'Model': label,
                        'CV_Accuracy': cv_score})

cv_scores.append({'Model': 'Bagging',
                        'CV_Accuracy': bagged_accuracy_train_om})
scores_df = pd.DataFrame(cv_scores, columns=['Model', 'CV_Accuracy'])

scores_df.sort_values(by='CV_Accuracy', ascending=False, inplace=True)
scores_df.reset_index(drop=True, inplace=True)
scores_df.index +=1 # So the indices will be model rankings by score


print("Mean 5-Fold CV Accuracy Score by Model:")

scores_df
```


    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")
    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")
    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")
    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")
    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")
    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")
    /usr/local/lib/python3.6/dist-packages/sklearn/discriminant_analysis.py:682: UserWarning: Variables are collinear
      warnings.warn("Variables are collinear")


    Mean 5-Fold CV Accuracy Score by Model:





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
      <td>0.597928</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Decision Tree</td>
      <td>0.589507</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Bagging</td>
      <td>0.536476</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Logistic Regression</td>
      <td>0.532831</td>
    </tr>
    <tr>
      <th>5</th>
      <td>KNN</td>
      <td>0.531141</td>
    </tr>
    <tr>
      <th>6</th>
      <td>qda</td>
      <td>0.440778</td>
    </tr>
  </tbody>
</table>
</div>



# Results and comparison between baseline model and Second Model

Our analysis of the training set show that random forest is the best model to use for the test set for both the baseline model and the model we developed ourselves

### Baseline Model Test Set Results



```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_train,y_train,result3_std)
rfml = RandomForestClassifier(n_estimators=50, oob_score=True, max_depth=10,random_state=1234)
rfml.fit(x, y)
ypredml2=rfml.predict(x)
print("accuracy score for the random forest model:",accuracy_score(ypredml2,y))

xt1,yt1 = prepare_data(x_test1,y_test1,result3_std)
ypredml2_test1_bl=rfml.predict(xt1)
print("accuracy score for the random forest model:",accuracy_score(ypredml2_test1_bl,yt1))

xt2,yt2 = prepare_data(x_test2,y_test2,result3_std)
ypredml2_test2_bl=knockout_test(rfml,xt2)
print("accuracy score for the random forest model:",accuracy_score(ypredml2_test2_bl,yt2))

```


    accuracy score for the random forest model: 0.6728395061728395
    accuracy score for the random forest model: 0.6666666666666666
    accuracy score for the random forest model: 0.1875




```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_train,y_train,result2_std)
rfml = RandomForestClassifier(n_estimators=50, oob_score=True, max_depth=10,random_state=1234)
rfml.fit(x, y)
ypredml2=rfml.predict(x)
print("accuracy score for the random forest of our model on the train set :",accuracy_score(ypredml2,y))
xt1,yt1 = prepare_data(x_test1,y_test1,result2_std)
ypredml2_test1=rfml.predict(xt1)
print("accuracy score for the random forest model:",accuracy_score(ypredml2_test1,yt1))

xt2,yt2 = prepare_data(x_test2,y_test2,result2_std)
ypredml2_test2=knockout_test(rfml,xt2)
print("accuracy score for the random forest model:",accuracy_score(ypredml2_test2,yt2))


```


    accuracy score for the random forest of our model on the train set : 0.6717171717171717
    accuracy score for the random forest model: 0.5625
    accuracy score for the random forest model: 0.25


#EDA of our own model data

This section represents all the Explanatory Data Analysis that was done with all the data used on this project



```
result2_std.boxplot(column=['Age','# won','Wage','Overall'],figsize=(10,8),
                       grid=True)
```





    <matplotlib.axes._subplots.AxesSubplot at 0x7f271de56860>




![png](milst4_files/milst4_202_1.png)




```
# show players Overall distribution
plt.hist(df1.Potential, bins=10, alpha=0.4,normed=True, color='b')
plt.title("#Players Potential distribution")
plt.xlabel("Potential")
plt.ylabel("Count")
overall_mean = df1_clean.Overall.mean()
overall_std = df1_clean.Overall.std()
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100)
p = norm.pdf(x, overall_mean, overall_std)
plt.plot(x, p, 'k', linewidth=2, color='r')
title = "Player's Potential,  mean = %.2f,  std = %.2f" % (overall_mean, overall_std)
plt.title(title)

plt.show()



# show players Overall distribution
plt.hist(df1_clean.Potential, bins=10, alpha=0.4,normed=True, color='b')
plt.title("#Players Overall Rating distribution")
plt.xlabel("Overall")
plt.ylabel("Count")
potential_mean = df1_clean.Potential.mean()
potential_std = df1_clean.Potential.std()
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100)
p = norm.pdf(x, potential_mean, potential_std)
plt.plot(x, p, 'k', linewidth=2, color='r')
title = "Player's Overall Rating,  mean = %.2f,  std = %.2f" % (potential_mean, potential_std)
plt.title(title)

plt.show()
```



![png](milst4_files/milst4_203_0.png)



![png](milst4_files/milst4_203_1.png)




```
age=df1['Age']
Nationality=df1['Nationality']
playerval=df1.groupby('Club')['Value']
playernationality=df1.groupby('Nationality')['Age']


# n = pd.DataFrame(list(zip(df1['Value'],df1['Club'])))
age_high = result2.Age.sort_values()[:10]
age_low = result2.Age.sort_values()[-10:]

pot_high = result2.Potential.sort_values()[:10]
pot_low = result2.Potential.sort_values()[-10:]

wage_high = result2.Wage.sort_values()[:10]
wage_low = result2.Wage.sort_values()[-10:]

value_high = result2.Value.sort_values()[:10]
value_low = result2.Value.sort_values()[-10:]


plt.figure(figsize = [18,9])
plt.subplot(2,2,1)
agedist=plt.barh(age_high.index,age_high,align='center')
agedist=plt.barh(age_low.index,age_low,align='center')
plt.xlabel('Age')
plt.ylabel('Country')
plt.subplot(2,2,2)
agedist=plt.barh(pot_high.index,pot_high,align='center')
agedist=plt.barh(pot_low.index,pot_low,align='center')
plt.xlabel('Potential')
plt.ylabel('Country')
plt.subplot(2,2,3)
agedist=plt.barh(wage_high.index,wage_high,align='center')
agedist=plt.barh(wage_low.index,wage_low,align='center')
plt.xlabel('Wage')
plt.ylabel('Country')
plt.subplot(2,2,4)
agedist=plt.barh(value_high.index,value_high,align='center')
agedist=plt.barh(value_low.index,value_low,align='center')
plt.xlabel('Value')
plt.ylabel('Country')



```





    Text(0,0.5,'Country')




![png](milst4_files/milst4_204_1.png)




```
plt.figure(figsize = [21,15])
plt.subplot(1,3,1)
plt.title('Logisitic Feature Importance for Draw')
plt.barh(result2_std.columns,reg2.coef_[0,:69],color='r')
plt.barh(result2_std.columns,reg2.coef_[0,69:],color='b')
plt.legend(['Home Team','Away Team'])
plt.subplot(1,3,2)
plt.title('Logisitic Feature Importance for Home Team Win')
plt.barh(result2_std.columns,reg2.coef_[1,:69],color='r')
plt.barh(result2_std.columns,reg2.coef_[1,69:],color='b')
plt.legend(['Home Team','Away Team'])
plt.subplot(1,3,3)
plt.title('Logisitic Feature Importance for Away Team Win')
plt.barh(result2_std.columns,reg2.coef_[2,:69],color='r')
plt.barh(result2_std.columns,reg2.coef_[2,69:],color='b')
plt.legend(['Home Team','Away Team'])

```





    <matplotlib.legend.Legend at 0x7f271d20eb00>




![png](milst4_files/milst4_205_1.png)
