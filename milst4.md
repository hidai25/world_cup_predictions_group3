
# <img style="float: left; padding-right: 10px; width: 45px" src="https://raw.githubusercontent.com/Harvard-IACS/2018-CS109A/master/content/styles/iacs.png"> CS109A Introduction to Data Science: 


## Final Project :  World Cup 2018 Predictions 


**Harvard University**<br/>
**Fall 2018**<br/>
**Instructors**: Pavlos Protopapas, Kevin Rader<br/>
**Group 3**: Hidai Bar-Mor, Anitha L. Srinivasan, Yves Tegaboue<br/>


<hr style="height:2pt">



#World cup 2018 prediction-Exploratory Data Analysis





```
#RUN THIS CELL 
import requests
from IPython.core.display import HTML
styles = requests.get("https://raw.githubusercontent.com/Harvard-IACS/2018-CS109A/master/content/styles/cs109.css").text
HTML(styles)
```





<style>
blockquote { background: #AEDE94; }
h1 { 
    padding-top: 25px;
    padding-bottom: 25px;
    text-align: left; 
    padding-left: 10px;
    background-color: #DDDDDD; 
    color: black;
}
h2 { 
    padding-top: 10px;
    padding-bottom: 10px;
    text-align: left; 
    padding-left: 5px;
    background-color: #EEEEEE; 
    color: black;
}

div.exercise {
	background-color: #ffcccc;
	border-color: #E9967A; 	
	border-left: 5px solid #800080; 
	padding: 0.5em;
}
div.theme {
	background-color: #DDDDDD;
	border-color: #E9967A; 	
	border-left: 5px solid #800080; 
	padding: 0.5em;
	font-size: 18pt;
}
div.gc { 
	background-color: #AEDE94;
	border-color: #E9967A; 	 
	border-left: 5px solid #800080; 
	padding: 0.5em;
	font-size: 12pt;
}
p.q1 { 
    padding-top: 5px;
    padding-bottom: 5px;
    text-align: left; 
    padding-left: 5px;
    background-color: #EEEEEE; 
    color: black;
}
header {
   padding-top: 35px;
    padding-bottom: 35px;
    text-align: left; 
    padding-left: 10px;
    background-color: #DDDDDD; 
    color: black;
}
</style>







```
# load different libraries useful for our work

import numpy as np
import pandas as pd
import statsmodels.api as sm
import requests
import math
from statsmodels.api import OLS
from sklearn.decomposition import PCA
from sklearn.linear_model import LogisticRegression
from sklearn.linear_model import LogisticRegressionCV
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis
from sklearn.discriminant_analysis import QuadraticDiscriminantAnalysis
from sklearn.preprocessing import PolynomialFeatures
from sklearn.neighbors import KNeighborsClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score
from sklearn.metrics import accuracy_score
from sklearn.model_selection import KFold
from sklearn.preprocessing import MinMaxScaler
from sklearn.ensemble import AdaBoostClassifier
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import confusion_matrix
from sklearn.model_selection import train_test_split
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import LeaveOneOut
from sklearn.model_selection import KFold
from sklearn.pipeline import make_pipeline
from sklearn.datasets import make_blobs
from io import BytesIO
from pandas.plotting import scatter_matrix
from sklearn.discriminant_analysis import QuadraticDiscriminantAnalysis
from sklearn.neural_network import MLPClassifier
from scipy.special import gamma
from bs4 import BeautifulSoup
import bs4 as bs
import matplotlib
import matplotlib.pyplot as plt
%matplotlib inline
import datetime
from dateutil import relativedelta as rdelta
import seaborn as sns
sns.set()
from IPython.display import display
from scipy.stats import norm
from keras.models import Sequential
from keras.layers import Dense, Activation, Dropout, Flatten, Reshape
from keras.optimizers import SGD
```


    /usr/local/lib/python3.6/dist-packages/statsmodels/compat/pandas.py:56: FutureWarning: The pandas.core.datetools module is deprecated and will be removed in a future version. Please use the pandas.tseries module instead.
      from pandas.core import datetools
    Using TensorFlow backend.


##List of National Teams qualifed to the FIFA World Cup 2018 and List of players<a id ='Teamdanalysis'></a> 

In this section, we scrape the list of the 32 National Teams participating in the FIFA World Cup 2018 and the list of the 23 players belonging to each team 



```
# https://en.wikipedia.org/wiki/2018_FIFA_World_Cup_squads
# scrape the squad of every nation selected to the world cup with their data and get a list of all the countries from wikipedia

my_page=requests.get("https://en.wikipedia.org/wiki/2018_FIFA_World_Cup_squads")

#Page for Champions leage Team 2017 - 2018
champions_league_page = requests.get("https://en.wikipedia.org/wiki/2017%E2%80%9318_UEFA_Champions_League_group_stage")

squad_soup = BeautifulSoup(my_page.text, "html.parser")
CL_soup = BeautifulSoup(champions_league_page.text, "html.parser") # Parse the champions league page

#GET the list of teams for CHampions LEague 2017-2018
CL_list = []
#find top_node of the teams
top_node = CL_soup.find("th", attrs={"width": "200"})

next_link = top_node.find_next('a')  # get the first link in the node
for i in range(78):  # getting through the next 78 links 
    next_link = next_link.find_next('a')  # retrieve the next link
    CL_team = next_link.text.strip('\n')  # Strip all junk character
    if CL_team and '[' not in CL_team:  # Condition to make sure that the link is an actual team 
        CL_list.append(CL_team)  # Add the team to the champions league team 

        
cup_date = datetime.datetime(2018, 7, 1, 0, 0, 0)
country_tables = squad_soup.findAll("table", attrs={"class": "sortable wikitable plainrowheaders"})
starlist = []
countrylist = []
top_league_country = ['France', 'Italy', 'Germany', 'England', 'Spain']   #List of top 5 Soccer League in the World


for country_table in country_tables:
    players = country_table.findAll("th", attrs={"scope": "row"})
        
    for name in players:
        d = dict()
        d['name'] = name.find('a').text.strip('\n')
        pos = name.find_previous_sibling()
        d['pos'] = pos.find('a').text.strip('\n').strip('()')
        rawbday = name.find_next_sibling()
        bday = datetime.datetime.strptime(rawbday.find('span',attrs={'class':'bday'}).text.strip('\n'), '%Y-%m-%d')
        age = rdelta.relativedelta(cup_date,bday).years
        
        d['age'] = age
        caps = rawbday.find_next_sibling()
        d['caps'] = caps.text.strip('\n')
        goals = caps.find_next_sibling()
        d['goals'] = goals.text.strip('\n')
        country = goals.find_previous('h3').find('span').text.strip('\n')
        club = goals.find_next('a').find_next('a').text.strip('\n')   # Retrieve the club information of the player
        if caps.find_next('a')['title'] in top_league_country:  #Check if the Club is in Top5 leagues
            top_league = 1  # If yes, CLub in Top 5 league 
        else: 
            top_league = 0  # Club isn't in top 5 league
        d['country'] = country
        d['club'] = club  # Add the club Name of the player in the DataFrame
        d['Top League'] = top_league # Add the player in Top 5 League or Not. 
        
        if club in CL_list:  # Check if the club played champions league
            CL_league = 1
        else: 
            CL_league = 0
        d['CL League 2017-2018'] = CL_league # add Champions league 2017/2018 to the data frame
        starlist.append(d)

    countrylist.append(country)
print(starlist)
print(countrylist)


```


    [{'name': 'Essam El-Hadary', 'pos': 'GK', 'age': 45, 'caps': '158', 'goals': '0', 'country': 'Egypt', 'club': 'Al-Taawoun', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ali Gabr', 'pos': 'DF', 'age': 29, 'caps': '21', 'goals': '1', 'country': 'Egypt', 'club': 'West Bromwich Albion', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ahmed Elmohamady', 'pos': 'DF', 'age': 30, 'caps': '78', 'goals': '2', 'country': 'Egypt', 'club': 'Aston Villa', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Omar Gaber', 'pos': 'MF', 'age': 26, 'caps': '24', 'goals': '0', 'country': 'Egypt', 'club': 'Los Angeles FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Sam Morsy', 'pos': 'MF', 'age': 26, 'caps': '5', 'goals': '0', 'country': 'Egypt', 'club': 'Wigan Athletic', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ahmed Hegazi', 'pos': 'DF', 'age': 27, 'caps': '45', 'goals': '1', 'country': 'Egypt', 'club': 'West Bromwich Albion', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ahmed Fathy', 'pos': 'DF', 'age': 33, 'caps': '126', 'goals': '3', 'country': 'Egypt', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Tarek Hamed', 'pos': 'MF', 'age': 29, 'caps': '21', 'goals': '0', 'country': 'Egypt', 'club': 'Zamalek', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Marwan Mohsen', 'pos': 'FW', 'age': 29, 'caps': '24', 'goals': '4', 'country': 'Egypt', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohamed Salah', 'pos': 'FW', 'age': 26, 'caps': '57', 'goals': '33', 'country': 'Egypt', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Kahraba', 'pos': 'FW', 'age': 24, 'caps': '19', 'goals': '3', 'country': 'Egypt', 'club': 'Al-Ittihad', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ayman Ashraf', 'pos': 'DF', 'age': 27, 'caps': '4', 'goals': '0', 'country': 'Egypt', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohamed Abdel-Shafy', 'pos': 'DF', 'age': 33, 'caps': '51', 'goals': '1', 'country': 'Egypt', 'club': 'Al-Fateh', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ramadan Sobhi', 'pos': 'FW', 'age': 21, 'caps': '23', 'goals': '1', 'country': 'Egypt', 'club': 'Stoke City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mahmoud Hamdy', 'pos': 'DF', 'age': 23, 'caps': '0', 'goals': '0', 'country': 'Egypt', 'club': 'Zamalek', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Sherif Ekramy', 'pos': 'GK', 'age': 34, 'caps': '22', 'goals': '0', 'country': 'Egypt', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohamed Elneny', 'pos': 'MF', 'age': 25, 'caps': '61', 'goals': '5', 'country': 'Egypt', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Shikabala', 'pos': 'FW', 'age': 32, 'caps': '29', 'goals': '2', 'country': 'Egypt', 'club': 'Al-Raed', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Abdallah Said', 'pos': 'MF', 'age': 32, 'caps': '36', 'goals': '6', 'country': 'Egypt', 'club': 'KuPS', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Saad Samir', 'pos': 'DF', 'age': 29, 'caps': '11', 'goals': '0', 'country': 'Egypt', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Trézéguet', 'pos': 'MF', 'age': 23, 'caps': '24', 'goals': '2', 'country': 'Egypt', 'club': 'Kasımpaşa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Amr Warda', 'pos': 'FW', 'age': 24, 'caps': '16', 'goals': '0', 'country': 'Egypt', 'club': 'Atromitos', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohamed El-Shenawy', 'pos': 'GK', 'age': 29, 'caps': '3', 'goals': '0', 'country': 'Egypt', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Igor Akinfeev', 'pos': 'GK', 'age': 32, 'caps': '106', 'goals': '0', 'country': 'Russia', 'club': 'CSKA Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Mário Fernandes', 'pos': 'DF', 'age': 27, 'caps': '5', 'goals': '0', 'country': 'Russia', 'club': 'CSKA Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Ilya Kutepov', 'pos': 'DF', 'age': 24, 'caps': '7', 'goals': '0', 'country': 'Russia', 'club': 'Spartak Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Sergei Ignashevich', 'pos': 'DF', 'age': 38, 'caps': '122', 'goals': '8', 'country': 'Russia', 'club': 'CSKA Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Andrei Semyonov', 'pos': 'DF', 'age': 29, 'caps': '6', 'goals': '0', 'country': 'Russia', 'club': 'Akhmat Grozny', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Denis Cheryshev', 'pos': 'MF', 'age': 27, 'caps': '11', 'goals': '0', 'country': 'Russia', 'club': 'Villarreal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Daler Kuzyayev', 'pos': 'MF', 'age': 25, 'caps': '6', 'goals': '0', 'country': 'Russia', 'club': 'Zenit Saint Petersburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yury Gazinsky', 'pos': 'MF', 'age': 28, 'caps': '6', 'goals': '0', 'country': 'Russia', 'club': 'Krasnodar', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alan Dzagoev', 'pos': 'MF', 'age': 28, 'caps': '57', 'goals': '9', 'country': 'Russia', 'club': 'CSKA Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Fyodor Smolov', 'pos': 'FW', 'age': 28, 'caps': '32', 'goals': '12', 'country': 'Russia', 'club': 'Krasnodar', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Roman Zobnin', 'pos': 'MF', 'age': 24, 'caps': '12', 'goals': '0', 'country': 'Russia', 'club': 'Spartak Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Andrey Lunyov', 'pos': 'GK', 'age': 26, 'caps': '3', 'goals': '0', 'country': 'Russia', 'club': 'Zenit Saint Petersburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Fyodor Kudryashov', 'pos': 'DF', 'age': 31, 'caps': '19', 'goals': '0', 'country': 'Russia', 'club': 'Rubin Kazan', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Vladimir Granat', 'pos': 'DF', 'age': 31, 'caps': '12', 'goals': '1', 'country': 'Russia', 'club': 'Rubin Kazan', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aleksei Miranchuk', 'pos': 'MF', 'age': 22, 'caps': '18', 'goals': '4', 'country': 'Russia', 'club': 'Lokomotiv Moscow', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Anton Miranchuk', 'pos': 'MF', 'age': 22, 'caps': '6', 'goals': '0', 'country': 'Russia', 'club': 'Lokomotiv Moscow', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aleksandr Golovin', 'pos': 'MF', 'age': 22, 'caps': '19', 'goals': '2', 'country': 'Russia', 'club': 'CSKA Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Yuri Zhirkov', 'pos': 'MF', 'age': 34, 'caps': '84', 'goals': '2', 'country': 'Russia', 'club': 'Zenit Saint Petersburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aleksandr Samedov', 'pos': 'MF', 'age': 33, 'caps': '48', 'goals': '7', 'country': 'Russia', 'club': 'Spartak Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Vladimir Gabulov', 'pos': 'GK', 'age': 34, 'caps': '10', 'goals': '0', 'country': 'Russia', 'club': 'Club Brugge', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aleksandr Yerokhin', 'pos': 'MF', 'age': 28, 'caps': '17', 'goals': '0', 'country': 'Russia', 'club': 'Zenit Saint Petersburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Artem Dzyuba', 'pos': 'FW', 'age': 29, 'caps': '23', 'goals': '11', 'country': 'Russia', 'club': 'Arsenal Tula', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Igor Smolnikov', 'pos': 'DF', 'age': 29, 'caps': '27', 'goals': '0', 'country': 'Russia', 'club': 'Zenit Saint Petersburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Abdullah Al-Mayouf', 'pos': 'GK', 'age': 31, 'caps': '11', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mansoor Al-Harbi', 'pos': 'DF', 'age': 30, 'caps': '40', 'goals': '1', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Osama Hawsawi', 'pos': 'DF', 'age': 34, 'caps': '135', 'goals': '7', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ali Al-Bulaihi', 'pos': 'DF', 'age': 28, 'caps': '4', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Omar Hawsawi', 'pos': 'DF', 'age': 32, 'caps': '42', 'goals': '3', 'country': 'Saudi Arabia', 'club': 'Al-Nassr', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohammed Al-Breik', 'pos': 'DF', 'age': 25, 'caps': '11', 'goals': '1', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Salman Al-Faraj', 'pos': 'MF', 'age': 28, 'caps': '43', 'goals': '3', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yahya Al-Shehri', 'pos': 'MF', 'age': 28, 'caps': '57', 'goals': '8', 'country': 'Saudi Arabia', 'club': 'Leganés', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Hattan Bahebri', 'pos': 'MF', 'age': 25, 'caps': '5', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Shabab', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohammad Al-Sahlawi', 'pos': 'FW', 'age': 31, 'caps': '40', 'goals': '28', 'country': 'Saudi Arabia', 'club': 'Al-Nassr', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Abdulmalek Al-Khaibri', 'pos': 'MF', 'age': 32, 'caps': '36', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohamed Kanno', 'pos': 'MF', 'age': 23, 'caps': '6', 'goals': '1', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yasser Al-Shahrani', 'pos': 'DF', 'age': 26, 'caps': '37', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Abdullah Otayf', 'pos': 'MF', 'age': 25, 'caps': '16', 'goals': '1', 'country': 'Saudi Arabia', 'club': 'Al-Hilal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Abdullah Al-Khaibari', 'pos': 'MF', 'age': 21, 'caps': '5', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Shabab', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Housain Al-Mogahwi', 'pos': 'MF', 'age': 30, 'caps': '18', 'goals': '1', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Taisir Al-Jassim', 'pos': 'MF', 'age': 33, 'caps': '132', 'goals': '19', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Salem Al-Dawsari', 'pos': 'MF', 'age': 26, 'caps': '33', 'goals': '4', 'country': 'Saudi Arabia', 'club': 'Villarreal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Fahad Al-Muwallad', 'pos': 'FW', 'age': 23, 'caps': '45', 'goals': '10', 'country': 'Saudi Arabia', 'club': 'Levante', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Muhannad Assiri', 'pos': 'FW', 'age': 31, 'caps': '18', 'goals': '4', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yasser Al-Mosailem', 'pos': 'GK', 'age': 34, 'caps': '32', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohammed Al-Owais', 'pos': 'GK', 'age': 26, 'caps': '6', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Motaz Hawsawi', 'pos': 'DF', 'age': 26, 'caps': '17', 'goals': '0', 'country': 'Saudi Arabia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Fernando Muslera', 'pos': 'GK', 'age': 32, 'caps': '97', 'goals': '0', 'country': 'Uruguay', 'club': 'Galatasaray', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'José Giménez', 'pos': 'DF', 'age': 23, 'caps': '42', 'goals': '5', 'country': 'Uruguay', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Diego Godín', 'pos': 'DF', 'age': 32, 'caps': '116', 'goals': '8', 'country': 'Uruguay', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Guillermo Varela', 'pos': 'DF', 'age': 25, 'caps': '3', 'goals': '0', 'country': 'Uruguay', 'club': 'Peñarol', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Carlos Sánchez', 'pos': 'MF', 'age': 33, 'caps': '36', 'goals': '1', 'country': 'Uruguay', 'club': 'Monterrey', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Rodrigo Bentancur', 'pos': 'MF', 'age': 21, 'caps': '7', 'goals': '0', 'country': 'Uruguay', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Cristian Rodríguez', 'pos': 'MF', 'age': 32, 'caps': '105', 'goals': '11', 'country': 'Uruguay', 'club': 'Peñarol', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Nahitan Nández', 'pos': 'MF', 'age': 22, 'caps': '12', 'goals': '0', 'country': 'Uruguay', 'club': 'Boca Juniors', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Luis Suárez', 'pos': 'FW', 'age': 31, 'caps': '98', 'goals': '51', 'country': 'Uruguay', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Giorgian De Arrascaeta', 'pos': 'FW', 'age': 24, 'caps': '14', 'goals': '2', 'country': 'Uruguay', 'club': 'Cruzeiro', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Cristhian Stuani', 'pos': 'FW', 'age': 31, 'caps': '41', 'goals': '5', 'country': 'Uruguay', 'club': 'Girona', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Martín Campaña', 'pos': 'GK', 'age': 29, 'caps': '1', 'goals': '0', 'country': 'Uruguay', 'club': 'Independiente', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gastón Silva', 'pos': 'DF', 'age': 24, 'caps': '17', 'goals': '0', 'country': 'Uruguay', 'club': 'Independiente', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Lucas Torreira', 'pos': 'MF', 'age': 22, 'caps': '3', 'goals': '0', 'country': 'Uruguay', 'club': 'Sampdoria', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Matías Vecino', 'pos': 'MF', 'age': 26, 'caps': '22', 'goals': '1', 'country': 'Uruguay', 'club': 'Inter Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Maxi Pereira', 'pos': 'DF', 'age': 34, 'caps': '125', 'goals': '3', 'country': 'Uruguay', 'club': 'Porto', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Diego Laxalt', 'pos': 'MF', 'age': 25, 'caps': '6', 'goals': '0', 'country': 'Uruguay', 'club': 'Genoa', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Maxi Gómez', 'pos': 'FW', 'age': 21, 'caps': '5', 'goals': '0', 'country': 'Uruguay', 'club': 'Celta Vigo', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Sebastián Coates', 'pos': 'DF', 'age': 27, 'caps': '30', 'goals': '1', 'country': 'Uruguay', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Jonathan Urretaviscaya', 'pos': 'FW', 'age': 28, 'caps': '4', 'goals': '0', 'country': 'Uruguay', 'club': 'Monterrey', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Edinson Cavani', 'pos': 'FW', 'age': 31, 'caps': '101', 'goals': '42', 'country': 'Uruguay', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Martín Cáceres', 'pos': 'DF', 'age': 31, 'caps': '76', 'goals': '4', 'country': 'Uruguay', 'club': 'Lazio', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Martín Silva', 'pos': 'GK', 'age': 35, 'caps': '11', 'goals': '0', 'country': 'Uruguay', 'club': 'Vasco da Gama', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alireza Beiranvand', 'pos': 'GK', 'age': 25, 'caps': '22', 'goals': '0', 'country': 'Iran', 'club': 'Persepolis', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mehdi Torabi', 'pos': 'MF', 'age': 23, 'caps': '17', 'goals': '4', 'country': 'Iran', 'club': 'Saipa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ehsan Hajsafi', 'pos': 'DF', 'age': 28, 'caps': '94', 'goals': '6', 'country': 'Iran', 'club': 'Olympiacos', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Rouzbeh Cheshmi', 'pos': 'DF', 'age': 24, 'caps': '10', 'goals': '1', 'country': 'Iran', 'club': 'Esteghlal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Milad Mohammadi', 'pos': 'DF', 'age': 24, 'caps': '19', 'goals': '0', 'country': 'Iran', 'club': 'Akhmat Grozny', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Saeid Ezatolahi', 'pos': 'MF', 'age': 21, 'caps': '25', 'goals': '1', 'country': 'Iran', 'club': 'Amkar Perm', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Masoud Shojaei', 'pos': 'MF', 'age': 34, 'caps': '74', 'goals': '8', 'country': 'Iran', 'club': 'AEK Athens', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Morteza Pouraliganji', 'pos': 'DF', 'age': 26, 'caps': '27', 'goals': '2', 'country': 'Iran', 'club': 'Al-Sadd', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Omid Ebrahimi', 'pos': 'MF', 'age': 30, 'caps': '30', 'goals': '0', 'country': 'Iran', 'club': 'Esteghlal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Karim Ansarifard', 'pos': 'FW', 'age': 28, 'caps': '64', 'goals': '17', 'country': 'Iran', 'club': 'Olympiacos', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Vahid Amiri', 'pos': 'MF', 'age': 30, 'caps': '36', 'goals': '1', 'country': 'Iran', 'club': 'Persepolis', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohammad Rashid Mazaheri', 'pos': 'GK', 'age': 29, 'caps': '3', 'goals': '0', 'country': 'Iran', 'club': 'Zob Ahan', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohammad Reza Khanzadeh', 'pos': 'DF', 'age': 27, 'caps': '11', 'goals': '1', 'country': 'Iran', 'club': 'Padideh', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Saman Ghoddos', 'pos': 'FW', 'age': 24, 'caps': '8', 'goals': '1', 'country': 'Iran', 'club': 'Östersund', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Pejman Montazeri', 'pos': 'DF', 'age': 34, 'caps': '46', 'goals': '1', 'country': 'Iran', 'club': 'Esteghlal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Reza Ghoochannejhad', 'pos': 'FW', 'age': 30, 'caps': '43', 'goals': '17', 'country': 'Iran', 'club': 'Heerenveen', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mehdi Taremi', 'pos': 'FW', 'age': 25, 'caps': '26', 'goals': '11', 'country': 'Iran', 'club': 'Al-Gharafa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alireza Jahanbakhsh', 'pos': 'FW', 'age': 24, 'caps': '38', 'goals': '4', 'country': 'Iran', 'club': 'AZ', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Majid Hosseini', 'pos': 'DF', 'age': 22, 'caps': '1', 'goals': '0', 'country': 'Iran', 'club': 'Esteghlal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Sardar Azmoun', 'pos': 'FW', 'age': 23, 'caps': '33', 'goals': '23', 'country': 'Iran', 'club': 'Rubin Kazan', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ashkan Dejagah', 'pos': 'FW', 'age': 31, 'caps': '46', 'goals': '9', 'country': 'Iran', 'club': 'Nottingham Forest', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Amir Abedzadeh', 'pos': 'GK', 'age': 25, 'caps': '1', 'goals': '0', 'country': 'Iran', 'club': 'Marítimo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ramin Rezaeian', 'pos': 'DF', 'age': 28, 'caps': '28', 'goals': '2', 'country': 'Iran', 'club': 'Oostende', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yassine Bounou', 'pos': 'GK', 'age': 27, 'caps': '11', 'goals': '0', 'country': 'Morocco', 'club': 'Girona', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Achraf Hakimi', 'pos': 'DF', 'age': 19, 'caps': '10', 'goals': '1', 'country': 'Morocco', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Hamza Mendyl', 'pos': 'DF', 'age': 20, 'caps': '13', 'goals': '0', 'country': 'Morocco', 'club': 'Lille', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Manuel da Costa', 'pos': 'DF', 'age': 32, 'caps': '28', 'goals': '1', 'country': 'Morocco', 'club': 'İstanbul Başakşehir', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Medhi Benatia', 'pos': 'DF', 'age': 31, 'caps': '57', 'goals': '2', 'country': 'Morocco', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Romain Saïss', 'pos': 'DF', 'age': 28, 'caps': '24', 'goals': '1', 'country': 'Morocco', 'club': 'Wolverhampton Wanderers', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Hakim Ziyech', 'pos': 'MF', 'age': 25, 'caps': '18', 'goals': '9', 'country': 'Morocco', 'club': 'Ajax', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Karim El Ahmadi', 'pos': 'MF', 'age': 33, 'caps': '51', 'goals': '1', 'country': 'Morocco', 'club': 'Feyenoord', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Ayoub El Kaabi', 'pos': 'FW', 'age': 25, 'caps': '10', 'goals': '11', 'country': 'Morocco', 'club': 'RS Berkane', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Younès Belhanda', 'pos': 'MF', 'age': 28, 'caps': '47', 'goals': '5', 'country': 'Morocco', 'club': 'Galatasaray', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Fayçal Fajr', 'pos': 'MF', 'age': 29, 'caps': '23', 'goals': '2', 'country': 'Morocco', 'club': 'Getafe', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Munir Mohamedi', 'pos': 'GK', 'age': 29, 'caps': '27', 'goals': '0', 'country': 'Morocco', 'club': 'Numancia', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Khalid Boutaïb', 'pos': 'FW', 'age': 31, 'caps': '18', 'goals': '7', 'country': 'Morocco', 'club': 'Yeni Malatyaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mbark Boussoufa', 'pos': 'MF', 'age': 33, 'caps': '59', 'goals': '7', 'country': 'Morocco', 'club': 'Al-Jazira', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Youssef Aït Bennasser', 'pos': 'MF', 'age': 21, 'caps': '14', 'goals': '0', 'country': 'Morocco', 'club': 'Caen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nordin Amrabat', 'pos': 'MF', 'age': 31, 'caps': '44', 'goals': '4', 'country': 'Morocco', 'club': 'Leganés', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nabil Dirar', 'pos': 'DF', 'age': 32, 'caps': '34', 'goals': '3', 'country': 'Morocco', 'club': 'Fenerbahçe', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Amine Harit', 'pos': 'MF', 'age': 21, 'caps': '6', 'goals': '0', 'country': 'Morocco', 'club': 'Schalke 04', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Youssef En-Nesyri', 'pos': 'FW', 'age': 21, 'caps': '16', 'goals': '2', 'country': 'Morocco', 'club': 'Málaga', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Aziz Bouhaddouz', 'pos': 'FW', 'age': 31, 'caps': '15', 'goals': '3', 'country': 'Morocco', 'club': 'FC St. Pauli', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Sofyan Amrabat', 'pos': 'MF', 'age': 21, 'caps': '6', 'goals': '0', 'country': 'Morocco', 'club': 'Feyenoord', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Ahmed Reda Tagnaouti', 'pos': 'GK', 'age': 22, 'caps': '2', 'goals': '0', 'country': 'Morocco', 'club': 'IR Tanger', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mehdi Carcela', 'pos': 'MF', 'age': 29, 'caps': '20', 'goals': '1', 'country': 'Morocco', 'club': 'Standard Liège', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Rui Patrício', 'pos': 'GK', 'age': 30, 'caps': '69', 'goals': '0', 'country': 'Portugal', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Bruno Alves', 'pos': 'DF', 'age': 36, 'caps': '96', 'goals': '11', 'country': 'Portugal', 'club': 'Rangers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Pepe', 'pos': 'DF', 'age': 35, 'caps': '95', 'goals': '5', 'country': 'Portugal', 'club': 'Beşiktaş', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Manuel Fernandes', 'pos': 'MF', 'age': 32, 'caps': '14', 'goals': '3', 'country': 'Portugal', 'club': 'Lokomotiv Moscow', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Raphaël Guerreiro', 'pos': 'DF', 'age': 24, 'caps': '24', 'goals': '2', 'country': 'Portugal', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'José Fonte', 'pos': 'DF', 'age': 34, 'caps': '31', 'goals': '0', 'country': 'Portugal', 'club': 'Dalian Yifang', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Cristiano Ronaldo', 'pos': 'FW', 'age': 33, 'caps': '150', 'goals': '81', 'country': 'Portugal', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'João Moutinho', 'pos': 'MF', 'age': 31, 'caps': '110', 'goals': '7', 'country': 'Portugal', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'André Silva', 'pos': 'FW', 'age': 22, 'caps': '23', 'goals': '12', 'country': 'Portugal', 'club': 'Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'João Mário', 'pos': 'MF', 'age': 25, 'caps': '36', 'goals': '2', 'country': 'Portugal', 'club': 'West Ham United', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Bernardo Silva', 'pos': 'MF', 'age': 23, 'caps': '25', 'goals': '2', 'country': 'Portugal', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Anthony Lopes', 'pos': 'GK', 'age': 27, 'caps': '7', 'goals': '0', 'country': 'Portugal', 'club': 'Lyon', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Rúben Dias', 'pos': 'DF', 'age': 21, 'caps': '1', 'goals': '0', 'country': 'Portugal', 'club': 'Benfica', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'William Carvalho', 'pos': 'MF', 'age': 26, 'caps': '43', 'goals': '2', 'country': 'Portugal', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Ricardo Pereira', 'pos': 'DF', 'age': 24, 'caps': '4', 'goals': '0', 'country': 'Portugal', 'club': 'Porto', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Bruno Fernandes', 'pos': 'MF', 'age': 23, 'caps': '6', 'goals': '1', 'country': 'Portugal', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Gonçalo Guedes', 'pos': 'FW', 'age': 21, 'caps': '10', 'goals': '3', 'country': 'Portugal', 'club': 'Valencia', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Gelson Martins', 'pos': 'FW', 'age': 23, 'caps': '18', 'goals': '0', 'country': 'Portugal', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Mário Rui', 'pos': 'DF', 'age': 27, 'caps': '4', 'goals': '0', 'country': 'Portugal', 'club': 'Napoli', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ricardo Quaresma', 'pos': 'FW', 'age': 34, 'caps': '77', 'goals': '9', 'country': 'Portugal', 'club': 'Beşiktaş', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Cédric', 'pos': 'DF', 'age': 26, 'caps': '29', 'goals': '1', 'country': 'Portugal', 'club': 'Southampton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Beto', 'pos': 'GK', 'age': 36, 'caps': '14', 'goals': '0', 'country': 'Portugal', 'club': 'Göztepe', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Adrien Silva', 'pos': 'MF', 'age': 29, 'caps': '23', 'goals': '1', 'country': 'Portugal', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'David de Gea', 'pos': 'GK', 'age': 27, 'caps': '29', 'goals': '0', 'country': 'Spain', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Dani Carvajal', 'pos': 'DF', 'age': 26, 'caps': '15', 'goals': '0', 'country': 'Spain', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Gerard Piqué', 'pos': 'DF', 'age': 31, 'caps': '98', 'goals': '5', 'country': 'Spain', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Nacho', 'pos': 'DF', 'age': 28, 'caps': '17', 'goals': '0', 'country': 'Spain', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Sergio Busquets', 'pos': 'MF', 'age': 29, 'caps': '103', 'goals': '2', 'country': 'Spain', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Andrés Iniesta', 'pos': 'MF', 'age': 34, 'caps': '127', 'goals': '14', 'country': 'Spain', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Saúl', 'pos': 'MF', 'age': 23, 'caps': '10', 'goals': '0', 'country': 'Spain', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Koke', 'pos': 'MF', 'age': 26, 'caps': '40', 'goals': '0', 'country': 'Spain', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Rodrigo', 'pos': 'FW', 'age': 27, 'caps': '6', 'goals': '2', 'country': 'Spain', 'club': 'Valencia', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Thiago', 'pos': 'MF', 'age': 27, 'caps': '29', 'goals': '2', 'country': 'Spain', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Lucas Vázquez', 'pos': 'FW', 'age': 27, 'caps': '7', 'goals': '0', 'country': 'Spain', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Álvaro Odriozola', 'pos': 'DF', 'age': 22, 'caps': '4', 'goals': '1', 'country': 'Spain', 'club': 'Real Sociedad', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kepa Arrizabalaga', 'pos': 'GK', 'age': 23, 'caps': '1', 'goals': '0', 'country': 'Spain', 'club': 'Athletic Bilbao', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'César Azpilicueta', 'pos': 'DF', 'age': 28, 'caps': '22', 'goals': '0', 'country': 'Spain', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Sergio Ramos', 'pos': 'DF', 'age': 32, 'caps': '152', 'goals': '13', 'country': 'Spain', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Nacho Monreal', 'pos': 'DF', 'age': 32, 'caps': '22', 'goals': '1', 'country': 'Spain', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Iago Aspas', 'pos': 'FW', 'age': 30, 'caps': '10', 'goals': '5', 'country': 'Spain', 'club': 'Celta Vigo', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jordi Alba', 'pos': 'DF', 'age': 29, 'caps': '62', 'goals': '8', 'country': 'Spain', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Diego Costa', 'pos': 'FW', 'age': 29, 'caps': '20', 'goals': '7', 'country': 'Spain', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marco Asensio', 'pos': 'MF', 'age': 22, 'caps': '12', 'goals': '0', 'country': 'Spain', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'David Silva', 'pos': 'FW', 'age': 32, 'caps': '121', 'goals': '35', 'country': 'Spain', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Isco', 'pos': 'MF', 'age': 26, 'caps': '28', 'goals': '10', 'country': 'Spain', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Pepe Reina', 'pos': 'GK', 'age': 35, 'caps': '36', 'goals': '0', 'country': 'Spain', 'club': 'Napoli', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Mathew Ryan', 'pos': 'GK', 'age': 26, 'caps': '44', 'goals': '0', 'country': 'Australia', 'club': 'Brighton & Hove Albion', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Milos Degenek', 'pos': 'DF', 'age': 24, 'caps': '18', 'goals': '0', 'country': 'Australia', 'club': 'Yokohama F. Marinos', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'James Meredith', 'pos': 'DF', 'age': 30, 'caps': '2', 'goals': '0', 'country': 'Australia', 'club': 'Millwall', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Tim Cahill', 'pos': 'FW', 'age': 38, 'caps': '106', 'goals': '50', 'country': 'Australia', 'club': 'Millwall', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mark Milligan', 'pos': 'DF', 'age': 32, 'caps': '71', 'goals': '6', 'country': 'Australia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Matthew Jurman', 'pos': 'DF', 'age': 28, 'caps': '4', 'goals': '0', 'country': 'Australia', 'club': 'Suwon Samsung Bluewings', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mathew Leckie', 'pos': 'FW', 'age': 27, 'caps': '53', 'goals': '8', 'country': 'Australia', 'club': 'Hertha BSC', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Massimo Luongo', 'pos': 'MF', 'age': 25, 'caps': '36', 'goals': '5', 'country': 'Australia', 'club': 'Queens Park Rangers', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Tomi Juric', 'pos': 'FW', 'age': 26, 'caps': '35', 'goals': '8', 'country': 'Australia', 'club': 'Luzern', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Robbie Kruse', 'pos': 'FW', 'age': 29, 'caps': '64', 'goals': '5', 'country': 'Australia', 'club': 'VfL Bochum', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Andrew Nabbout', 'pos': 'FW', 'age': 25, 'caps': '4', 'goals': '1', 'country': 'Australia', 'club': 'Urawa Red Diamonds', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Brad Jones', 'pos': 'GK', 'age': 36, 'caps': '6', 'goals': '0', 'country': 'Australia', 'club': 'Feyenoord', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Aaron Mooy', 'pos': 'MF', 'age': 27, 'caps': '34', 'goals': '5', 'country': 'Australia', 'club': 'Huddersfield Town', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jamie Maclaren', 'pos': 'FW', 'age': 24, 'caps': '6', 'goals': '0', 'country': 'Australia', 'club': 'Hibernian', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mile Jedinak', 'pos': 'MF', 'age': 33, 'caps': '76', 'goals': '18', 'country': 'Australia', 'club': 'Aston Villa', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Aziz Behich', 'pos': 'DF', 'age': 27, 'caps': '23', 'goals': '2', 'country': 'Australia', 'club': 'Bursaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Daniel Arzani', 'pos': 'FW', 'age': 19, 'caps': '2', 'goals': '1', 'country': 'Australia', 'club': 'Melbourne City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Danny Vukovic', 'pos': 'GK', 'age': 33, 'caps': '1', 'goals': '0', 'country': 'Australia', 'club': 'Genk', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Josh Risdon', 'pos': 'DF', 'age': 25, 'caps': '8', 'goals': '0', 'country': 'Australia', 'club': 'Western Sydney Wanderers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Trent Sainsbury', 'pos': 'DF', 'age': 26, 'caps': '35', 'goals': '3', 'country': 'Australia', 'club': 'Grasshoppers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Dimitri Petratos', 'pos': 'FW', 'age': 25, 'caps': '2', 'goals': '0', 'country': 'Australia', 'club': 'Newcastle Jets', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jackson Irvine', 'pos': 'MF', 'age': 25, 'caps': '19', 'goals': '2', 'country': 'Australia', 'club': 'Hull City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Tom Rogic', 'pos': 'MF', 'age': 25, 'caps': '37', 'goals': '7', 'country': 'Australia', 'club': 'Celtic', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Kasper Schmeichel', 'pos': 'GK', 'age': 31, 'caps': '35', 'goals': '0', 'country': 'Denmark', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Michael Krohn-Dehli', 'pos': 'MF', 'age': 35, 'caps': '59', 'goals': '6', 'country': 'Denmark', 'club': 'Deportivo La Coruña', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jannik Vestergaard', 'pos': 'DF', 'age': 25, 'caps': '16', 'goals': '1', 'country': 'Denmark', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Simon Kjær', 'pos': 'DF', 'age': 29, 'caps': '78', 'goals': '3', 'country': 'Denmark', 'club': 'Sevilla', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jonas Knudsen', 'pos': 'DF', 'age': 25, 'caps': '3', 'goals': '0', 'country': 'Denmark', 'club': 'Ipswich Town', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Andreas Christensen', 'pos': 'DF', 'age': 22, 'caps': '16', 'goals': '1', 'country': 'Denmark', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'William Kvist', 'pos': 'MF', 'age': 33, 'caps': '80', 'goals': '2', 'country': 'Denmark', 'club': 'Copenhagen', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Thomas Delaney', 'pos': 'MF', 'age': 26, 'caps': '27', 'goals': '4', 'country': 'Denmark', 'club': 'Werder Bremen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nicolai Jørgensen', 'pos': 'FW', 'age': 27, 'caps': '31', 'goals': '8', 'country': 'Denmark', 'club': 'Feyenoord', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Christian Eriksen', 'pos': 'MF', 'age': 26, 'caps': '78', 'goals': '22', 'country': 'Denmark', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Martin Braithwaite', 'pos': 'FW', 'age': 27, 'caps': '20', 'goals': '1', 'country': 'Denmark', 'club': 'Bordeaux', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kasper Dolberg', 'pos': 'FW', 'age': 20, 'caps': '6', 'goals': '1', 'country': 'Denmark', 'club': 'Ajax', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mathias Jørgensen', 'pos': 'DF', 'age': 28, 'caps': '12', 'goals': '0', 'country': 'Denmark', 'club': 'Huddersfield Town', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Henrik Dalsgaard', 'pos': 'DF', 'age': 28, 'caps': '11', 'goals': '0', 'country': 'Denmark', 'club': 'Brentford', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Viktor Fischer', 'pos': 'FW', 'age': 24, 'caps': '19', 'goals': '3', 'country': 'Denmark', 'club': 'Copenhagen', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jonas Lössl', 'pos': 'GK', 'age': 29, 'caps': '1', 'goals': '0', 'country': 'Denmark', 'club': 'Huddersfield Town', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jens Stryger Larsen', 'pos': 'DF', 'age': 27, 'caps': '13', 'goals': '1', 'country': 'Denmark', 'club': 'Udinese', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Lukas Lerager', 'pos': 'MF', 'age': 24, 'caps': '4', 'goals': '0', 'country': 'Denmark', 'club': 'Bordeaux', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Lasse Schöne', 'pos': 'MF', 'age': 32, 'caps': '36', 'goals': '3', 'country': 'Denmark', 'club': 'Ajax', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yussuf Poulsen', 'pos': 'FW', 'age': 24, 'caps': '28', 'goals': '4', 'country': 'Denmark', 'club': 'RB Leipzig', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Andreas Cornelius', 'pos': 'FW', 'age': 25, 'caps': '18', 'goals': '4', 'country': 'Denmark', 'club': 'Atalanta', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Frederik Rønnow', 'pos': 'GK', 'age': 25, 'caps': '6', 'goals': '0', 'country': 'Denmark', 'club': 'Brøndby', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Pione Sisto', 'pos': 'FW', 'age': 23, 'caps': '14', 'goals': '1', 'country': 'Denmark', 'club': 'Celta Vigo', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Hugo Lloris', 'pos': 'GK', 'age': 31, 'caps': '98', 'goals': '0', 'country': 'France', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Benjamin Pavard', 'pos': 'DF', 'age': 22, 'caps': '6', 'goals': '0', 'country': 'France', 'club': 'VfB Stuttgart', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Presnel Kimpembe', 'pos': 'DF', 'age': 22, 'caps': '2', 'goals': '0', 'country': 'France', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Raphaël Varane', 'pos': 'DF', 'age': 25, 'caps': '42', 'goals': '2', 'country': 'France', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Samuel Umtiti', 'pos': 'DF', 'age': 24, 'caps': '19', 'goals': '2', 'country': 'France', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Paul Pogba', 'pos': 'MF', 'age': 25, 'caps': '54', 'goals': '9', 'country': 'France', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Antoine Griezmann', 'pos': 'FW', 'age': 27, 'caps': '54', 'goals': '20', 'country': 'France', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Thomas Lemar', 'pos': 'FW', 'age': 22, 'caps': '12', 'goals': '3', 'country': 'France', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Olivier Giroud', 'pos': 'FW', 'age': 31, 'caps': '74', 'goals': '31', 'country': 'France', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Kylian Mbappé', 'pos': 'FW', 'age': 19, 'caps': '15', 'goals': '4', 'country': 'France', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ousmane Dembélé', 'pos': 'FW', 'age': 21, 'caps': '12', 'goals': '2', 'country': 'France', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Corentin Tolisso', 'pos': 'MF', 'age': 23, 'caps': '9', 'goals': '0', 'country': 'France', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': "N'Golo Kanté", 'pos': 'MF', 'age': 27, 'caps': '24', 'goals': '1', 'country': 'France', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Blaise Matuidi', 'pos': 'MF', 'age': 31, 'caps': '67', 'goals': '9', 'country': 'France', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Steven Nzonzi', 'pos': 'MF', 'age': 29, 'caps': '4', 'goals': '0', 'country': 'France', 'club': 'Sevilla', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Steve Mandanda', 'pos': 'GK', 'age': 33, 'caps': '27', 'goals': '0', 'country': 'France', 'club': 'Marseille', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Adil Rami', 'pos': 'DF', 'age': 32, 'caps': '35', 'goals': '1', 'country': 'France', 'club': 'Marseille', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nabil Fekir', 'pos': 'FW', 'age': 24, 'caps': '12', 'goals': '2', 'country': 'France', 'club': 'Lyon', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Djibril Sidibé', 'pos': 'DF', 'age': 25, 'caps': '17', 'goals': '1', 'country': 'France', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Florian Thauvin', 'pos': 'FW', 'age': 25, 'caps': '4', 'goals': '0', 'country': 'France', 'club': 'Marseille', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Lucas Hernández', 'pos': 'DF', 'age': 22, 'caps': '5', 'goals': '0', 'country': 'France', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Benjamin Mendy', 'pos': 'DF', 'age': 23, 'caps': '7', 'goals': '0', 'country': 'France', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Alphonse Areola', 'pos': 'GK', 'age': 25, 'caps': '0', 'goals': '0', 'country': 'France', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Pedro Gallese', 'pos': 'GK', 'age': 28, 'caps': '38', 'goals': '0', 'country': 'Peru', 'club': 'Veracruz', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alberto Rodríguez', 'pos': 'DF', 'age': 34, 'caps': '59', 'goals': '0', 'country': 'Peru', 'club': 'Atlético Junior', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aldo Corzo', 'pos': 'DF', 'age': 29, 'caps': '25', 'goals': '0', 'country': 'Peru', 'club': 'Universitario', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Anderson Santamaría', 'pos': 'DF', 'age': 26, 'caps': '4', 'goals': '0', 'country': 'Peru', 'club': 'Puebla', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Miguel Araujo', 'pos': 'DF', 'age': 23, 'caps': '7', 'goals': '0', 'country': 'Peru', 'club': 'Alianza Lima', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Miguel Trauco', 'pos': 'DF', 'age': 25, 'caps': '27', 'goals': '0', 'country': 'Peru', 'club': 'Flamengo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Paolo Hurtado', 'pos': 'MF', 'age': 27, 'caps': '31', 'goals': '3', 'country': 'Peru', 'club': 'Vitória de Guimarães', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Christian Cueva', 'pos': 'MF', 'age': 26, 'caps': '44', 'goals': '8', 'country': 'Peru', 'club': 'São Paulo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Paolo Guerrero', 'pos': 'FW', 'age': 34, 'caps': '88', 'goals': '34', 'country': 'Peru', 'club': 'Flamengo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jefferson Farfán', 'pos': 'FW', 'age': 33, 'caps': '84', 'goals': '25', 'country': 'Peru', 'club': 'Lokomotiv Moscow', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Raúl Ruidíaz', 'pos': 'FW', 'age': 27, 'caps': '30', 'goals': '4', 'country': 'Peru', 'club': 'Morelia', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Carlos Cáceda', 'pos': 'GK', 'age': 26, 'caps': '4', 'goals': '0', 'country': 'Peru', 'club': 'Deportivo Municipal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Renato Tapia', 'pos': 'MF', 'age': 22, 'caps': '23', 'goals': '1', 'country': 'Peru', 'club': 'Feyenoord', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Andy Polo', 'pos': 'MF', 'age': 23, 'caps': '17', 'goals': '1', 'country': 'Peru', 'club': 'Portland Timbers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Christian Ramos', 'pos': 'DF', 'age': 29, 'caps': '66', 'goals': '3', 'country': 'Peru', 'club': 'Veracruz', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Wilder Cartagena', 'pos': 'MF', 'age': 23, 'caps': '3', 'goals': '0', 'country': 'Peru', 'club': 'Veracruz', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Luis Advíncula', 'pos': 'DF', 'age': 28, 'caps': '65', 'goals': '0', 'country': 'Peru', 'club': 'Lobos BUAP', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'André Carrillo', 'pos': 'FW', 'age': 27, 'caps': '45', 'goals': '5', 'country': 'Peru', 'club': 'Watford', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Yoshimar Yotún', 'pos': 'MF', 'age': 28, 'caps': '73', 'goals': '2', 'country': 'Peru', 'club': 'Orlando City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Edison Flores', 'pos': 'FW', 'age': 24, 'caps': '29', 'goals': '9', 'country': 'Peru', 'club': 'AaB', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'José Carvallo', 'pos': 'GK', 'age': 32, 'caps': '6', 'goals': '0', 'country': 'Peru', 'club': 'UTC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Nilson Loyola', 'pos': 'DF', 'age': 23, 'caps': '3', 'goals': '0', 'country': 'Peru', 'club': 'Melgar', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Pedro Aquino', 'pos': 'MF', 'age': 23, 'caps': '13', 'goals': '0', 'country': 'Peru', 'club': 'Lobos BUAP', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Nahuel Guzmán', 'pos': 'GK', 'age': 32, 'caps': '6', 'goals': '0', 'country': 'Argentina', 'club': 'UANL', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gabriel Mercado', 'pos': 'DF', 'age': 31, 'caps': '20', 'goals': '3', 'country': 'Argentina', 'club': 'Sevilla', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Nicolás Tagliafico', 'pos': 'DF', 'age': 25, 'caps': '4', 'goals': '0', 'country': 'Argentina', 'club': 'Ajax', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Cristian Ansaldi', 'pos': 'DF', 'age': 31, 'caps': '5', 'goals': '0', 'country': 'Argentina', 'club': 'Torino', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Lucas Biglia', 'pos': 'MF', 'age': 32, 'caps': '57', 'goals': '1', 'country': 'Argentina', 'club': 'Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Federico Fazio', 'pos': 'DF', 'age': 31, 'caps': '9', 'goals': '1', 'country': 'Argentina', 'club': 'Roma', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Éver Banega', 'pos': 'MF', 'age': 30, 'caps': '62', 'goals': '6', 'country': 'Argentina', 'club': 'Sevilla', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marcos Acuña', 'pos': 'DF', 'age': 26, 'caps': '10', 'goals': '0', 'country': 'Argentina', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Gonzalo Higuaín', 'pos': 'FW', 'age': 30, 'caps': '71', 'goals': '31', 'country': 'Argentina', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Lionel Messi', 'pos': 'FW', 'age': 31, 'caps': '124', 'goals': '64', 'country': 'Argentina', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ángel Di María', 'pos': 'MF', 'age': 30, 'caps': '94', 'goals': '19', 'country': 'Argentina', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Franco Armani', 'pos': 'GK', 'age': 31, 'caps': '0', 'goals': '0', 'country': 'Argentina', 'club': 'River Plate', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Maximiliano Meza', 'pos': 'MF', 'age': 25, 'caps': '2', 'goals': '0', 'country': 'Argentina', 'club': 'Independiente', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Javier Mascherano', 'pos': 'DF', 'age': 34, 'caps': '143', 'goals': '3', 'country': 'Argentina', 'club': 'Hebei China Fortune', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Enzo Pérez', 'pos': 'MF', 'age': 32, 'caps': '23', 'goals': '1', 'country': 'Argentina', 'club': 'River Plate', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Marcos Rojo', 'pos': 'DF', 'age': 28, 'caps': '56', 'goals': '2', 'country': 'Argentina', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Nicolás Otamendi', 'pos': 'DF', 'age': 30, 'caps': '54', 'goals': '4', 'country': 'Argentina', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Eduardo Salvio', 'pos': 'DF', 'age': 27, 'caps': '9', 'goals': '0', 'country': 'Argentina', 'club': 'Benfica', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Sergio Agüero', 'pos': 'FW', 'age': 30, 'caps': '85', 'goals': '37', 'country': 'Argentina', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Giovani Lo Celso', 'pos': 'MF', 'age': 22, 'caps': '5', 'goals': '0', 'country': 'Argentina', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Paulo Dybala', 'pos': 'FW', 'age': 24, 'caps': '12', 'goals': '0', 'country': 'Argentina', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Cristian Pavón', 'pos': 'MF', 'age': 22, 'caps': '5', 'goals': '0', 'country': 'Argentina', 'club': 'Boca Juniors', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Willy Caballero', 'pos': 'GK', 'age': 36, 'caps': '3', 'goals': '0', 'country': 'Argentina', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Dominik Livaković', 'pos': 'GK', 'age': 23, 'caps': '1', 'goals': '0', 'country': 'Croatia', 'club': 'Dinamo Zagreb', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Šime Vrsaljko', 'pos': 'DF', 'age': 26, 'caps': '35', 'goals': '0', 'country': 'Croatia', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ivan Strinić', 'pos': 'DF', 'age': 30, 'caps': '43', 'goals': '0', 'country': 'Croatia', 'club': 'Sampdoria', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ivan Perišić', 'pos': 'FW', 'age': 29, 'caps': '66', 'goals': '18', 'country': 'Croatia', 'club': 'Inter Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Vedran Ćorluka', 'pos': 'DF', 'age': 32, 'caps': '99', 'goals': '4', 'country': 'Croatia', 'club': 'Lokomotiv Moscow', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Dejan Lovren', 'pos': 'DF', 'age': 28, 'caps': '39', 'goals': '2', 'country': 'Croatia', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ivan Rakitić', 'pos': 'MF', 'age': 30, 'caps': '92', 'goals': '14', 'country': 'Croatia', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Mateo Kovačić', 'pos': 'MF', 'age': 24, 'caps': '41', 'goals': '1', 'country': 'Croatia', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Andrej Kramarić', 'pos': 'FW', 'age': 27, 'caps': '31', 'goals': '9', 'country': 'Croatia', 'club': '1899 Hoffenheim', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Luka Modrić', 'pos': 'MF', 'age': 32, 'caps': '106', 'goals': '12', 'country': 'Croatia', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marcelo Brozović', 'pos': 'MF', 'age': 25, 'caps': '35', 'goals': '6', 'country': 'Croatia', 'club': 'Inter Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Lovre Kalinić', 'pos': 'GK', 'age': 28, 'caps': '11', 'goals': '0', 'country': 'Croatia', 'club': 'Gent', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Tin Jedvaj', 'pos': 'DF', 'age': 22, 'caps': '12', 'goals': '0', 'country': 'Croatia', 'club': 'Bayer Leverkusen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Filip Bradarić', 'pos': 'MF', 'age': 26, 'caps': '4', 'goals': '0', 'country': 'Croatia', 'club': 'Rijeka', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Duje Ćaleta-Car', 'pos': 'DF', 'age': 21, 'caps': '1', 'goals': '0', 'country': 'Croatia', 'club': 'Red Bull Salzburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Nikola Kalinić', 'pos': 'FW', 'age': 30, 'caps': '42', 'goals': '15', 'country': 'Croatia', 'club': 'Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mario Mandžukić', 'pos': 'FW', 'age': 32, 'caps': '83', 'goals': '30', 'country': 'Croatia', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ante Rebić', 'pos': 'FW', 'age': 24, 'caps': '16', 'goals': '1', 'country': 'Croatia', 'club': 'Eintracht Frankfurt', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Milan Badelj', 'pos': 'MF', 'age': 29, 'caps': '38', 'goals': '1', 'country': 'Croatia', 'club': 'Fiorentina', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Marko Pjaca', 'pos': 'FW', 'age': 23, 'caps': '16', 'goals': '1', 'country': 'Croatia', 'club': 'Schalke 04', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Domagoj Vida', 'pos': 'DF', 'age': 29, 'caps': '59', 'goals': '2', 'country': 'Croatia', 'club': 'Beşiktaş', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Josip Pivarić', 'pos': 'DF', 'age': 29, 'caps': '19', 'goals': '0', 'country': 'Croatia', 'club': 'Dynamo Kyiv', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Danijel Subašić', 'pos': 'GK', 'age': 33, 'caps': '38', 'goals': '0', 'country': 'Croatia', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Hannes Þór Halldórsson', 'pos': 'GK', 'age': 34, 'caps': '49', 'goals': '0', 'country': 'Iceland', 'club': 'Randers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Birkir Már Sævarsson', 'pos': 'DF', 'age': 33, 'caps': '79', 'goals': '1', 'country': 'Iceland', 'club': 'Valur', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Samúel Friðjónsson', 'pos': 'MF', 'age': 22, 'caps': '4', 'goals': '0', 'country': 'Iceland', 'club': 'Vålerenga', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Albert Guðmundsson', 'pos': 'MF', 'age': 21, 'caps': '5', 'goals': '3', 'country': 'Iceland', 'club': 'PSV Eindhoven', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Sverrir Ingi Ingason', 'pos': 'DF', 'age': 24, 'caps': '20', 'goals': '3', 'country': 'Iceland', 'club': 'Rostov', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ragnar Sigurðsson', 'pos': 'DF', 'age': 32, 'caps': '77', 'goals': '3', 'country': 'Iceland', 'club': 'Rostov', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jóhann Berg Guðmundsson', 'pos': 'MF', 'age': 27, 'caps': '67', 'goals': '7', 'country': 'Iceland', 'club': 'Burnley', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Birkir Bjarnason', 'pos': 'MF', 'age': 30, 'caps': '67', 'goals': '9', 'country': 'Iceland', 'club': 'Aston Villa', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Björn Bergmann Sigurðarson', 'pos': 'FW', 'age': 27, 'caps': '12', 'goals': '1', 'country': 'Iceland', 'club': 'Rostov', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gylfi Sigurðsson', 'pos': 'MF', 'age': 28, 'caps': '57', 'goals': '19', 'country': 'Iceland', 'club': 'Everton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Alfreð Finnbogason', 'pos': 'FW', 'age': 29, 'caps': '47', 'goals': '13', 'country': 'Iceland', 'club': 'FC Augsburg', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Frederik Schram', 'pos': 'GK', 'age': 23, 'caps': '4', 'goals': '0', 'country': 'Iceland', 'club': 'Roskilde', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Rúnar Alex Rúnarsson', 'pos': 'GK', 'age': 23, 'caps': '3', 'goals': '0', 'country': 'Iceland', 'club': 'Nordsjælland', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kári Árnason', 'pos': 'DF', 'age': 35, 'caps': '67', 'goals': '5', 'country': 'Iceland', 'club': 'Aberdeen', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Hólmar Örn Eyjólfsson', 'pos': 'DF', 'age': 27, 'caps': '10', 'goals': '1', 'country': 'Iceland', 'club': 'Levski Sofia', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ólafur Ingi Skúlason', 'pos': 'MF', 'age': 35, 'caps': '36', 'goals': '1', 'country': 'Iceland', 'club': 'Kardemir Karabükspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aron Gunnarsson', 'pos': 'MF', 'age': 29, 'caps': '77', 'goals': '2', 'country': 'Iceland', 'club': 'Cardiff City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Hörður Björgvin Magnússon', 'pos': 'DF', 'age': 25, 'caps': '16', 'goals': '2', 'country': 'Iceland', 'club': 'Bristol City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Rúrik Gíslason', 'pos': 'MF', 'age': 30, 'caps': '47', 'goals': '3', 'country': 'Iceland', 'club': 'SV Sandhausen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Emil Hallfreðsson', 'pos': 'MF', 'age': 34, 'caps': '64', 'goals': '1', 'country': 'Iceland', 'club': 'Udinese', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Arnór Ingvi Traustason', 'pos': 'MF', 'age': 25, 'caps': '19', 'goals': '5', 'country': 'Iceland', 'club': 'Malmö', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jón Daði Böðvarsson', 'pos': 'FW', 'age': 26, 'caps': '38', 'goals': '2', 'country': 'Iceland', 'club': 'Reading', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ari Freyr Skúlason', 'pos': 'DF', 'age': 31, 'caps': '56', 'goals': '0', 'country': 'Iceland', 'club': 'Lokeren', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ikechukwu Ezenwa', 'pos': 'GK', 'age': 29, 'caps': '24', 'goals': '0', 'country': 'Nigeria', 'club': 'Enyimba', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Brian Idowu', 'pos': 'DF', 'age': 26, 'caps': '5', 'goals': '1', 'country': 'Nigeria', 'club': 'Amkar Perm', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Elderson Echiéjilé', 'pos': 'DF', 'age': 30, 'caps': '62', 'goals': '3', 'country': 'Nigeria', 'club': 'Cercle Brugge', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Wilfred Ndidi', 'pos': 'MF', 'age': 21, 'caps': '17', 'goals': '0', 'country': 'Nigeria', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'William Troost-Ekong', 'pos': 'DF', 'age': 24, 'caps': '22', 'goals': '1', 'country': 'Nigeria', 'club': 'Bursaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Leon Balogun', 'pos': 'DF', 'age': 30, 'caps': '19', 'goals': '0', 'country': 'Nigeria', 'club': 'Mainz 05', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ahmed Musa', 'pos': 'FW', 'age': 25, 'caps': '72', 'goals': '13', 'country': 'Nigeria', 'club': 'CSKA Moscow', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Peter Etebo', 'pos': 'MF', 'age': 22, 'caps': '14', 'goals': '1', 'country': 'Nigeria', 'club': 'Las Palmas', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Odion Ighalo', 'pos': 'FW', 'age': 29, 'caps': '19', 'goals': '4', 'country': 'Nigeria', 'club': 'Changchun Yatai', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'John Obi Mikel', 'pos': 'MF', 'age': 31, 'caps': '85', 'goals': '6', 'country': 'Nigeria', 'club': 'Tianjin TEDA', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Victor Moses', 'pos': 'FW', 'age': 27, 'caps': '34', 'goals': '11', 'country': 'Nigeria', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Shehu Abdullahi', 'pos': 'DF', 'age': 25, 'caps': '25', 'goals': '0', 'country': 'Nigeria', 'club': 'Bursaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Simeon Nwankwo', 'pos': 'FW', 'age': 26, 'caps': '2', 'goals': '0', 'country': 'Nigeria', 'club': 'Crotone', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kelechi Iheanacho', 'pos': 'FW', 'age': 22, 'caps': '18', 'goals': '8', 'country': 'Nigeria', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Joel Obi', 'pos': 'MF', 'age': 27, 'caps': '17', 'goals': '0', 'country': 'Nigeria', 'club': 'Torino', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Daniel Akpeyi', 'pos': 'GK', 'age': 32, 'caps': '7', 'goals': '0', 'country': 'Nigeria', 'club': 'Chippa United', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ogenyi Onazi', 'pos': 'MF', 'age': 25, 'caps': '52', 'goals': '1', 'country': 'Nigeria', 'club': 'Trabzonspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alex Iwobi', 'pos': 'FW', 'age': 22, 'caps': '19', 'goals': '5', 'country': 'Nigeria', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'John Ogu', 'pos': 'MF', 'age': 30, 'caps': '20', 'goals': '2', 'country': 'Nigeria', 'club': "Hapoel Be'er Sheva", 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Chidozie Awaziem', 'pos': 'DF', 'age': 21, 'caps': '4', 'goals': '0', 'country': 'Nigeria', 'club': 'Nantes', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Tyronne Ebuehi', 'pos': 'DF', 'age': 22, 'caps': '7', 'goals': '0', 'country': 'Nigeria', 'club': 'ADO Den Haag', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kenneth Omeruo', 'pos': 'DF', 'age': 24, 'caps': '39', 'goals': '0', 'country': 'Nigeria', 'club': 'Kasımpaşa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Francis Uzoho', 'pos': 'GK', 'age': 19, 'caps': '6', 'goals': '0', 'country': 'Nigeria', 'club': 'Deportivo La Coruña', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Alisson', 'pos': 'GK', 'age': 25, 'caps': '26', 'goals': '0', 'country': 'Brazil', 'club': 'Roma', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Thiago Silva', 'pos': 'DF', 'age': 33, 'caps': '71', 'goals': '5', 'country': 'Brazil', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Miranda', 'pos': 'DF', 'age': 33, 'caps': '47', 'goals': '2', 'country': 'Brazil', 'club': 'Inter Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Pedro Geromel', 'pos': 'DF', 'age': 32, 'caps': '2', 'goals': '0', 'country': 'Brazil', 'club': 'Grêmio', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Casemiro', 'pos': 'MF', 'age': 26, 'caps': '24', 'goals': '0', 'country': 'Brazil', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Filipe Luís', 'pos': 'DF', 'age': 32, 'caps': '33', 'goals': '2', 'country': 'Brazil', 'club': 'Atlético Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Douglas Costa', 'pos': 'FW', 'age': 27, 'caps': '25', 'goals': '3', 'country': 'Brazil', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Renato Augusto', 'pos': 'MF', 'age': 30, 'caps': '28', 'goals': '5', 'country': 'Brazil', 'club': 'Beijing Sinobo Guoan', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gabriel Jesus', 'pos': 'FW', 'age': 21, 'caps': '17', 'goals': '10', 'country': 'Brazil', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Neymar', 'pos': 'FW', 'age': 26, 'caps': '85', 'goals': '55', 'country': 'Brazil', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Philippe Coutinho', 'pos': 'MF', 'age': 26, 'caps': '37', 'goals': '10', 'country': 'Brazil', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marcelo', 'pos': 'DF', 'age': 30, 'caps': '54', 'goals': '6', 'country': 'Brazil', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marquinhos', 'pos': 'DF', 'age': 24, 'caps': '26', 'goals': '0', 'country': 'Brazil', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Danilo', 'pos': 'DF', 'age': 26, 'caps': '18', 'goals': '0', 'country': 'Brazil', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Paulinho', 'pos': 'MF', 'age': 29, 'caps': '50', 'goals': '12', 'country': 'Brazil', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Cássio', 'pos': 'GK', 'age': 31, 'caps': '1', 'goals': '0', 'country': 'Brazil', 'club': 'Corinthians', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Fernandinho', 'pos': 'MF', 'age': 33, 'caps': '44', 'goals': '2', 'country': 'Brazil', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Fred', 'pos': 'MF', 'age': 25, 'caps': '8', 'goals': '0', 'country': 'Brazil', 'club': 'Shakhtar Donetsk', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Willian', 'pos': 'MF', 'age': 29, 'caps': '57', 'goals': '8', 'country': 'Brazil', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Roberto Firmino', 'pos': 'FW', 'age': 26, 'caps': '21', 'goals': '6', 'country': 'Brazil', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Taison', 'pos': 'FW', 'age': 30, 'caps': '8', 'goals': '1', 'country': 'Brazil', 'club': 'Shakhtar Donetsk', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Fagner', 'pos': 'DF', 'age': 29, 'caps': '4', 'goals': '0', 'country': 'Brazil', 'club': 'Corinthians', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ederson', 'pos': 'GK', 'age': 24, 'caps': '1', 'goals': '0', 'country': 'Brazil', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Keylor Navas', 'pos': 'GK', 'age': 31, 'caps': '81', 'goals': '0', 'country': 'Costa Rica', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Johnny Acosta', 'pos': 'DF', 'age': 34, 'caps': '69', 'goals': '2', 'country': 'Costa Rica', 'club': 'Águilas Doradas', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Giancarlo González', 'pos': 'DF', 'age': 30, 'caps': '70', 'goals': '2', 'country': 'Costa Rica', 'club': 'Bologna', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ian Smith', 'pos': 'DF', 'age': 20, 'caps': '4', 'goals': '0', 'country': 'Costa Rica', 'club': 'Norrköping', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Celso Borges', 'pos': 'MF', 'age': 30, 'caps': '113', 'goals': '21', 'country': 'Costa Rica', 'club': 'Deportivo La Coruña', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Óscar Duarte', 'pos': 'DF', 'age': 29, 'caps': '39', 'goals': '2', 'country': 'Costa Rica', 'club': 'Espanyol', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Christian Bolaños', 'pos': 'MF', 'age': 34, 'caps': '81', 'goals': '6', 'country': 'Costa Rica', 'club': 'Saprissa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Bryan Oviedo', 'pos': 'DF', 'age': 28, 'caps': '44', 'goals': '1', 'country': 'Costa Rica', 'club': 'Sunderland', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Daniel Colindres', 'pos': 'MF', 'age': 33, 'caps': '11', 'goals': '0', 'country': 'Costa Rica', 'club': 'Saprissa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Bryan Ruiz', 'pos': 'MF', 'age': 32, 'caps': '110', 'goals': '24', 'country': 'Costa Rica', 'club': 'Sporting CP', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Johan Venegas', 'pos': 'FW', 'age': 29, 'caps': '47', 'goals': '10', 'country': 'Costa Rica', 'club': 'Saprissa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Joel Campbell', 'pos': 'FW', 'age': 26, 'caps': '77', 'goals': '15', 'country': 'Costa Rica', 'club': 'Real Betis', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Rodney Wallace', 'pos': 'MF', 'age': 30, 'caps': '30', 'goals': '4', 'country': 'Costa Rica', 'club': 'New York City FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Randall Azofeifa', 'pos': 'MF', 'age': 33, 'caps': '58', 'goals': '3', 'country': 'Costa Rica', 'club': 'Herediano', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Francisco Calvo', 'pos': 'DF', 'age': 25, 'caps': '38', 'goals': '4', 'country': 'Costa Rica', 'club': 'Minnesota United', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Cristian Gamboa', 'pos': 'DF', 'age': 28, 'caps': '68', 'goals': '3', 'country': 'Costa Rica', 'club': 'Celtic', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Yeltsin Tejeda', 'pos': 'MF', 'age': 26, 'caps': '51', 'goals': '0', 'country': 'Costa Rica', 'club': 'Lausanne', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Patrick Pemberton', 'pos': 'GK', 'age': 36, 'caps': '39', 'goals': '0', 'country': 'Costa Rica', 'club': 'Alajuelense', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kendall Waston', 'pos': 'DF', 'age': 30, 'caps': '26', 'goals': '3', 'country': 'Costa Rica', 'club': 'Vancouver Whitecaps FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'David Guzmán', 'pos': 'MF', 'age': 28, 'caps': '43', 'goals': '0', 'country': 'Costa Rica', 'club': 'Portland Timbers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Marco Ureña', 'pos': 'FW', 'age': 28, 'caps': '64', 'goals': '15', 'country': 'Costa Rica', 'club': 'Los Angeles FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kenner Gutiérrez', 'pos': 'DF', 'age': 29, 'caps': '9', 'goals': '0', 'country': 'Costa Rica', 'club': 'Alajuelense', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Leonel Moreira', 'pos': 'GK', 'age': 28, 'caps': '9', 'goals': '0', 'country': 'Costa Rica', 'club': 'Herediano', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Vladimir Stojković', 'pos': 'GK', 'age': 34, 'caps': '81', 'goals': '0', 'country': 'Serbia', 'club': 'Partizan', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Antonio Rukavina', 'pos': 'DF', 'age': 34, 'caps': '47', 'goals': '0', 'country': 'Serbia', 'club': 'Villarreal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Duško Tošić', 'pos': 'DF', 'age': 33, 'caps': '24', 'goals': '1', 'country': 'Serbia', 'club': 'Beşiktaş', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Luka Milivojević', 'pos': 'MF', 'age': 27, 'caps': '28', 'goals': '1', 'country': 'Serbia', 'club': 'Crystal Palace', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Uroš Spajić', 'pos': 'DF', 'age': 25, 'caps': '5', 'goals': '0', 'country': 'Serbia', 'club': 'Anderlecht', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Branislav Ivanović', 'pos': 'DF', 'age': 34, 'caps': '103', 'goals': '13', 'country': 'Serbia', 'club': 'Zenit Saint Petersburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Andrija Živković', 'pos': 'MF', 'age': 21, 'caps': '10', 'goals': '0', 'country': 'Serbia', 'club': 'Benfica', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Aleksandar Prijović', 'pos': 'FW', 'age': 28, 'caps': '9', 'goals': '1', 'country': 'Serbia', 'club': 'PAOK', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aleksandar Mitrović', 'pos': 'FW', 'age': 23, 'caps': '37', 'goals': '16', 'country': 'Serbia', 'club': 'Fulham', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Dušan Tadić', 'pos': 'MF', 'age': 29, 'caps': '53', 'goals': '13', 'country': 'Serbia', 'club': 'Southampton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Aleksandar Kolarov', 'pos': 'DF', 'age': 32, 'caps': '76', 'goals': '10', 'country': 'Serbia', 'club': 'Roma', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Predrag Rajković', 'pos': 'GK', 'age': 22, 'caps': '8', 'goals': '0', 'country': 'Serbia', 'club': 'Maccabi Tel Aviv', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Miloš Veljković', 'pos': 'DF', 'age': 22, 'caps': '2', 'goals': '0', 'country': 'Serbia', 'club': 'Werder Bremen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Milan Rodić', 'pos': 'DF', 'age': 27, 'caps': '1', 'goals': '0', 'country': 'Serbia', 'club': 'Red Star Belgrade', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Nikola Milenković', 'pos': 'DF', 'age': 20, 'caps': '3', 'goals': '0', 'country': 'Serbia', 'club': 'Fiorentina', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Marko Grujić', 'pos': 'MF', 'age': 22, 'caps': '8', 'goals': '0', 'country': 'Serbia', 'club': 'Cardiff City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Filip Kostić', 'pos': 'MF', 'age': 25, 'caps': '23', 'goals': '2', 'country': 'Serbia', 'club': 'Hamburger SV', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nemanja Radonjić', 'pos': 'FW', 'age': 22, 'caps': '3', 'goals': '0', 'country': 'Serbia', 'club': 'Red Star Belgrade', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Luka Jović', 'pos': 'FW', 'age': 20, 'caps': '1', 'goals': '0', 'country': 'Serbia', 'club': 'Eintracht Frankfurt', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Sergej Milinković-Savić', 'pos': 'MF', 'age': 23, 'caps': '4', 'goals': '0', 'country': 'Serbia', 'club': 'Lazio', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nemanja Matić', 'pos': 'MF', 'age': 29, 'caps': '40', 'goals': '2', 'country': 'Serbia', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Adem Ljajić', 'pos': 'MF', 'age': 26, 'caps': '29', 'goals': '6', 'country': 'Serbia', 'club': 'Torino', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Marko Dmitrović', 'pos': 'GK', 'age': 26, 'caps': '2', 'goals': '0', 'country': 'Serbia', 'club': 'Eibar', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Yann Sommer', 'pos': 'GK', 'age': 29, 'caps': '35', 'goals': '0', 'country': 'Switzerland', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Stephan Lichtsteiner', 'pos': 'DF', 'age': 34, 'caps': '100', 'goals': '8', 'country': 'Switzerland', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'François Moubandje', 'pos': 'DF', 'age': 28, 'caps': '18', 'goals': '0', 'country': 'Switzerland', 'club': 'Toulouse', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Nico Elvedi', 'pos': 'DF', 'age': 21, 'caps': '6', 'goals': '0', 'country': 'Switzerland', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Manuel Akanji', 'pos': 'DF', 'age': 22, 'caps': '7', 'goals': '0', 'country': 'Switzerland', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Michael Lang', 'pos': 'DF', 'age': 27, 'caps': '24', 'goals': '2', 'country': 'Switzerland', 'club': 'Basel', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Breel Embolo', 'pos': 'FW', 'age': 21, 'caps': '25', 'goals': '3', 'country': 'Switzerland', 'club': 'Schalke 04', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Remo Freuler', 'pos': 'MF', 'age': 26, 'caps': '10', 'goals': '0', 'country': 'Switzerland', 'club': 'Atalanta', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Haris Seferović', 'pos': 'FW', 'age': 26, 'caps': '51', 'goals': '12', 'country': 'Switzerland', 'club': 'Benfica', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Granit Xhaka', 'pos': 'MF', 'age': 25, 'caps': '62', 'goals': '9', 'country': 'Switzerland', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Valon Behrami', 'pos': 'MF', 'age': 33, 'caps': '79', 'goals': '2', 'country': 'Switzerland', 'club': 'Udinese', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Yvon Mvogo', 'pos': 'GK', 'age': 24, 'caps': '0', 'goals': '0', 'country': 'Switzerland', 'club': 'RB Leipzig', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ricardo Rodríguez', 'pos': 'DF', 'age': 25, 'caps': '53', 'goals': '5', 'country': 'Switzerland', 'club': 'Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Steven Zuber', 'pos': 'MF', 'age': 26, 'caps': '12', 'goals': '3', 'country': 'Switzerland', 'club': '1899 Hoffenheim', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Blerim Džemaili', 'pos': 'MF', 'age': 32, 'caps': '65', 'goals': '9', 'country': 'Switzerland', 'club': 'Bologna', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Gelson Fernandes', 'pos': 'MF', 'age': 31, 'caps': '67', 'goals': '2', 'country': 'Switzerland', 'club': 'Eintracht Frankfurt', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Denis Zakaria', 'pos': 'MF', 'age': 21, 'caps': '10', 'goals': '0', 'country': 'Switzerland', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mario Gavranović', 'pos': 'FW', 'age': 28, 'caps': '14', 'goals': '5', 'country': 'Switzerland', 'club': 'Dinamo Zagreb', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Josip Drmić', 'pos': 'FW', 'age': 25, 'caps': '29', 'goals': '9', 'country': 'Switzerland', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Johan Djourou', 'pos': 'DF', 'age': 31, 'caps': '74', 'goals': '2', 'country': 'Switzerland', 'club': 'Antalyaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Roman Bürki', 'pos': 'GK', 'age': 27, 'caps': '9', 'goals': '0', 'country': 'Switzerland', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Fabian Schär', 'pos': 'DF', 'age': 26, 'caps': '39', 'goals': '7', 'country': 'Switzerland', 'club': 'Deportivo La Coruña', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Xherdan Shaqiri', 'pos': 'MF', 'age': 26, 'caps': '70', 'goals': '20', 'country': 'Switzerland', 'club': 'Stoke City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Manuel Neuer', 'pos': 'GK', 'age': 32, 'caps': '76', 'goals': '0', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marvin Plattenhardt', 'pos': 'DF', 'age': 26, 'caps': '6', 'goals': '0', 'country': 'Germany', 'club': 'Hertha BSC', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jonas Hector', 'pos': 'DF', 'age': 28, 'caps': '38', 'goals': '3', 'country': 'Germany', 'club': '1. FC Köln', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Matthias Ginter', 'pos': 'DF', 'age': 24, 'caps': '18', 'goals': '0', 'country': 'Germany', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mats Hummels', 'pos': 'DF', 'age': 29, 'caps': '64', 'goals': '5', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Sami Khedira', 'pos': 'MF', 'age': 31, 'caps': '75', 'goals': '7', 'country': 'Germany', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Julian Draxler', 'pos': 'MF', 'age': 24, 'caps': '44', 'goals': '6', 'country': 'Germany', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Toni Kroos', 'pos': 'MF', 'age': 28, 'caps': '83', 'goals': '12', 'country': 'Germany', 'club': 'Real Madrid', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Timo Werner', 'pos': 'FW', 'age': 22, 'caps': '14', 'goals': '8', 'country': 'Germany', 'club': 'RB Leipzig', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Mesut Özil', 'pos': 'MF', 'age': 29, 'caps': '90', 'goals': '23', 'country': 'Germany', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Marco Reus', 'pos': 'FW', 'age': 29, 'caps': '31', 'goals': '9', 'country': 'Germany', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Kevin Trapp', 'pos': 'GK', 'age': 27, 'caps': '3', 'goals': '0', 'country': 'Germany', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Thomas Müller', 'pos': 'MF', 'age': 28, 'caps': '91', 'goals': '38', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Leon Goretzka', 'pos': 'MF', 'age': 23, 'caps': '15', 'goals': '6', 'country': 'Germany', 'club': 'Schalke 04', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Niklas Süle', 'pos': 'DF', 'age': 22, 'caps': '11', 'goals': '0', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Antonio Rüdiger', 'pos': 'DF', 'age': 25, 'caps': '24', 'goals': '1', 'country': 'Germany', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jérôme Boateng', 'pos': 'DF', 'age': 29, 'caps': '71', 'goals': '1', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Joshua Kimmich', 'pos': 'DF', 'age': 23, 'caps': '29', 'goals': '3', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Sebastian Rudy', 'pos': 'MF', 'age': 28, 'caps': '25', 'goals': '1', 'country': 'Germany', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Julian Brandt', 'pos': 'MF', 'age': 22, 'caps': '16', 'goals': '1', 'country': 'Germany', 'club': 'Bayer Leverkusen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'İlkay Gündoğan', 'pos': 'MF', 'age': 27, 'caps': '26', 'goals': '4', 'country': 'Germany', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marc-André ter Stegen', 'pos': 'GK', 'age': 26, 'caps': '20', 'goals': '0', 'country': 'Germany', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Mario Gómez', 'pos': 'FW', 'age': 32, 'caps': '75', 'goals': '31', 'country': 'Germany', 'club': 'VfB Stuttgart', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'José de Jesús Corona', 'pos': 'GK', 'age': 37, 'caps': '52', 'goals': '0', 'country': 'Mexico', 'club': 'Cruz Azul', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Hugo Ayala', 'pos': 'DF', 'age': 31, 'caps': '43', 'goals': '1', 'country': 'Mexico', 'club': 'UANL', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Carlos Salcedo', 'pos': 'DF', 'age': 24, 'caps': '21', 'goals': '0', 'country': 'Mexico', 'club': 'Eintracht Frankfurt', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Rafael Márquez', 'pos': 'DF', 'age': 39, 'caps': '145', 'goals': '18', 'country': 'Mexico', 'club': 'Atlas', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Érick Gutiérrez', 'pos': 'MF', 'age': 23, 'caps': '9', 'goals': '0', 'country': 'Mexico', 'club': 'Pachuca', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jonathan dos Santos', 'pos': 'MF', 'age': 28, 'caps': '32', 'goals': '0', 'country': 'Mexico', 'club': 'LA Galaxy', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Miguel Layún', 'pos': 'MF', 'age': 30, 'caps': '64', 'goals': '6', 'country': 'Mexico', 'club': 'Sevilla', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marco Fabián', 'pos': 'FW', 'age': 28, 'caps': '39', 'goals': '9', 'country': 'Mexico', 'club': 'Eintracht Frankfurt', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Raúl Jiménez', 'pos': 'FW', 'age': 27, 'caps': '63', 'goals': '13', 'country': 'Mexico', 'club': 'Benfica', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Giovani dos Santos', 'pos': 'MF', 'age': 29, 'caps': '105', 'goals': '19', 'country': 'Mexico', 'club': 'LA Galaxy', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Carlos Vela', 'pos': 'FW', 'age': 29, 'caps': '68', 'goals': '18', 'country': 'Mexico', 'club': 'Los Angeles FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alfredo Talavera', 'pos': 'GK', 'age': 35, 'caps': '27', 'goals': '0', 'country': 'Mexico', 'club': 'Toluca', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Guillermo Ochoa', 'pos': 'GK', 'age': 32, 'caps': '94', 'goals': '0', 'country': 'Mexico', 'club': 'Standard Liège', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Javier Hernández', 'pos': 'FW', 'age': 30, 'caps': '102', 'goals': '49', 'country': 'Mexico', 'club': 'West Ham United', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Héctor Moreno', 'pos': 'DF', 'age': 30, 'caps': '92', 'goals': '3', 'country': 'Mexico', 'club': 'Real Sociedad', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Héctor Herrera', 'pos': 'DF', 'age': 28, 'caps': '66', 'goals': '5', 'country': 'Mexico', 'club': 'Porto', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Jesús Manuel Corona', 'pos': 'MF', 'age': 25, 'caps': '36', 'goals': '7', 'country': 'Mexico', 'club': 'Porto', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Andrés Guardado', 'pos': 'MF', 'age': 31, 'caps': '145', 'goals': '25', 'country': 'Mexico', 'club': 'Real Betis', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Oribe Peralta', 'pos': 'FW', 'age': 34, 'caps': '67', 'goals': '26', 'country': 'Mexico', 'club': 'América', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Javier Aquino', 'pos': 'MF', 'age': 28, 'caps': '53', 'goals': '0', 'country': 'Mexico', 'club': 'UANL', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Edson Álvarez', 'pos': 'DF', 'age': 20, 'caps': '13', 'goals': '1', 'country': 'Mexico', 'club': 'América', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Hirving Lozano', 'pos': 'FW', 'age': 22, 'caps': '28', 'goals': '7', 'country': 'Mexico', 'club': 'PSV Eindhoven', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jesús Gallardo', 'pos': 'MF', 'age': 23, 'caps': '23', 'goals': '0', 'country': 'Mexico', 'club': 'UNAM', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kim Seung-gyu', 'pos': 'GK', 'age': 27, 'caps': '33', 'goals': '0', 'country': 'South Korea', 'club': 'Vissel Kobe', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Lee Yong', 'pos': 'DF', 'age': 31, 'caps': '28', 'goals': '0', 'country': 'South Korea', 'club': 'Jeonbuk Hyundai Motors', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jung Seung-hyun', 'pos': 'DF', 'age': 24, 'caps': '6', 'goals': '0', 'country': 'South Korea', 'club': 'Sagan Tosu', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Oh Ban-suk', 'pos': 'DF', 'age': 30, 'caps': '2', 'goals': '0', 'country': 'South Korea', 'club': 'Jeju United', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yun Young-sun', 'pos': 'DF', 'age': 29, 'caps': '6', 'goals': '0', 'country': 'South Korea', 'club': 'Seongnam FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Park Joo-ho', 'pos': 'DF', 'age': 31, 'caps': '36', 'goals': '0', 'country': 'South Korea', 'club': 'Ulsan Hyundai', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Son Heung-min', 'pos': 'FW', 'age': 25, 'caps': '67', 'goals': '21', 'country': 'South Korea', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ju Se-jong', 'pos': 'MF', 'age': 27, 'caps': '11', 'goals': '1', 'country': 'South Korea', 'club': 'Asan Mugunghwa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kim Shin-wook', 'pos': 'FW', 'age': 30, 'caps': '50', 'goals': '10', 'country': 'South Korea', 'club': 'Jeonbuk Hyundai Motors', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Lee Seung-woo', 'pos': 'MF', 'age': 20, 'caps': '4', 'goals': '0', 'country': 'South Korea', 'club': 'Hellas Verona', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Hwang Hee-chan', 'pos': 'FW', 'age': 22, 'caps': '14', 'goals': '2', 'country': 'South Korea', 'club': 'Red Bull Salzburg', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kim Min-woo', 'pos': 'DF', 'age': 28, 'caps': '20', 'goals': '1', 'country': 'South Korea', 'club': 'Sangju Sangmu', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Koo Ja-cheol', 'pos': 'MF', 'age': 29, 'caps': '68', 'goals': '19', 'country': 'South Korea', 'club': 'FC Augsburg', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Hong Chul', 'pos': 'DF', 'age': 27, 'caps': '14', 'goals': '0', 'country': 'South Korea', 'club': 'Sangju Sangmu', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jung Woo-young', 'pos': 'MF', 'age': 28, 'caps': '30', 'goals': '1', 'country': 'South Korea', 'club': 'Vissel Kobe', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ki Sung-yueng', 'pos': 'MF', 'age': 29, 'caps': '102', 'goals': '10', 'country': 'South Korea', 'club': 'Swansea City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Lee Jae-sung', 'pos': 'MF', 'age': 25, 'caps': '35', 'goals': '6', 'country': 'South Korea', 'club': 'Jeonbuk Hyundai Motors', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Moon Seon-min', 'pos': 'MF', 'age': 26, 'caps': '3', 'goals': '1', 'country': 'South Korea', 'club': 'Incheon United', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kim Young-gwon', 'pos': 'DF', 'age': 28, 'caps': '53', 'goals': '2', 'country': 'South Korea', 'club': 'Guangzhou Evergrande', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jang Hyun-soo', 'pos': 'DF', 'age': 26, 'caps': '51', 'goals': '3', 'country': 'South Korea', 'club': 'FC Tokyo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kim Jin-hyeon', 'pos': 'GK', 'age': 30, 'caps': '15', 'goals': '0', 'country': 'South Korea', 'club': 'Cerezo Osaka', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Go Yo-han', 'pos': 'DF', 'age': 30, 'caps': '20', 'goals': '0', 'country': 'South Korea', 'club': 'FC Seoul', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jo Hyeon-woo', 'pos': 'GK', 'age': 26, 'caps': '6', 'goals': '0', 'country': 'South Korea', 'club': 'Daegu FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Robin Olsen', 'pos': 'GK', 'age': 28, 'caps': '18', 'goals': '0', 'country': 'Sweden', 'club': 'Copenhagen', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mikael Lustig', 'pos': 'DF', 'age': 31, 'caps': '66', 'goals': '6', 'country': 'Sweden', 'club': 'Celtic', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Victor Lindelöf', 'pos': 'DF', 'age': 23, 'caps': '21', 'goals': '1', 'country': 'Sweden', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Andreas Granqvist', 'pos': 'DF', 'age': 33, 'caps': '72', 'goals': '6', 'country': 'Sweden', 'club': 'Krasnodar', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Martin Olsson', 'pos': 'DF', 'age': 30, 'caps': '43', 'goals': '5', 'country': 'Sweden', 'club': 'Swansea City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ludwig Augustinsson', 'pos': 'DF', 'age': 24, 'caps': '15', 'goals': '0', 'country': 'Sweden', 'club': 'Werder Bremen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Sebastian Larsson', 'pos': 'MF', 'age': 33, 'caps': '100', 'goals': '6', 'country': 'Sweden', 'club': 'Hull City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Albin Ekdal', 'pos': 'MF', 'age': 28, 'caps': '34', 'goals': '0', 'country': 'Sweden', 'club': 'Hamburger SV', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Marcus Berg', 'pos': 'FW', 'age': 31, 'caps': '57', 'goals': '18', 'country': 'Sweden', 'club': 'Al Ain', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Emil Forsberg', 'pos': 'MF', 'age': 26, 'caps': '36', 'goals': '6', 'country': 'Sweden', 'club': 'RB Leipzig', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'John Guidetti', 'pos': 'FW', 'age': 26, 'caps': '20', 'goals': '1', 'country': 'Sweden', 'club': 'Alavés', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Karl-Johan Johnsson', 'pos': 'GK', 'age': 28, 'caps': '5', 'goals': '0', 'country': 'Sweden', 'club': 'Guingamp', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Gustav Svensson', 'pos': 'MF', 'age': 31, 'caps': '13', 'goals': '0', 'country': 'Sweden', 'club': 'Seattle Sounders FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Filip Helander', 'pos': 'DF', 'age': 25, 'caps': '4', 'goals': '0', 'country': 'Sweden', 'club': 'Bologna', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Oscar Hiljemark', 'pos': 'MF', 'age': 26, 'caps': '22', 'goals': '2', 'country': 'Sweden', 'club': 'Genoa', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Emil Krafth', 'pos': 'DF', 'age': 23, 'caps': '13', 'goals': '0', 'country': 'Sweden', 'club': 'Bologna', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Viktor Claesson', 'pos': 'MF', 'age': 26, 'caps': '22', 'goals': '3', 'country': 'Sweden', 'club': 'Krasnodar', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Pontus Jansson', 'pos': 'DF', 'age': 27, 'caps': '15', 'goals': '0', 'country': 'Sweden', 'club': 'Leeds United', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Marcus Rohdén', 'pos': 'MF', 'age': 27, 'caps': '12', 'goals': '1', 'country': 'Sweden', 'club': 'Crotone', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ola Toivonen', 'pos': 'FW', 'age': 31, 'caps': '59', 'goals': '13', 'country': 'Sweden', 'club': 'Toulouse', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jimmy Durmaz', 'pos': 'MF', 'age': 29, 'caps': '45', 'goals': '3', 'country': 'Sweden', 'club': 'Toulouse', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Isaac Kiese Thelin', 'pos': 'FW', 'age': 26, 'caps': '20', 'goals': '2', 'country': 'Sweden', 'club': 'Waasland-Beveren', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kristoffer Nordfeldt', 'pos': 'GK', 'age': 29, 'caps': '8', 'goals': '0', 'country': 'Sweden', 'club': 'Swansea City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Thibaut Courtois', 'pos': 'GK', 'age': 26, 'caps': '58', 'goals': '0', 'country': 'Belgium', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Toby Alderweireld', 'pos': 'DF', 'age': 29, 'caps': '77', 'goals': '3', 'country': 'Belgium', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Thomas Vermaelen', 'pos': 'DF', 'age': 32, 'caps': '66', 'goals': '1', 'country': 'Belgium', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Vincent Kompany', 'pos': 'DF', 'age': 32, 'caps': '77', 'goals': '4', 'country': 'Belgium', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jan Vertonghen', 'pos': 'DF', 'age': 31, 'caps': '102', 'goals': '8', 'country': 'Belgium', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Axel Witsel', 'pos': 'MF', 'age': 29, 'caps': '90', 'goals': '9', 'country': 'Belgium', 'club': 'Tianjin Quanjian', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Kevin De Bruyne', 'pos': 'MF', 'age': 27, 'caps': '62', 'goals': '14', 'country': 'Belgium', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marouane Fellaini', 'pos': 'MF', 'age': 30, 'caps': '82', 'goals': '17', 'country': 'Belgium', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Romelu Lukaku', 'pos': 'FW', 'age': 25, 'caps': '69', 'goals': '36', 'country': 'Belgium', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Eden Hazard', 'pos': 'FW', 'age': 27, 'caps': '86', 'goals': '22', 'country': 'Belgium', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Yannick Carrasco', 'pos': 'MF', 'age': 24, 'caps': '26', 'goals': '5', 'country': 'Belgium', 'club': 'Dalian Yifang', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Simon Mignolet', 'pos': 'GK', 'age': 30, 'caps': '21', 'goals': '0', 'country': 'Belgium', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Koen Casteels', 'pos': 'GK', 'age': 26, 'caps': '0', 'goals': '0', 'country': 'Belgium', 'club': 'VfL Wolfsburg', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Dries Mertens', 'pos': 'FW', 'age': 31, 'caps': '69', 'goals': '14', 'country': 'Belgium', 'club': 'Napoli', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Thomas Meunier', 'pos': 'DF', 'age': 26, 'caps': '25', 'goals': '5', 'country': 'Belgium', 'club': 'Paris Saint-Germain', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Thorgan Hazard', 'pos': 'MF', 'age': 25, 'caps': '11', 'goals': '1', 'country': 'Belgium', 'club': 'Borussia Mönchengladbach', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Youri Tielemans', 'pos': 'MF', 'age': 21, 'caps': '9', 'goals': '0', 'country': 'Belgium', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Adnan Januzaj', 'pos': 'FW', 'age': 23, 'caps': '8', 'goals': '0', 'country': 'Belgium', 'club': 'Real Sociedad', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mousa Dembélé', 'pos': 'MF', 'age': 30, 'caps': '76', 'goals': '5', 'country': 'Belgium', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Dedryck Boyata', 'pos': 'DF', 'age': 27, 'caps': '7', 'goals': '0', 'country': 'Belgium', 'club': 'Celtic', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Michy Batshuayi', 'pos': 'FW', 'age': 24, 'caps': '16', 'goals': '7', 'country': 'Belgium', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Nacer Chadli', 'pos': 'MF', 'age': 28, 'caps': '45', 'goals': '5', 'country': 'Belgium', 'club': 'West Bromwich Albion', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Leander Dendoncker', 'pos': 'DF', 'age': 23, 'caps': '5', 'goals': '0', 'country': 'Belgium', 'club': 'Anderlecht', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Jordan Pickford', 'pos': 'GK', 'age': 24, 'caps': '3', 'goals': '0', 'country': 'England', 'club': 'Everton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kyle Walker', 'pos': 'DF', 'age': 28, 'caps': '35', 'goals': '0', 'country': 'England', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Danny Rose', 'pos': 'DF', 'age': 27, 'caps': '18', 'goals': '0', 'country': 'England', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Eric Dier', 'pos': 'MF', 'age': 24, 'caps': '26', 'goals': '3', 'country': 'England', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'John Stones', 'pos': 'DF', 'age': 24, 'caps': '26', 'goals': '0', 'country': 'England', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Harry Maguire', 'pos': 'DF', 'age': 25, 'caps': '5', 'goals': '0', 'country': 'England', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jesse Lingard', 'pos': 'MF', 'age': 25, 'caps': '12', 'goals': '1', 'country': 'England', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jordan Henderson', 'pos': 'MF', 'age': 28, 'caps': '39', 'goals': '0', 'country': 'England', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Harry Kane', 'pos': 'FW', 'age': 24, 'caps': '24', 'goals': '13', 'country': 'England', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Raheem Sterling', 'pos': 'FW', 'age': 23, 'caps': '38', 'goals': '2', 'country': 'England', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jamie Vardy', 'pos': 'FW', 'age': 31, 'caps': '22', 'goals': '7', 'country': 'England', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kieran Trippier', 'pos': 'DF', 'age': 27, 'caps': '7', 'goals': '0', 'country': 'England', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jack Butland', 'pos': 'GK', 'age': 25, 'caps': '8', 'goals': '0', 'country': 'England', 'club': 'Stoke City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Danny Welbeck', 'pos': 'FW', 'age': 27, 'caps': '39', 'goals': '16', 'country': 'England', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Gary Cahill', 'pos': 'DF', 'age': 32, 'caps': '60', 'goals': '5', 'country': 'England', 'club': 'Chelsea', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Phil Jones', 'pos': 'DF', 'age': 26, 'caps': '25', 'goals': '0', 'country': 'England', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Fabian Delph', 'pos': 'DF', 'age': 28, 'caps': '11', 'goals': '0', 'country': 'England', 'club': 'Manchester City', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ashley Young', 'pos': 'DF', 'age': 32, 'caps': '34', 'goals': '7', 'country': 'England', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Marcus Rashford', 'pos': 'FW', 'age': 20, 'caps': '19', 'goals': '3', 'country': 'England', 'club': 'Manchester United', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Dele Alli', 'pos': 'MF', 'age': 22, 'caps': '25', 'goals': '2', 'country': 'England', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Ruben Loftus-Cheek', 'pos': 'MF', 'age': 22, 'caps': '4', 'goals': '0', 'country': 'England', 'club': 'Crystal Palace', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Trent Alexander-Arnold', 'pos': 'DF', 'age': 19, 'caps': '1', 'goals': '0', 'country': 'England', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Nick Pope', 'pos': 'GK', 'age': 26, 'caps': '1', 'goals': '0', 'country': 'England', 'club': 'Burnley', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jaime Penedo', 'pos': 'GK', 'age': 36, 'caps': '131', 'goals': '0', 'country': 'Panama', 'club': 'Dinamo București', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Michael Amir Murillo', 'pos': 'DF', 'age': 22, 'caps': '22', 'goals': '2', 'country': 'Panama', 'club': 'New York Red Bulls', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Harold Cummings', 'pos': 'DF', 'age': 26, 'caps': '52', 'goals': '0', 'country': 'Panama', 'club': 'San Jose Earthquakes', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Fidel Escobar', 'pos': 'DF', 'age': 23, 'caps': '23', 'goals': '1', 'country': 'Panama', 'club': 'New York Red Bulls', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Román Torres', 'pos': 'DF', 'age': 32, 'caps': '111', 'goals': '10', 'country': 'Panama', 'club': 'Seattle Sounders FC', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gabriel Gómez', 'pos': 'MF', 'age': 34, 'caps': '144', 'goals': '12', 'country': 'Panama', 'club': 'Atlético Bucaramanga', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Blas Pérez', 'pos': 'FW', 'age': 37, 'caps': '118', 'goals': '43', 'country': 'Panama', 'club': 'Municipal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Édgar Bárcenas', 'pos': 'MF', 'age': 24, 'caps': '29', 'goals': '0', 'country': 'Panama', 'club': 'Tapachula', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gabriel Torres', 'pos': 'FW', 'age': 29, 'caps': '72', 'goals': '14', 'country': 'Panama', 'club': 'Huachipato', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ismael Díaz', 'pos': 'FW', 'age': 21, 'caps': '11', 'goals': '2', 'country': 'Panama', 'club': 'Deportivo Fabril', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Armando Cooper', 'pos': 'MF', 'age': 30, 'caps': '98', 'goals': '7', 'country': 'Panama', 'club': 'Universidad de Chile', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'José Calderón', 'pos': 'GK', 'age': 32, 'caps': '31', 'goals': '0', 'country': 'Panama', 'club': 'Chorrillo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Adolfo Machado', 'pos': 'DF', 'age': 33, 'caps': '76', 'goals': '1', 'country': 'Panama', 'club': 'Houston Dynamo', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Valentín Pimentel', 'pos': 'MF', 'age': 27, 'caps': '23', 'goals': '1', 'country': 'Panama', 'club': 'Plaza Amador', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Erick Davis', 'pos': 'DF', 'age': 27, 'caps': '38', 'goals': '0', 'country': 'Panama', 'club': 'Dunajská Streda', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Abdiel Arroyo', 'pos': 'FW', 'age': 24, 'caps': '33', 'goals': '5', 'country': 'Panama', 'club': 'Alajuelense', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Luis Ovalle', 'pos': 'DF', 'age': 29, 'caps': '25', 'goals': '0', 'country': 'Panama', 'club': 'CD Olimpia', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Luis Tejada', 'pos': 'FW', 'age': 36, 'caps': '105', 'goals': '43', 'country': 'Panama', 'club': 'Sport Boys', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ricardo Ávila', 'pos': 'MF', 'age': 21, 'caps': '5', 'goals': '0', 'country': 'Panama', 'club': 'Gent', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aníbal Godoy', 'pos': 'MF', 'age': 28, 'caps': '88', 'goals': '1', 'country': 'Panama', 'club': 'San Jose Earthquakes', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'José Luis Rodríguez', 'pos': 'MF', 'age': 20, 'caps': '2', 'goals': '0', 'country': 'Panama', 'club': 'Gent', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Álex Rodríguez', 'pos': 'GK', 'age': 27, 'caps': '6', 'goals': '0', 'country': 'Panama', 'club': 'San Francisco', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Felipe Baloy', 'pos': 'DF', 'age': 37, 'caps': '102', 'goals': '3', 'country': 'Panama', 'club': 'Municipal', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Farouk Ben Mustapha', 'pos': 'GK', 'age': 29, 'caps': '15', 'goals': '0', 'country': 'Tunisia', 'club': 'Al-Shabab', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Syam Ben Youssef', 'pos': 'DF', 'age': 29, 'caps': '42', 'goals': '1', 'country': 'Tunisia', 'club': 'Kasımpaşa', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yohan Benalouane', 'pos': 'DF', 'age': 31, 'caps': '4', 'goals': '0', 'country': 'Tunisia', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Yassine Meriah', 'pos': 'DF', 'age': 24, 'caps': '16', 'goals': '1', 'country': 'Tunisia', 'club': 'CS Sfaxien', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Oussama Haddadi', 'pos': 'DF', 'age': 26, 'caps': '9', 'goals': '0', 'country': 'Tunisia', 'club': 'Dijon', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Rami Bedoui', 'pos': 'DF', 'age': 28, 'caps': '8', 'goals': '0', 'country': 'Tunisia', 'club': 'Étoile du Sahel', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Saîf-Eddine Khaoui', 'pos': 'FW', 'age': 23, 'caps': '5', 'goals': '0', 'country': 'Tunisia', 'club': 'Troyes', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Fakhreddine Ben Youssef', 'pos': 'FW', 'age': 27, 'caps': '39', 'goals': '5', 'country': 'Tunisia', 'club': 'Al-Ettifaq', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Anice Badri', 'pos': 'MF', 'age': 27, 'caps': '7', 'goals': '2', 'country': 'Tunisia', 'club': 'Espérance', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Wahbi Khazri', 'pos': 'FW', 'age': 27, 'caps': '35', 'goals': '12', 'country': 'Tunisia', 'club': 'Rennes', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Dylan Bronn', 'pos': 'DF', 'age': 23, 'caps': '5', 'goals': '0', 'country': 'Tunisia', 'club': 'Gent', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ali Maâloul', 'pos': 'DF', 'age': 28, 'caps': '46', 'goals': '0', 'country': 'Tunisia', 'club': 'Al Ahly', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ferjani Sassi', 'pos': 'MF', 'age': 26, 'caps': '39', 'goals': '3', 'country': 'Tunisia', 'club': 'Al-Nassr', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mohamed Amine Ben Amor', 'pos': 'MF', 'age': 26, 'caps': '26', 'goals': '1', 'country': 'Tunisia', 'club': 'Al-Ahli', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ahmed Khalil', 'pos': 'FW', 'age': 23, 'caps': '3', 'goals': '0', 'country': 'Tunisia', 'club': 'Club Africain', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Aymen Mathlouthi', 'pos': 'GK', 'age': 33, 'caps': '70', 'goals': '0', 'country': 'Tunisia', 'club': 'Al-Batin', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ellyes Skhiri', 'pos': 'MF', 'age': 23, 'caps': '5', 'goals': '0', 'country': 'Tunisia', 'club': 'Montpellier', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Bassem Srarfi', 'pos': 'FW', 'age': 21, 'caps': '5', 'goals': '0', 'country': 'Tunisia', 'club': 'Nice', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Saber Khalifa', 'pos': 'FW', 'age': 31, 'caps': '44', 'goals': '7', 'country': 'Tunisia', 'club': 'Club Africain', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Ghailene Chaalali', 'pos': 'FW', 'age': 24, 'caps': '6', 'goals': '1', 'country': 'Tunisia', 'club': 'Espérance', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Hamdi Nagguez', 'pos': 'DF', 'age': 25, 'caps': '15', 'goals': '0', 'country': 'Tunisia', 'club': 'Zamalek', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Mouez Hassen', 'pos': 'GK', 'age': 23, 'caps': '3', 'goals': '0', 'country': 'Tunisia', 'club': 'Châteauroux', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Naïm Sliti', 'pos': 'FW', 'age': 25, 'caps': '17', 'goals': '3', 'country': 'Tunisia', 'club': 'Dijon', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'David Ospina', 'pos': 'GK', 'age': 29, 'caps': '86', 'goals': '0', 'country': 'Colombia', 'club': 'Arsenal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Cristián Zapata', 'pos': 'DF', 'age': 31, 'caps': '55', 'goals': '2', 'country': 'Colombia', 'club': 'Milan', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Óscar Murillo', 'pos': 'DF', 'age': 30, 'caps': '13', 'goals': '0', 'country': 'Colombia', 'club': 'Pachuca', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Santiago Arias', 'pos': 'DF', 'age': 26, 'caps': '41', 'goals': '0', 'country': 'Colombia', 'club': 'PSV Eindhoven', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Wílmar Barrios', 'pos': 'MF', 'age': 24, 'caps': '10', 'goals': '0', 'country': 'Colombia', 'club': 'Boca Juniors', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Carlos Sánchez', 'pos': 'MF', 'age': 32, 'caps': '85', 'goals': '0', 'country': 'Colombia', 'club': 'Espanyol', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Carlos Bacca', 'pos': 'FW', 'age': 31, 'caps': '45', 'goals': '14', 'country': 'Colombia', 'club': 'Villarreal', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Abel Aguilar', 'pos': 'MF', 'age': 33, 'caps': '70', 'goals': '7', 'country': 'Colombia', 'club': 'Deportivo Cali', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Radamel Falcao', 'pos': 'FW', 'age': 32, 'caps': '73', 'goals': '29', 'country': 'Colombia', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'James Rodríguez', 'pos': 'MF', 'age': 26, 'caps': '63', 'goals': '21', 'country': 'Colombia', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Juan Cuadrado', 'pos': 'MF', 'age': 30, 'caps': '70', 'goals': '7', 'country': 'Colombia', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Camilo Vargas', 'pos': 'GK', 'age': 29, 'caps': '5', 'goals': '0', 'country': 'Colombia', 'club': 'Deportivo Cali', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yerry Mina', 'pos': 'DF', 'age': 23, 'caps': '12', 'goals': '3', 'country': 'Colombia', 'club': 'Barcelona', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Luis Muriel', 'pos': 'FW', 'age': 27, 'caps': '18', 'goals': '2', 'country': 'Colombia', 'club': 'Sevilla', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Mateus Uribe', 'pos': 'MF', 'age': 27, 'caps': '8', 'goals': '0', 'country': 'Colombia', 'club': 'América', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Jefferson Lerma', 'pos': 'MF', 'age': 23, 'caps': '5', 'goals': '0', 'country': 'Colombia', 'club': 'Levante', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Johan Mojica', 'pos': 'DF', 'age': 25, 'caps': '4', 'goals': '1', 'country': 'Colombia', 'club': 'Girona', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Farid Díaz', 'pos': 'DF', 'age': 34, 'caps': '13', 'goals': '0', 'country': 'Colombia', 'club': 'Olimpia', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Miguel Borja', 'pos': 'FW', 'age': 25, 'caps': '7', 'goals': '2', 'country': 'Colombia', 'club': 'Palmeiras', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Juan Fernando Quintero', 'pos': 'MF', 'age': 25, 'caps': '15', 'goals': '2', 'country': 'Colombia', 'club': 'River Plate', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'José Izquierdo', 'pos': 'FW', 'age': 25, 'caps': '5', 'goals': '1', 'country': 'Colombia', 'club': 'Brighton & Hove Albion', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'José Fernando Cuadrado', 'pos': 'GK', 'age': 33, 'caps': '1', 'goals': '0', 'country': 'Colombia', 'club': 'Once Caldas', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Dávinson Sánchez', 'pos': 'DF', 'age': 22, 'caps': '9', 'goals': '0', 'country': 'Colombia', 'club': 'Tottenham Hotspur', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Eiji Kawashima', 'pos': 'GK', 'age': 35, 'caps': '84', 'goals': '0', 'country': 'Japan', 'club': 'Metz', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Naomichi Ueda', 'pos': 'DF', 'age': 23, 'caps': '4', 'goals': '0', 'country': 'Japan', 'club': 'Kashima Antlers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gen Shoji', 'pos': 'DF', 'age': 25, 'caps': '11', 'goals': '1', 'country': 'Japan', 'club': 'Kashima Antlers', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Keisuke Honda', 'pos': 'MF', 'age': 32, 'caps': '95', 'goals': '36', 'country': 'Japan', 'club': 'Pachuca', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yuto Nagatomo', 'pos': 'DF', 'age': 31, 'caps': '105', 'goals': '3', 'country': 'Japan', 'club': 'Galatasaray', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Wataru Endo', 'pos': 'DF', 'age': 25, 'caps': '12', 'goals': '0', 'country': 'Japan', 'club': 'Urawa Red Diamonds', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gaku Shibasaki', 'pos': 'MF', 'age': 26, 'caps': '18', 'goals': '3', 'country': 'Japan', 'club': 'Getafe', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Genki Haraguchi', 'pos': 'MF', 'age': 27, 'caps': '33', 'goals': '6', 'country': 'Japan', 'club': 'Fortuna Düsseldorf', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Shinji Okazaki', 'pos': 'FW', 'age': 32, 'caps': '113', 'goals': '50', 'country': 'Japan', 'club': 'Leicester City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Shinji Kagawa', 'pos': 'MF', 'age': 29, 'caps': '92', 'goals': '30', 'country': 'Japan', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Takashi Usami', 'pos': 'MF', 'age': 26, 'caps': '24', 'goals': '3', 'country': 'Japan', 'club': 'Fortuna Düsseldorf', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Masaaki Higashiguchi', 'pos': 'GK', 'age': 32, 'caps': '5', 'goals': '0', 'country': 'Japan', 'club': 'Gamba Osaka', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Yoshinori Muto', 'pos': 'FW', 'age': 25, 'caps': '24', 'goals': '2', 'country': 'Japan', 'club': 'Mainz 05', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Takashi Inui', 'pos': 'MF', 'age': 30, 'caps': '27', 'goals': '4', 'country': 'Japan', 'club': 'Eibar', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Yuya Osako', 'pos': 'FW', 'age': 28, 'caps': '29', 'goals': '7', 'country': 'Japan', 'club': '1. FC Köln', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Hotaru Yamaguchi', 'pos': 'MF', 'age': 27, 'caps': '42', 'goals': '2', 'country': 'Japan', 'club': 'Cerezo Osaka', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Makoto Hasebe', 'pos': 'MF', 'age': 34, 'caps': '110', 'goals': '2', 'country': 'Japan', 'club': 'Eintracht Frankfurt', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ryota Oshima', 'pos': 'MF', 'age': 25, 'caps': '5', 'goals': '0', 'country': 'Japan', 'club': 'Kawasaki Frontale', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Hiroki Sakai', 'pos': 'DF', 'age': 28, 'caps': '43', 'goals': '0', 'country': 'Japan', 'club': 'Marseille', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Tomoaki Makino', 'pos': 'DF', 'age': 31, 'caps': '32', 'goals': '4', 'country': 'Japan', 'club': 'Urawa Red Diamonds', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Gōtoku Sakai', 'pos': 'DF', 'age': 27, 'caps': '41', 'goals': '0', 'country': 'Japan', 'club': 'Hamburger SV', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Maya Yoshida', 'pos': 'DF', 'age': 29, 'caps': '82', 'goals': '10', 'country': 'Japan', 'club': 'Southampton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kosuke Nakamura', 'pos': 'GK', 'age': 23, 'caps': '4', 'goals': '0', 'country': 'Japan', 'club': 'Kashiwa Reysol', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Wojciech Szczęsny', 'pos': 'GK', 'age': 28, 'caps': '35', 'goals': '0', 'country': 'Poland', 'club': 'Juventus', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Michał Pazdan', 'pos': 'DF', 'age': 30, 'caps': '33', 'goals': '0', 'country': 'Poland', 'club': 'Legia Warsaw', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Artur Jędrzejczyk', 'pos': 'DF', 'age': 30, 'caps': '36', 'goals': '3', 'country': 'Poland', 'club': 'Legia Warsaw', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Thiago Cionek', 'pos': 'DF', 'age': 32, 'caps': '19', 'goals': '0', 'country': 'Poland', 'club': 'SPAL', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jan Bednarek', 'pos': 'DF', 'age': 22, 'caps': '3', 'goals': '0', 'country': 'Poland', 'club': 'Southampton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Jacek Góralski', 'pos': 'MF', 'age': 25, 'caps': '5', 'goals': '0', 'country': 'Poland', 'club': 'Ludogorets Razgrad', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Arkadiusz Milik', 'pos': 'FW', 'age': 24, 'caps': '40', 'goals': '12', 'country': 'Poland', 'club': 'Napoli', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Karol Linetty', 'pos': 'MF', 'age': 23, 'caps': '20', 'goals': '1', 'country': 'Poland', 'club': 'Sampdoria', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Robert Lewandowski', 'pos': 'FW', 'age': 29, 'caps': '95', 'goals': '55', 'country': 'Poland', 'club': 'Bayern Munich', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Grzegorz Krychowiak', 'pos': 'MF', 'age': 28, 'caps': '51', 'goals': '2', 'country': 'Poland', 'club': 'West Bromwich Albion', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kamil Grosicki', 'pos': 'MF', 'age': 30, 'caps': '57', 'goals': '12', 'country': 'Poland', 'club': 'Hull City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Bartosz Białkowski', 'pos': 'GK', 'age': 30, 'caps': '1', 'goals': '0', 'country': 'Poland', 'club': 'Ipswich Town', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Maciej Rybus', 'pos': 'MF', 'age': 28, 'caps': '51', 'goals': '2', 'country': 'Poland', 'club': 'Lokomotiv Moscow', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Łukasz Teodorczyk', 'pos': 'FW', 'age': 27, 'caps': '17', 'goals': '4', 'country': 'Poland', 'club': 'Anderlecht', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Kamil Glik', 'pos': 'DF', 'age': 30, 'caps': '57', 'goals': '4', 'country': 'Poland', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Jakub Błaszczykowski', 'pos': 'MF', 'age': 32, 'caps': '99', 'goals': '20', 'country': 'Poland', 'club': 'VfL Wolfsburg', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Sławomir Peszko', 'pos': 'MF', 'age': 33, 'caps': '43', 'goals': '2', 'country': 'Poland', 'club': 'Lechia Gdańsk', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Bartosz Bereszyński', 'pos': 'DF', 'age': 25, 'caps': '8', 'goals': '0', 'country': 'Poland', 'club': 'Sampdoria', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Piotr Zieliński', 'pos': 'MF', 'age': 24, 'caps': '33', 'goals': '5', 'country': 'Poland', 'club': 'Napoli', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Łukasz Piszczek', 'pos': 'DF', 'age': 33, 'caps': '63', 'goals': '3', 'country': 'Poland', 'club': 'Borussia Dortmund', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Rafał Kurzawa', 'pos': 'MF', 'age': 25, 'caps': '3', 'goals': '0', 'country': 'Poland', 'club': 'Górnik Zabrze', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Łukasz Fabiański', 'pos': 'GK', 'age': 33, 'caps': '45', 'goals': '0', 'country': 'Poland', 'club': 'Swansea City', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Dawid Kownacki', 'pos': 'FW', 'age': 21, 'caps': '2', 'goals': '1', 'country': 'Poland', 'club': 'Sampdoria', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Abdoulaye Diallo', 'pos': 'GK', 'age': 26, 'caps': '17', 'goals': '0', 'country': 'Senegal', 'club': 'Rennes', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Adama Mbengue', 'pos': 'DF', 'age': 24, 'caps': '6', 'goals': '0', 'country': 'Senegal', 'club': 'Caen', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Kalidou Koulibaly', 'pos': 'DF', 'age': 27, 'caps': '26', 'goals': '0', 'country': 'Senegal', 'club': 'Napoli', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Kara Mbodji', 'pos': 'DF', 'age': 28, 'caps': '52', 'goals': '5', 'country': 'Senegal', 'club': 'Anderlecht', 'Top League': 0, 'CL League 2017-2018': 1}, {'name': 'Idrissa Gueye', 'pos': 'MF', 'age': 28, 'caps': '61', 'goals': '1', 'country': 'Senegal', 'club': 'Everton', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Salif Sané', 'pos': 'MF', 'age': 27, 'caps': '22', 'goals': '0', 'country': 'Senegal', 'club': 'Hannover 96', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Moussa Sow', 'pos': 'FW', 'age': 32, 'caps': '51', 'goals': '18', 'country': 'Senegal', 'club': 'Bursaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Cheikhou Kouyaté', 'pos': 'MF', 'age': 28, 'caps': '48', 'goals': '2', 'country': 'Senegal', 'club': 'West Ham United', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Mame Biram Diouf', 'pos': 'FW', 'age': 30, 'caps': '49', 'goals': '10', 'country': 'Senegal', 'club': 'Stoke City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Sadio Mané', 'pos': 'FW', 'age': 26, 'caps': '53', 'goals': '14', 'country': 'Senegal', 'club': 'Liverpool', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': "Cheikh N'Doye", 'pos': 'MF', 'age': 32, 'caps': '26', 'goals': '3', 'country': 'Senegal', 'club': 'Birmingham City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Youssouf Sabaly', 'pos': 'DF', 'age': 25, 'caps': '5', 'goals': '0', 'country': 'Senegal', 'club': 'Bordeaux', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': "Alfred N'Diaye", 'pos': 'MF', 'age': 28, 'caps': '21', 'goals': '0', 'country': 'Senegal', 'club': 'Wolverhampton Wanderers', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Moussa Konaté', 'pos': 'FW', 'age': 25, 'caps': '28', 'goals': '10', 'country': 'Senegal', 'club': 'Amiens', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Diafra Sakho', 'pos': 'FW', 'age': 28, 'caps': '12', 'goals': '3', 'country': 'Senegal', 'club': 'Rennes', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': "Khadim N'Diaye", 'pos': 'GK', 'age': 33, 'caps': '26', 'goals': '0', 'country': 'Senegal', 'club': 'Horoya', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Badou Ndiaye', 'pos': 'MF', 'age': 27, 'caps': '20', 'goals': '1', 'country': 'Senegal', 'club': 'Stoke City', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Ismaïla Sarr', 'pos': 'FW', 'age': 20, 'caps': '16', 'goals': '3', 'country': 'Senegal', 'club': 'Rennes', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': "M'Baye Niang", 'pos': 'FW', 'age': 23, 'caps': '7', 'goals': '0', 'country': 'Senegal', 'club': 'Torino', 'Top League': 1, 'CL League 2017-2018': 0}, {'name': 'Keita Baldé', 'pos': 'FW', 'age': 23, 'caps': '19', 'goals': '3', 'country': 'Senegal', 'club': 'Monaco', 'Top League': 1, 'CL League 2017-2018': 1}, {'name': 'Lamine Gassama', 'pos': 'DF', 'age': 28, 'caps': '36', 'goals': '0', 'country': 'Senegal', 'club': 'Alanyaspor', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Moussa Wagué', 'pos': 'DF', 'age': 19, 'caps': '10', 'goals': '0', 'country': 'Senegal', 'club': 'Eupen', 'Top League': 0, 'CL League 2017-2018': 0}, {'name': 'Alfred Gomis', 'pos': 'GK', 'age': 24, 'caps': '1', 'goals': '0', 'country': 'Senegal', 'club': 'SPAL', 'Top League': 1, 'CL League 2017-2018': 0}]
    ['Egypt', 'Russia', 'Saudi Arabia', 'Uruguay', 'Iran', 'Morocco', 'Portugal', 'Spain', 'Australia', 'Denmark', 'France', 'Peru', 'Argentina', 'Croatia', 'Iceland', 'Nigeria', 'Brazil', 'Costa Rica', 'Serbia', 'Switzerland', 'Germany', 'Mexico', 'South Korea', 'Sweden', 'Belgium', 'England', 'Panama', 'Tunisia', 'Colombia', 'Japan', 'Poland', 'Senegal']




```
# put the squad data and nations participating on a data frame to use in later analysis

# players squads in details and their respective information
df_players = pd.DataFrame(starlist)
display(df_players.head())

# All the national teams participating in the world cup- used frequently throughout our worl
df_countries = pd.DataFrame(countrylist,columns = ['Nations Participating'])
display(df_countries.head())

cup2018_players = df_players['name']

list_of_players=df_players['name']
```



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
      <th>CL League 2017-2018</th>
      <th>Top League</th>
      <th>age</th>
      <th>caps</th>
      <th>club</th>
      <th>country</th>
      <th>goals</th>
      <th>name</th>
      <th>pos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>45</td>
      <td>158</td>
      <td>Al-Taawoun</td>
      <td>Egypt</td>
      <td>0</td>
      <td>Essam El-Hadary</td>
      <td>GK</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>1</td>
      <td>29</td>
      <td>21</td>
      <td>West Bromwich Albion</td>
      <td>Egypt</td>
      <td>1</td>
      <td>Ali Gabr</td>
      <td>DF</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>1</td>
      <td>30</td>
      <td>78</td>
      <td>Aston Villa</td>
      <td>Egypt</td>
      <td>2</td>
      <td>Ahmed Elmohamady</td>
      <td>DF</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>26</td>
      <td>24</td>
      <td>Los Angeles FC</td>
      <td>Egypt</td>
      <td>0</td>
      <td>Omar Gaber</td>
      <td>MF</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>1</td>
      <td>26</td>
      <td>5</td>
      <td>Wigan Athletic</td>
      <td>Egypt</td>
      <td>0</td>
      <td>Sam Morsy</td>
      <td>MF</td>
    </tr>
  </tbody>
</table>
</div>



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
      <th>Nations Participating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Egypt</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Russia</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Saudi Arabia</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Iran</td>
    </tr>
  </tbody>
</table>
</div>


###First List of Predictors 

First Predictiors includes : 

1- Goals: Number of Goals scored by each team

2- Age: Average age of each team

3- Caps: Average number of Caps per team

4- Top League: Number of Players playing in the top 5 soccer league in the world 

5- CL League 17-18: Number of players in the national that played in the champions league 2017-2018



```
# make a pivot table of the averages of all the squads  ages caps and goals and sum the other columns
df_players['caps'] = pd.to_numeric(df_players['caps'])
df_players['goals'] = pd.to_numeric(df_players['goals'])
players_features = pd.pivot_table(df_players,index='country',values=['age','caps','Top League','CL League 2017-2018','goals'], aggfunc={'age':'mean','caps':'mean','Top League':'sum','CL League 2017-2018':'sum','goals':'sum'}) # 'Top League'
players_features['country']=players_features.index
players_features.head()
```





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
      <th>CL League 2017-2018</th>
      <th>Top League</th>
      <th>age</th>
      <th>caps</th>
      <th>goals</th>
      <th>country</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Argentina</th>
      <td>14</td>
      <td>14</td>
      <td>29.130435</td>
      <td>37.347826</td>
      <td>172</td>
      <td>Argentina</td>
    </tr>
    <tr>
      <th>Australia</th>
      <td>2</td>
      <td>9</td>
      <td>27.608696</td>
      <td>29.826087</td>
      <td>121</td>
      <td>Australia</td>
    </tr>
    <tr>
      <th>Belgium</th>
      <td>17</td>
      <td>19</td>
      <td>27.217391</td>
      <td>47.260870</td>
      <td>156</td>
      <td>Belgium</td>
    </tr>
    <tr>
      <th>Brazil</th>
      <td>18</td>
      <td>17</td>
      <td>28.130435</td>
      <td>29.869565</td>
      <td>127</td>
      <td>Brazil</td>
    </tr>
    <tr>
      <th>Colombia</th>
      <td>6</td>
      <td>13</td>
      <td>27.913043</td>
      <td>31.000000</td>
      <td>91</td>
      <td>Colombia</td>
    </tr>
  </tbody>
</table>
</div>



##Cleaning and Preparation of Training data set 

In this section: 

- We import the dataset from Kaggle of all the games; 
- We filtered the data to only include the national team  qualified for the world Cup 2018
- We Clean the data






```
# wolrdcup1 fifa international results
response = requests.get('https://drive.google.com/uc?export=download&id=1p9aepCMiZWyA9eQYuUl3gDV51kYVqMDj')
df = pd.read_csv(BytesIO(response.content))
df.head()
```





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
      <th>date</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>home_score</th>
      <th>away_score</th>
      <th>tournament</th>
      <th>city</th>
      <th>country</th>
      <th>neutral</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1872-11-30</td>
      <td>Scotland</td>
      <td>England</td>
      <td>0</td>
      <td>0</td>
      <td>Friendly</td>
      <td>Glasgow</td>
      <td>Scotland</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1873-03-08</td>
      <td>England</td>
      <td>Scotland</td>
      <td>4</td>
      <td>2</td>
      <td>Friendly</td>
      <td>London</td>
      <td>England</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1874-03-07</td>
      <td>Scotland</td>
      <td>England</td>
      <td>2</td>
      <td>1</td>
      <td>Friendly</td>
      <td>Glasgow</td>
      <td>Scotland</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1875-03-06</td>
      <td>England</td>
      <td>Scotland</td>
      <td>2</td>
      <td>2</td>
      <td>Friendly</td>
      <td>London</td>
      <td>England</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1876-03-04</td>
      <td>Scotland</td>
      <td>England</td>
      <td>3</td>
      <td>0</td>
      <td>Friendly</td>
      <td>Glasgow</td>
      <td>Scotland</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>





```
print(df.shape)
df=df.drop(["city"],axis=1)
```


    (39638, 9)




```
df.describe()
```





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
      <th>home_score</th>
      <th>away_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>39638.000000</td>
      <td>39638.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1.748221</td>
      <td>1.187749</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.747294</td>
      <td>1.400002</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>31.000000</td>
      <td>21.000000</td>
    </tr>
  </tbody>
</table>
</div>





```

# Add winnning team column and goal difference
winner=[]
for i in range(len(df['home_team'])):
  if df['home_score'][i] > df['away_score'][i]:
    winner.append(df['home_team'][i])
  elif df['home_score'][i] < df['away_score'][i]:
    winner.append(df['away_team'][i])
  else:
    winner.append('Draw')
df['winning_team']=winner
df['goal_difference']=np.absolute(df['home_score']-df['away_score'])
df.head()

```





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
      <th>date</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>home_score</th>
      <th>away_score</th>
      <th>tournament</th>
      <th>country</th>
      <th>neutral</th>
      <th>winning_team</th>
      <th>goal_difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1872-11-30</td>
      <td>Scotland</td>
      <td>England</td>
      <td>0</td>
      <td>0</td>
      <td>Friendly</td>
      <td>Scotland</td>
      <td>False</td>
      <td>Draw</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1873-03-08</td>
      <td>England</td>
      <td>Scotland</td>
      <td>4</td>
      <td>2</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1874-03-07</td>
      <td>Scotland</td>
      <td>England</td>
      <td>2</td>
      <td>1</td>
      <td>Friendly</td>
      <td>Scotland</td>
      <td>False</td>
      <td>Scotland</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1875-03-06</td>
      <td>England</td>
      <td>Scotland</td>
      <td>2</td>
      <td>2</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>Draw</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1876-03-04</td>
      <td>Scotland</td>
      <td>England</td>
      <td>3</td>
      <td>0</td>
      <td>Friendly</td>
      <td>Scotland</td>
      <td>False</td>
      <td>Scotland</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>





```
# get all historical matches of the countries participating in 2018 world cup

df_natteam_home=df[df['home_team'].isin(countrylist)]
df_natteam_away=df[df['away_team'].isin(countrylist)]
df=pd.concat((df_natteam_home,df_natteam_away))
# df.drop_duplicates()
display(df.shape)
df.head()
```



    (20375, 10)





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
      <th>date</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>home_score</th>
      <th>away_score</th>
      <th>tournament</th>
      <th>country</th>
      <th>neutral</th>
      <th>winning_team</th>
      <th>goal_difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>1873-03-08</td>
      <td>England</td>
      <td>Scotland</td>
      <td>4</td>
      <td>2</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1875-03-06</td>
      <td>England</td>
      <td>Scotland</td>
      <td>2</td>
      <td>2</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>Draw</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1877-03-03</td>
      <td>England</td>
      <td>Scotland</td>
      <td>1</td>
      <td>3</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>Scotland</td>
      <td>2</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1879-01-18</td>
      <td>England</td>
      <td>Wales</td>
      <td>2</td>
      <td>1</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1879-04-05</td>
      <td>England</td>
      <td>Scotland</td>
      <td>5</td>
      <td>4</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>





```
# Create column of the year of every match- to be used for later analysis
year=[]
for n in df['date']:
  year.append(int(n[:4]))
df['match_year']=year

display(df.head())

```



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
      <th>date</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>home_score</th>
      <th>away_score</th>
      <th>tournament</th>
      <th>country</th>
      <th>neutral</th>
      <th>winning_team</th>
      <th>goal_difference</th>
      <th>match_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>1873-03-08</td>
      <td>England</td>
      <td>Scotland</td>
      <td>4</td>
      <td>2</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>2</td>
      <td>1873</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1875-03-06</td>
      <td>England</td>
      <td>Scotland</td>
      <td>2</td>
      <td>2</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>Draw</td>
      <td>0</td>
      <td>1875</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1877-03-03</td>
      <td>England</td>
      <td>Scotland</td>
      <td>1</td>
      <td>3</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>Scotland</td>
      <td>2</td>
      <td>1877</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1879-01-18</td>
      <td>England</td>
      <td>Wales</td>
      <td>2</td>
      <td>1</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>1</td>
      <td>1879</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1879-04-05</td>
      <td>England</td>
      <td>Scotland</td>
      <td>5</td>
      <td>4</td>
      <td>Friendly</td>
      <td>England</td>
      <td>False</td>
      <td>England</td>
      <td>1</td>
      <td>1879</td>
    </tr>
  </tbody>
</table>
</div>




```
df.shape
```





    (20375, 11)





```

# get all international matches of the teams of the world cup since 1990
df=df[df.match_year>=1990]
display(df.head())
display(df.shape)
```



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
      <th>date</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>home_score</th>
      <th>away_score</th>
      <th>tournament</th>
      <th>country</th>
      <th>neutral</th>
      <th>winning_team</th>
      <th>goal_difference</th>
      <th>match_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>15795</th>
      <td>1990-01-17</td>
      <td>Mexico</td>
      <td>Argentina</td>
      <td>2</td>
      <td>0</td>
      <td>Friendly</td>
      <td>USA</td>
      <td>True</td>
      <td>Mexico</td>
      <td>2</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15802</th>
      <td>1990-01-24</td>
      <td>France</td>
      <td>German DR</td>
      <td>3</td>
      <td>0</td>
      <td>Friendly</td>
      <td>Kuwait</td>
      <td>True</td>
      <td>France</td>
      <td>3</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15804</th>
      <td>1990-01-25</td>
      <td>Nigeria</td>
      <td>Ivory Coast</td>
      <td>2</td>
      <td>0</td>
      <td>Friendly</td>
      <td>Nigeria</td>
      <td>False</td>
      <td>Nigeria</td>
      <td>2</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15809</th>
      <td>1990-01-28</td>
      <td>Nigeria</td>
      <td>Senegal</td>
      <td>1</td>
      <td>1</td>
      <td>Friendly</td>
      <td>Nigeria</td>
      <td>False</td>
      <td>Draw</td>
      <td>0</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15811</th>
      <td>1990-02-02</td>
      <td>Colombia</td>
      <td>Uruguay</td>
      <td>0</td>
      <td>2</td>
      <td>Friendly</td>
      <td>USA</td>
      <td>True</td>
      <td>Uruguay</td>
      <td>2</td>
      <td>1990</td>
    </tr>
  </tbody>
</table>
</div>



    (10580, 11)




```
df_natteam=df[df['home_team'].isin(countrylist)& df['away_team'].isin(countrylist)]
print(df_natteam.shape)
df_natteam.head()
```


    (3564, 11)





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
      <th>date</th>
      <th>home_team</th>
      <th>away_team</th>
      <th>home_score</th>
      <th>away_score</th>
      <th>tournament</th>
      <th>country</th>
      <th>neutral</th>
      <th>winning_team</th>
      <th>goal_difference</th>
      <th>match_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>15795</th>
      <td>1990-01-17</td>
      <td>Mexico</td>
      <td>Argentina</td>
      <td>2</td>
      <td>0</td>
      <td>Friendly</td>
      <td>USA</td>
      <td>True</td>
      <td>Mexico</td>
      <td>2</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15809</th>
      <td>1990-01-28</td>
      <td>Nigeria</td>
      <td>Senegal</td>
      <td>1</td>
      <td>1</td>
      <td>Friendly</td>
      <td>Nigeria</td>
      <td>False</td>
      <td>Draw</td>
      <td>0</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15811</th>
      <td>1990-02-02</td>
      <td>Colombia</td>
      <td>Uruguay</td>
      <td>0</td>
      <td>2</td>
      <td>Friendly</td>
      <td>USA</td>
      <td>True</td>
      <td>Uruguay</td>
      <td>2</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15813</th>
      <td>1990-02-02</td>
      <td>Iran</td>
      <td>Poland</td>
      <td>0</td>
      <td>2</td>
      <td>Friendly</td>
      <td>Iran</td>
      <td>False</td>
      <td>Poland</td>
      <td>2</td>
      <td>1990</td>
    </tr>
    <tr>
      <th>15816</th>
      <td>1990-02-04</td>
      <td>Costa Rica</td>
      <td>Uruguay</td>
      <td>0</td>
      <td>2</td>
      <td>Friendly</td>
      <td>USA</td>
      <td>True</td>
      <td>Uruguay</td>
      <td>2</td>
      <td>1990</td>
    </tr>
  </tbody>
</table>
</div>





```
# Show distribution of goal differences in the international matches

# fig, axes = plt.subplots(2, 1)
plt.hist(df_natteam['goal_difference'],bins=100)
plt.xlabel("number of goals")
plt.ylabel("count")
plt.title("goal difference in all international matches")
# df.hist('h_draw',bins=100, ax=axes[0,1])
```





    Text(0.5,1,'goal difference in all international matches')




![png](milst4_files/milst4_19_1.png)




```
# Show the distribution of the most played international tournaments
df_natteam['tournament'].value_counts()[:8].plot(kind='barh')
plt.xlabel("Number of matches")
```





    Text(0.5,0,'Number of matches')




![png](milst4_files/milst4_20_1.png)




```
# show the countries with the most wins since 1990 for all these international games
df_natteam['winning_team'].value_counts()[1:11].plot(kind='barh')
plt.xlabel("Winnings")
plt.title("The 10 most successful countries in internataional matches since 1990")

```





    Text(0.5,1,'The 10 most successful countries in internataional matches since 1990')




![png](milst4_files/milst4_21_1.png)




```
#examine the perfromance of the squads when they play at home
home_win = df_natteam[df_natteam.home_score > df_natteam.away_score]
away_win = df_natteam[df_natteam.home_score < df_natteam.away_score]
draw_df = df_natteam[df_natteam.home_score == df_natteam.away_score]

win=home_win.shape[0]/df_natteam.shape[0]*100
draw=draw_df.shape[0]/df_natteam.shape[0]*100
lose=(df_natteam.shape[0]-home_win.shape[0]-draw_df.shape[0])/df_natteam.shape[0]*100


objects = ('Win', 'Draw', 'Lose')
y_pos = np.arange(len(objects))
performance = [win,draw,lose]
plt.bar(y_pos, performance, align='center', alpha=0.4)
plt.xticks(y_pos, objects)
plt.ylabel('Percentage (%)')
plt.title('Performance of Home Team')
```





    Text(0.5,1,'Performance of Home Team')




![png](milst4_files/milst4_22_1.png)


### Create the traininig set



```
# Create x and y train
x_train = df_natteam[['home_team','away_team']]
y_train = df_natteam['winning_team']

```


### we perform k-fold cross validation to select the best parameters for each model based on some criteria.

## Cleaning and Preparation of the Test data set 

In this section: 

- We scrap the information about the 64 games played in the World Cup 2018 and thier outcome 
- We separate the group phase games and the knockout phase games 



```
# scrape and create our test set

fifa_page=requests.get("https://fixturedownload.com/results/fifa-world-cup-2018")
ranking_soup = BeautifulSoup(fifa_page.text, "html.parser")
match_table = ranking_soup.find('tbody')
rows_match = match_table.findAll('tr')

home_country = []
score = []
away_country = []
round_number = []
for row in rows_match:
    tds = row.findAll('td')
    
    round_number.append(tds[0].text)
    
    home_country.append(tds[3].text)
    
    away_country.append(tds[4].text)
    
    score.append(tds[6].text)
```




```
winner = []
k = 0
for value in score:
    game_result = value.split(' ')
    if int(game_result[0]) > int(game_result[-1]):
        winner.append(home_country[k])
    elif int(game_result[0]) == int(game_result[-1]):
        winner.append('Draw')
    else:
        winner.append(away_country[k])
    k +=1

df_test = pd.DataFrame({'round_number':round_number,'home_team':home_country,'away_team':away_country,'winning_team':winner})
df_test = df_test.replace({"Korea Republic":"South Korea","IR Iran":"Iran"})
df_test['phase'] = (df_test['round_number'] == '1') | (df_test['round_number'] == '2') | (df_test['round_number'] == '3')

phase_1 = df_test[df_test['phase']== True]
phase_2 = df_test[df_test['phase']== False]

# all the phases untill apart of knockout phase
x_test1 = phase_1[['home_team','away_team']]

# only the knockout phase matches
x_test2 = phase_2[['home_team','away_team']]

# all the winning teams untill apart of knockout phase
y_test1 = phase_1['winning_team']

# winning teams of the knockout phase
y_test2 = phase_2['winning_team']

y_test1.head()
```





    0     Russia
    1    Uruguay
    2       Iran
    3       Draw
    4     France
    Name: winning_team, dtype: object





```
import warnings


# the scraped data show a draw when a game is prolonged in the knockout phase to 120min/penalties. This is a solution to get the right winners of the knocout phase:
draws = y_test2 == 'Draw'
idx = y_test2[draws].index.values
for index in idx:
  team1 = x_test2['away_team'].loc[index]
  team2 = x_test2['home_team'].loc[index]
  team1_progress = ((x_test2['away_team'].loc[index+1:]==team1).any() or (x_test2['home_team'].loc[index+1:]==team1).any())
  if team1_progress:
    y_test2.loc[index] = team1
  else:
    y_test2.loc[index] = team2
# winning teams of the knocout phase with the winning teams after 120min/penalties-if occured    
display(y_test2)

warnings.filterwarnings("ignore")
```



    48     France
    49    Uruguay
    50     Russia
    51    Croatia
    52     Brazil
    53    Belgium
    54     Sweden
    55    England
    56     France
    57    Belgium
    58    England
    59    Croatia
    60     France
    61    Croatia
    62    Belgium
    63     France
    Name: winning_team, dtype: object




```
# do a hack on the knockout phase if there is a draw result
def knockout_test(model,x):
  # condition so that neural network could work also with this function
  if isinstance(model,(Sequential,)):
    ypred = model.predict(x)
  # the rest of the models can use probability
  else:
    ypred = model.predict_proba(x)
    
  winners = np.argmax(ypred,axis=1)
  draw = np.argmax(ypred,axis=1)==0
  # take care of an occurence of a draw predicted result- the hack
  if draw.any():
    draw_winners = np.argmax(ypred[draw,1:])+1
    winners[draw]=draw_winners
  
  return winners

```


##Feature engineering of baseline model

In this section: 

*   We built the feature by retrieving the outcome of the last 20 games played
*   We scraped the information about each of the coaches' salary 
* We retrieve the FIFA Ranking before the beginning of the World Cup 2018 



###FIFA rankings- the most important feature of our baseline model



```
# scrape the Fifa rankings from the fifa.com website as of the seventh of june 2018- Just before the world cup started
# https://www.fifa.com/fifa-world-ranking/ranking-table/men/rank/id12210/#all
fifa_page=requests.get("https://www.fifa.com/fifa-world-ranking/ranking-table/men/rank/id12210/#all")

ranking_soup = BeautifulSoup(fifa_page.text, "html.parser")

ranking_table = ranking_soup.findAll("tbody")

ranking_list=[]
        
for rank in ranking_table:
  ranked_countries = rank.findAll('tr')
  
  for c in ranked_countries:
    di = dict()
    
    ranking = c.find('td',attrs={'class':"fi-table__td fi-table__rank"}).text
    di['ranking'] = ranking
    di['country_ranked_06/18']=c.find('a',attrs={'class':"fi-t__link"}).find('span',attrs={'class':"fi-t__nText"}).text
    ranking_list.append(di)
raw_df_ranking = pd.DataFrame(ranking_list)
#south korea and Iran don't apear in the same name in ranking and country list
raw_df_ranking = raw_df_ranking.replace({"Korea Republic":"South Korea","IR Iran":"Iran"})

df_ranking=raw_df_ranking[raw_df_ranking['country_ranked_06/18'].isin(countrylist)]

df_ranking['ranking'] = pd.to_numeric(df_ranking['ranking'])
df_ranking.head()
```





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
      <th>country_ranked_06/18</th>
      <th>ranking</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Germany</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Brazil</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Belgium</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Portugal</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Argentina</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>





```
#get the rankings of only the squads that participated in the 2018 world cup
df_ranking=df_ranking[df_ranking['country_ranked_06/18'].isin(countrylist)]

df_ranking.set_index("country_ranked_06/18")
df_ranking = df_ranking.rename(columns={'country_ranked_06/18': 'country'})
df_ranking.set_index('country')
df_ranking.head()
```





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
      <th>country</th>
      <th>ranking</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Germany</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Brazil</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Belgium</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Portugal</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Argentina</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



#### Feature of the last 20 matches performance before the world cup



```
# get the 20 most recent performance and later make it a predictor
winning_teamlist = df_natteam['winning_team'].unique()

number_wins = []
for team in countrylist:
    index_played = df_natteam[(df_natteam['home_team']==team) | (df_natteam['away_team']==team)][-20:]
    won_games = index_played[index_played['winning_team']==team].shape[0]
    number_wins.append({'country':team,'# won':won_games})

df_wins = pd.DataFrame(number_wins)
df_wins = df_wins.set_index('country')
df_wins['country']=df_wins.index
df_wins.head()
```





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
      <th># won</th>
      <th>country</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Egypt</th>
      <td>6</td>
      <td>Egypt</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>5</td>
      <td>Russia</td>
    </tr>
    <tr>
      <th>Saudi Arabia</th>
      <td>2</td>
      <td>Saudi Arabia</td>
    </tr>
    <tr>
      <th>Uruguay</th>
      <td>7</td>
      <td>Uruguay</td>
    </tr>
    <tr>
      <th>Iran</th>
      <td>4</td>
      <td>Iran</td>
    </tr>
  </tbody>
</table>
</div>



#### National squad Coach salary feature



```
# Function to get the Original Country of Coach and his/her age 
import datetime 
import time
now = datetime.datetime.now()  # Import the datetime  library 

def coach_info(url):
    page = requests.get(url)
    soup = BeautifulSoup(page.text, "html.parser")  # Parse the passed URL 
    
    
    nodes = soup.select('div.fi-p__profile-text--uppercase')  # Select the div necessary for scrapping values
    age = now.year - int(nodes[0].find('span').text.strip()[-4:])  # Extract the age of the coach 
    nationality = nodes[1].find('span').text.strip()  # extract the nationality of the coach 
    return (age, nationality)
```




```
# Get the list for all coaches for the world cup, their age, original country 


ROOT = "https://www.fifa.com"

coach_page=requests.get("https://www.fifa.com/worldcup/players/coaches/") # Get URL from the coaches list
coach_soup = BeautifulSoup(coach_page.text, "html.parser") # Parse the page.text in Beautiful Soup

salary_page = requests.get("http://www.footballwood.in/salary-every-coach-in-2018-fifa-world-cup.html") # URL to get salary for coaches
salary_soup = BeautifulSoup(salary_page.text, "html.parser")

# Get list of coaches salary and their country 
coach_dict = {}
salary_nodes = salary_soup.findAll('tr')
for i in range(1,len(salary_nodes)):
    pays = salary_nodes[i].findNext('td').findNext('td').findNext('td').text
    if pays == 'Iran': pays = 'IR Iran'
    if pays == 'South Korea': pays = 'Korea Republic'
    salaire = salary_nodes[i].findNext('td').findNext('td').findNext('td').findNext('td').text.strip('£')   
    if salaire[-1] == 'm': 
        salary = float(salaire.strip('m')) * 1000000
    else:
        salary = float(salaire.replace(',', ''))
    coach_dict[pays] = salary
 
    
#ranking_table = ranking_soup.findAll("tbody")

def parse_coach (soup: BeautifulSoup):
    
    coachlist = []  #List to save the coaches
    teamlist = []  #list of save the team
    
    coach_nodes = soup.select('div.fi-p__name')   # Narrow to div containing the coach names
    team_nodes = soup.select('div.fi-p__country') # Narrow to div contaning the coach's team 
    detail_nodes = soup.select('div.col-sm-3')    # Get the href for the Coach information
    for i in range(len(coach_nodes)):
        dictionaire = {}  # dictionaire to save the coach and the team he/she corresponds to
        dictionaire['country'] = team_nodes[i].text.strip() # Get the country name for the coach 
        dictionaire['coach'] = coach_nodes[i].text.strip() # Get the coach corresponding to the country
        dictionaire['coach age'], dictionaire['nationality'] = coach_info(ROOT+detail_nodes[i].find('a').attrs['href']) # Get age and nationality of the coach 
        dictionaire['Coach Salary'] = coach_dict[team_nodes[i].text.strip()]
#         time.sleep(2)
        coachlist.append(dictionaire)
        
    return coachlist
```




```
df_coaches = pd.DataFrame(parse_coach(coach_soup))  # Convert coach list into dataframe
df_coaches['country'] = df_coaches.country.replace({"Korea Republic":"South Korea","IR Iran":"Iran"})
df_coaches.head()
```





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
      <th>Coach Salary</th>
      <th>coach</th>
      <th>coach age</th>
      <th>country</th>
      <th>nationality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>235600.0</td>
      <td>Adam NAWALKA</td>
      <td>61</td>
      <td>Poland</td>
      <td>POL</td>
    </tr>
    <tr>
      <th>1</th>
      <td>810000.0</td>
      <td>Age HAREIDE</td>
      <td>65</td>
      <td>Denmark</td>
      <td>NOR</td>
    </tr>
    <tr>
      <th>2</th>
      <td>810000.0</td>
      <td>Akira NISHINO</td>
      <td>63</td>
      <td>Japan</td>
      <td>JPN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>174500.0</td>
      <td>Aliou CISSE</td>
      <td>42</td>
      <td>Senegal</td>
      <td>SEN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1030000.0</td>
      <td>Bert VAN MARWIJK</td>
      <td>66</td>
      <td>Australia</td>
      <td>NED</td>
    </tr>
  </tbody>
</table>
</div>



### For our base line model an extra 3 features were used(taken from players_features dataframe above): average age of every team, average number of caps and average number of goals

## DataFrame for the baseline model

In this section we create dataframe to be used to create and train our model



```
#Data set with Few predictor focused on Fifa Ranking 
result3 = pd.merge(df_wins, players_features, on='country')
result3 = pd.merge(result3,df_ranking, on='country')
result3 = pd.merge(result3,df_coaches, on='country')
result3 = result3.set_index('country')
# drop unnecessary columns
result3=result3.drop(["Top League","nationality","CL League 2017-2018", "coach", "coach age"],axis=1)
display(result3.head())

```



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
      <th># won</th>
      <th>age</th>
      <th>caps</th>
      <th>goals</th>
      <th>ranking</th>
      <th>Coach Salary</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Egypt</th>
      <td>6</td>
      <td>28.521739</td>
      <td>37.304348</td>
      <td>64</td>
      <td>45</td>
      <td>1300000.0</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>5</td>
      <td>28.304348</td>
      <td>28.521739</td>
      <td>56</td>
      <td>70</td>
      <td>2210000.0</td>
    </tr>
    <tr>
      <th>Saudi Arabia</th>
      <td>2</td>
      <td>28.173913</td>
      <td>34.304348</td>
      <td>91</td>
      <td>67</td>
      <td>1230000.0</td>
    </tr>
    <tr>
      <th>Uruguay</th>
      <td>7</td>
      <td>27.782609</td>
      <td>42.260870</td>
      <td>134</td>
      <td>14</td>
      <td>1470000.0</td>
    </tr>
    <tr>
      <th>Iran</th>
      <td>4</td>
      <td>26.739130</td>
      <td>30.521739</td>
      <td>109</td>
      <td>37</td>
      <td>1690000.0</td>
    </tr>
  </tbody>
</table>
</div>




```
# standardize the features data frame because of differences in values scale
result3_std = (result3-result3.mean())/result3.std()
result3_std.head()
```





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
      <th># won</th>
      <th>age</th>
      <th>caps</th>
      <th>goals</th>
      <th>ranking</th>
      <th>Coach Salary</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Egypt</th>
      <td>-0.138330</td>
      <td>1.032079</td>
      <td>0.200949</td>
      <td>-0.975293</td>
      <td>0.953167</td>
      <td>0.164699</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>-0.433434</td>
      <td>0.818454</td>
      <td>-0.685091</td>
      <td>-1.170963</td>
      <td>2.171272</td>
      <td>1.194555</td>
    </tr>
    <tr>
      <th>Saudi Arabia</th>
      <td>-1.318745</td>
      <td>0.690278</td>
      <td>-0.101708</td>
      <td>-0.314906</td>
      <td>2.025099</td>
      <td>0.085479</td>
    </tr>
    <tr>
      <th>Uruguay</th>
      <td>0.156774</td>
      <td>0.305752</td>
      <td>0.700991</td>
      <td>0.736820</td>
      <td>-0.557283</td>
      <td>0.357090</td>
    </tr>
    <tr>
      <th>Iran</th>
      <td>-0.728538</td>
      <td>-0.719652</td>
      <td>-0.483319</td>
      <td>0.125351</td>
      <td>0.563373</td>
      <td>0.606066</td>
    </tr>
  </tbody>
</table>
</div>





```

plt.figure(figsize = [14,5])
plt.subplot(1,4,1)
bplot1=result3.boxplot(column=['age'],figsize=(2,4),
                       grid=True)
plt.subplot(1,4,2)
bplot2=result3.boxplot(column=['goals'],figsize=(2,4),
                       grid=True)
plt.subplot(1,4,3)
bplot3=result3.boxplot(column=['caps'],figsize=(2,4),
                       grid=True)
plt.subplot(1,4,4)
bplot4=result3.boxplot(column=['Coach Salary'],figsize=(2,4),
                       grid=True)


```



![png](milst4_files/milst4_45_0.png)




```
plt.figure(figsize=(9,5))
sns.heatmap(result3_std.corr(),annot=True) 
```





    <matplotlib.axes._subplots.AxesSubplot at 0x7f2727335ac8>




![png](milst4_files/milst4_46_1.png)


##Results of our baseline model

In this section, we display the results of our baseline model and we look for the best Hyper paramters for each model tried. The model to be tried include: 

- Mutinomial logistic Regression 
- kNN 
- Decision Tree
- Linear Discriminant Analysis / Quadratic Linear Discriminant Analysis 
- Random Forest 
- Neural Network




```
# encapsulate performance measures in functions

def train_acc_score(model):
    return round(np.mean(cross_val_score(model,x_train,y_train,cv=k_fold,scoring="accuracy")),4)

def test_acc_score(model):
    return round(accuracy_score(y_test, model.predict(x_test)),4)

def train_prec_score(model):
    return round(precision_score(y_train,model.predict(x_train),average='macro'),4)

def test_prec_score(model):
    return round(precision_score(y_test,model.predict(x_test),average='macro'),4)

```




```
# function used in order to get the a confusion matrix of the classifcation model used
def confusion_matrix_model(model_used,x,y):
    cm=confusion_matrix(y,model_used.predict(x))
    col=["Predicted Draw","Predicted Team 1","Predicted Team 2"]
    cm=pd.DataFrame(cm)
    cm.columns=col
    cm.index=["Actual Draw","Actual Team 1","Actual Team 2"]
    return cm.T

def confusion_matrix_model_knockout(model_used,x,y):
    cm=confusion_matrix(y,knockout_test(model_used,x))
    col=["Predicted Team 1","Predicted Team 2"]
    cm=pd.DataFrame(cm)
    cm.columns=col
    cm.index=["Actual Team 1","Actual Team 2"]
    return cm.T
```




```
# function used  in order to merge the x and y_train sets with the features data frame and then give it to the model

def prepare_data(x_train,y_train,result):

    x1 = np.array(result.loc[x_train['home_team']])   # Match each home team game to it's stats 
    x2 = np.array(result.loc[x_train['away_team']])   # Match each away team game to its stats
    y = np.zeros_like(y_train.values)
    y[y_train.values == 'Draw'] = 0   #Build the classfication
    y[y_train.values == x_train['home_team'].values] = 1
    y[y_train.values == x_train['away_team'].values] = 2
    y = np.array(y,dtype=np.float64)
    x = np.hstack((x1,x2))     # Overall dataset made of 138 predictors. 69 of the same for each team
    return x,y
  
```


## Classification methods



```
# function to get the observed versus predicted values
def encoder(ypred,y_train,x_train):
    ypred_label = list()
    k = 0
    for y in ypred:
        if y == 1:
            ypred_label.append(x_train['home_team'].values[k])
        elif y == 0:
            ypred_label.append('Draw')
        else:
            ypred_label.append(x_train['away_team'].values[k])
        k += 1
    return pd.DataFrame({'Predicted':ypred_label,'Observed':y_train.values})
```


##Logisitic regression - Baseline model



```
# finding top 2 components
var_explained = []
total_comp = 12
x,y = prepare_data(x_train,y_train,result3_std)
pca = PCA(n_components = total_comp).fit(x)

plt.plot(np.linspace(1, total_comp, total_comp), np.cumsum(pca.explained_variance_ratio_))
plt.xlabel('number of components')
plt.ylabel('variance explained')
plt.title('Cumulative variance explained',fontsize=15)

print("number of components that explain at least 90% of the variance=",\
    len(np.where(np.cumsum(pca.explained_variance_ratio_)<=0.9)[0])+1)
```


    number of components that explain at least 90% of the variance= 8



![png](milst4_files/milst4_54_1.png)


###train set



```
# fit the logistic regression model
reg3 = LogisticRegression(penalty='l2',multi_class='multinomial', solver='lbfgs', random_state=123456)
x,y = prepare_data(x_train,y_train,result3_std)
reg3.fit(x,y)
ypredml1 = reg3.predict(x)
df_pred_mat_lr = confusion_matrix_model(reg3,x,y)
display("conf matrix of logisitic regression",df_pred_mat_lr) 
print("accuracy score or the model logistic regression multinomial model is:",accuracy_score(ypredml1,y))
```



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



```
# test set code
x,y = prepare_data(x_test1,y_test1,result3_std)
ypredml1_test = reg3.predict(x)
df_predml1_test = encoder(ypredml1_test,y_test1,x_test1)
display(df_predml1_test.head())
df_pred_light_lr_test = confusion_matrix_model(reg3,x,y)
display("conf matrix of logisitic regression of the baseline model on the first test set",df_pred_light_lr_test) 
print("accuracy score or the model logistic regression multinomial model on test set 1 is:",accuracy_score(ypredml1_test,y))

x,y = prepare_data(x_test2,y_test2,result3_std)
df_predml1_test2 = knockout_test(reg3,x)
df_pred_light_lr_test2 = confusion_matrix_model_knockout(reg3,x,y)
display("conf matrix of logistic gression of the baseline model of the second test set",df_pred_light_lr_test2) 
print("accuracy score or the baseline model with logistic regression on test set 2 is:",accuracy_score(df_predml1_test2,y))
```



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



    'conf matrix of logistic gression of the baseline model of the second test set'



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


##KNN - baseline model



```
#Pick the best k of the model
max_score = 0
best_k = 0 
x,y = prepare_data(x_train,y_train,result3_std)
for k in range(5,26):
    KNN3 = KNeighborsClassifier(n_neighbors = k, weights='uniform')
    score = cross_val_score(KNN3, x, y ,cv=5, scoring='accuracy').mean()
    if score > max_score:
        best_k = k
        max_score = score
 
print( 'Best K is ' + str(best_k) +'.')
```


    Best K is 5.


###train set



```
# fit knn
KNN3=KNeighborsClassifier(n_neighbors=best_k,p=1,weights='uniform')

KNN3.fit(x,y)
#make predictions
ypredml3 = KNN3.predict(x)
#use confusion matrix function
df_pred_mat_knn = confusion_matrix_model(KNN3,x,y)
display("conf matrix of the KNN model of the train set on the baseline model",df_pred_mat_knn)  
print("accuracy score of the knn model of the train set on the baseline model:",accuracy_score(ypredml3,y))  

x,y = prepare_data(x_train,y_train,result3_std)
print("accuracy score of the knn model Validation Set on the baseline model:",cross_val_score(KNN3,x,y,cv=10).mean())
```



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



```
# test set code
x,y = prepare_data(x_test1,y_test1,result3_std)
ypredml3_test = KNN3.predict(x)
df_predml3_test = encoder(ypredml3_test,y_test1,x_test1)
display(df_predml3_test.head())
df_pred_light_KNN_test = confusion_matrix_model(KNN3,x,y)
display("conf matrix of KNN of the baseline model on the first test set",df_pred_light_KNN_test) 
print("accuracy score or the baseline model with KNN on the first test set is:",accuracy_score(ypredml3_test,y))

```



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



```
x,y = prepare_data(x_test2,y_test2,result3_std)

KNN3=KNeighborsClassifier(n_neighbors=best_k,p=1,weights='uniform')
KNN3.fit(x,y)
ypredml3_test2 = knockout_test(KNN3,x)
# print(min(y),max(y),min(ypredml3_test2),max(ypredml3_test2))
df_pred_light_KNN_test2 = confusion_matrix_model_knockout(KNN3,x,y)
display("conf matrix of KNN of the baseline model on the second test set",df_pred_light_KNN_test2) 
print("accuracy score or the baseline model with KNN on second  test set is:",accuracy_score(ypredml3_test2,y))


```



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


##Decision tree-Baseline Model



```
x,y = prepare_data(x_train,y_train,result3_std)
# fitting DecisionTreeClassifier class for depths 1-25, storing results
tree_cv_scores = []
for i in range (1,26):
  clf = DecisionTreeClassifier(criterion='gini', max_depth=i)
  clf.fit(x, y)
  train_score = clf.score(x, y)
  cv_score = cross_val_score(clf, x, y, scoring='accuracy', cv=5)
#   test_score = clf.score(x_test, y_test)
  tree_cv_scores.append({'Depth': i,
                         'Train Score': train_score,
                        'CV Mean Accuracy': cv_score.mean(),
                        'CV std.': cv_score.std(),
#                         'Test Score': test_score
                        })

columns=['Depth', 'Train Score', 'CV Mean Accuracy', 'CV std.'] # to preserve order
tree_scores_df = pd.DataFrame(tree_cv_scores, columns=columns)

```




```
# plot
num_SD = 2 # number of CV std for plotting

plt.figure(figsize =(10,6))
plt.plot(tree_scores_df['Depth'], tree_scores_df['Train Score'], label='Training Set Score')
plt.plot(tree_scores_df['Depth'], tree_scores_df['CV Mean Accuracy'],label='Mean CV Score ',color='green')
plt.fill_between(
    tree_scores_df['Depth'],
    tree_scores_df['CV Mean Accuracy'] -  num_SD * tree_scores_df['CV std.'],
    tree_scores_df['CV Mean Accuracy'] +  num_SD * tree_scores_df['CV std.'],
    alpha=.1, color='green')

plt.legend()
plt.xlabel("Max Tree Depth")
plt.ylabel("Mean CV Accuracy Score +/- {} SD".format(num_SD))
plt.title("Accuracy Scores vs. Maximum Tree Depth on Decision Tree Classifier, Training Set");
```



![png](milst4_files/milst4_69_0.png)




```
best_index = tree_scores_df['CV Mean Accuracy'].idxmax() 
best_depth = tree_scores_df['Depth'][best_index] 

tree_clf = DecisionTreeClassifier(max_depth=best_depth).fit(x,y)
best_depth
```





    16



##Linear Discriminant Analysis (LDA)-Baseline model

### Train Data Set 



```
x,y = prepare_data(x_train,y_train,result3_std)
lda = LinearDiscriminantAnalysis()
lda.fit(x, y)
ypredlda3 = lda.predict(x)
#use confusion matrix function
df_pred_mat_lda = confusion_matrix_model(lda,x,y)
display("conf matrix of lda on the baseline model",df_pred_mat_lda)  
print("accuracy score of lda on baseline model:",accuracy_score(ypredlda3,y))  

x,y = prepare_data(x_train,y_train,result3_std)
print("accuracy score of lda on baseline model Validation Set:",cross_val_score(lda,x,y,cv=10).mean())
```



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



```
x,y = prepare_data(x_test1,y_test1,result3_std)
lda = LinearDiscriminantAnalysis()
lda.fit(x, y)
ypredlda3 = lda.predict(x)
#use confusion matrix function
df_pred_mat_lda = confusion_matrix_model(lda,x,y)
display("conf matrix of the lda model",df_pred_mat_lda)  
print("accuracy score of the lda model:",accuracy_score(ypredlda3,y))  

x,y = prepare_data(x_train,y_train,result3_std)
print("accuracy score of the lda model Validation Set:",cross_val_score(lda,x,y,cv=10).mean())
```



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



```
x,y = prepare_data(x_test2,y_test2,result3_std)
lda = LinearDiscriminantAnalysis()
lda.fit(x, y)
ypredlda3 = knockout_test(lda,x)
#use confusion matrix function
# df_pred_mat_lda = confusion_matrix_model_knockout(lda,x,y)
# display("conf matrix of the lda model",df_pred_mat_lda)  
print("accuracy score of lda on baseline model of the second test set:",accuracy_score(ypredlda3,y))  

```


    accuracy score of lda on baseline model of the second test set: 0.0


## Quadratic Discriminant Analysis (QDA)

### Training Data Set



```
x,y = prepare_data(x_train,y_train,result3_std)
qda = QuadraticDiscriminantAnalysis()
qda.fit(x, y)
ypredqda3 = qda.predict(x)
#use confusion matrix function
df_pred_mat_qda = confusion_matrix_model(qda,x,y)
display("conf matrix of qda on baseline model train set",df_pred_mat_qda)  
print("accuracy score of qda on baselin model train set:",accuracy_score(ypredqda3,y))  

x,y = prepare_data(x_train,y_train,result3_std)
print("accuracy score of qda on baseline model Validation Set:",cross_val_score(qda,x,y,cv=10).mean())
```



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



```
x,y = prepare_data(x_test1,y_test1,result3_std)
ypredqda3_test1 = qda.predict(x)
#use confusion matrix function
df_pred_mat_qda = confusion_matrix_model(qda,x,y)
display("conf matrix of the qda model",df_pred_mat_qda)  
print("accuracy score of the qda model:",accuracy_score(ypredqda3_test1,y))  

x,y = prepare_data(x_train,y_train,result3_std)
print("accuracy score of the qda model Validation Set:",cross_val_score(qda,x,y,cv=10).mean())
```



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



```
x,y = prepare_data(x_test2,y_test2,result3_std)
ypredqda3_test2 = qda.predict(x)
#use confusion matrix function
# df_pred_mat_qda = confusion_matrix_model(qda,x,y)
# display("conf matrix of the qda model",df_pred_mat_qda)  
print("accuracy score of qda on baseline model second test:",accuracy_score(ypredqda3_test2,y))  

x,y = prepare_data(x_train,y_train,result3_std)
print("accuracy score of qda baseline model on Validation Set:",cross_val_score(qda,x,y,cv=10).mean())
```


    accuracy score of qda on baseline model second test: 0.5625
    accuracy score of qda baseline model on Validation Set: 0.5103846653394701


# Ensemble methods

In this section we try different types of Ensembles methods: 
- Random Forest
- Bagging 
- Boosting 

## Random Forest- baseline model

### train set



```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_train,y_train,result3_std)
rfml = RandomForestClassifier(n_estimators=50, oob_score=True, max_depth=10,random_state=1234)
rfml.fit(x, y)
ypredml2=rfml.predict(x)

print("accuracy score for the random forest model:",accuracy_score(ypredml2,y))

```


    accuracy score for the random forest model: 0.6728395061728395


### test set 1



```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_test1,y_test1,result3_std)
# rfml = RandomForestClassifier(n_estimators=50, oob_score=True, max_depth=10,random_state=1234)
# rfml.fit(x, y)
ypredml2=rfml.predict(x)

print("accuracy score for the random forest test1 on baseline model:",accuracy_score(ypredml2,y))

```


    accuracy score for the random forest test1 on baseline model: 0.6666666666666666


### test set 2



```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_test2,y_test2,result3_std)
# rfml = RandomForestClassifier(n_estimators=50, oob_score=True, max_depth=10,random_state=1234)
# rfml.fit(x, y)
ypredml2=knockout_test(rfml,x)

print("accuracy score for the random forest test2 on baseline model:",accuracy_score(ypredml2,y))

```


    accuracy score for the random forest test2 on baseline model: 0.1875


##Bagging



```
from sklearn.utils import resample


x,y = prepare_data(x_train,y_train,result3_std)
xt1,yt1 = prepare_data(x_test1,y_test1,result3_std)
xt2,yt2 = prepare_data(x_test2,y_test2,result3_std)

np.random.seed(0)

N_bootstraps = 45
bagging_train = np.zeros((x.shape[0], N_bootstraps),dtype=int)
bagging_test1 = np.zeros((xt1.shape[0], N_bootstraps),dtype=int)
bagging_test2 = np.zeros((xt2.shape[0], N_bootstraps),dtype=int)
bagging_trees = []

simpletree = DecisionTreeClassifier(max_depth=16)

# bootstrapping, saving results each time
for i in range(N_bootstraps):
  boot_xx, boot_y = resample(x, y)
  bagging_trees.append(simpletree.fit(boot_xx, boot_y))
  bagging_train[:, i] = simpletree.predict(x)
  bagging_test1[:, i] = simpletree.predict(xt1)
  bagging_test2[:, i] = simpletree.predict(xt2)


# format table
columns = ["Bootstrap-Model_"+str(i+1) for i in range(N_bootstraps)]
rows_train = ["Training-Row_" + str(i+1) for i in range(len(x))]
rows_test1 = ["Test-Row_" + str(i+1) for i in range(len(xt1))]
rows_test2 = ["Test-Row_" + str(i+1) for i in range(len(xt2))]


bagging_train = pd.DataFrame(bagging_train, columns=columns, index=rows_train)
bagging_test1 = pd.DataFrame(bagging_test1, columns=columns, index=rows_test1)
bagging_test2 = pd.DataFrame(bagging_test2, columns=columns, index=rows_test2)


print("train bootstrapped table:")
display(bagging_train.head())
print("\ntest1 bootstrapped table:")
display(bagging_test1.head())
print("\ntest2 bootstrapped table:")
display(bagging_test2.head())

```


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




```
# get majority to generate combined predictions
y_hat_train_maj = (np.mean(bagging_train,axis=1)>.5).astype(int)
y_hat_test_maj1 = (np.mean(bagging_test1,axis=1)>.5).astype(int)
y_hat_test_maj2 = (np.mean(bagging_test2,axis=1)>.5).astype(int)


bagged_accuracy_train = accuracy_score(y, y_hat_train_maj)
bagged_accuracy_test1 = accuracy_score(yt1, y_hat_test_maj1)
bagged_accuracy_test2 = accuracy_score(yt2, y_hat_test_maj2)


print("bagging model, training set accuracy:", bagged_accuracy_train)
print("bagging model, test-1 set accuracy:", bagged_accuracy_test1)
print("bagging model, test-2 set accuracy:", bagged_accuracy_test2)

```


    bagging model, training set accuracy: 0.5359147025813692
    bagging model, test-1 set accuracy: 0.4166666666666667
    bagging model, test-2 set accuracy: 0.4375




```
def running_predictions(prediction_dataset, targets):
    """A function to predict examples' class via the majority among trees (ties are predicted as 0)
    
    Inputs:
      prediction_dataset - a (n_examples by n_sub_models) dataset, where each entry [i,j] is sub-model j's prediction
          for example i
      targets - the true class labels
    
    Returns:
      a vector where vec[i] is the model's accuracy when using just the first i+1 sub-models
    """
    
    n_trees = prediction_dataset.shape[1]
    
    # find the running percentage of models voting 1 as more models are considered
    running_percent_1s = np.cumsum(prediction_dataset, axis=1)/np.arange(1,n_trees+1)
    
    # predict 1 when the running average is above 0.5
    running_conclusions = running_percent_1s > 0.5
    
    # check whether the running predictions match the targets
    running_correctnesss = running_conclusions == targets.reshape(-1,1)
    
    return np.mean(running_correctnesss, axis=0)
    # returns a 1-d series of the accuracy of using the first n trees to predict the targets
```




```
running_pred_train = running_predictions(bagging_train.values, y)
running_pred_test1 = running_predictions(bagging_test1.values, yt1)
running_pred_test2 = running_predictions(bagging_test2.values, yt2)

overfit_model = DecisionTreeClassifier(max_depth=16).fit(x, y)
ypred_overfit = overfit_model.predict(xt1)

plt.figure(figsize=(10,8))
plt.plot(range(1,46), running_pred_train, 'o-', label='Training set, Bootstrapped Models')
plt.plot(range(1,46), running_pred_test1, 'o-', label='Test set1, Bootstrapped Models')
plt.plot(range(1,46), running_pred_test2, 'o-', label='Test set2, Bootstrapped Models')
plt.title('Training and Test Set Accuracy Scores by Number of Bootstraps')
plt.xlabel('Number of Bootstraps')
plt.ylabel('Accuracy Score')
plt.legend();
```



![png](milst4_files/milst4_97_0.png)


## Boosting



```
n_estimators = 800
learning_rate = 0.05

x,y = prepare_data(x_train,y_train,result3_std)
ab = AdaBoostClassifier(base_estimator=DecisionTreeClassifier(max_depth=3),
                            n_estimators=n_estimators, learning_rate=learning_rate)
ab.fit(x, y)
xt1,yt1 = prepare_data(x_test1,y_test1,result3_std)
xt2,yt2 = prepare_data(x_test2,y_test2,result3_std)
ab_scores_train = list(ab.staged_score(x, y))
ab_scores_test1 = list(ab.staged_score(xt1, yt1))
ab_scores_test2 = list(ab.staged_score(xt2, yt2))

x_plot = range(1, n_estimators+1) # just so we plot x-values of 1-800 instead of 0-799

plt.plot(x_plot, ab_scores_train, label='Training Set')
plt.plot(x_plot, ab_scores_test1, label='Test Set-1')
plt.plot(x_plot, ab_scores_test2, label='Test Set-2')
plt.title('Adaboost Model: Accuracy Score by Number of Estimators/Iterations')
plt.xlabel('Number of Estimators/Iterations')
plt.ylabel('Accuracy')
plt.legend();
```



![png](milst4_files/milst4_99_0.png)




```
# List of lists of the staged predictions
staged_scores_train = []
staged_scores_test1 = []
staged_scores_test2 = []

for depth in range(1,5):
  ab = AdaBoostClassifier(base_estimator=DecisionTreeClassifier(max_depth=depth),
                            n_estimators=n_estimators, learning_rate=learning_rate)
  ab.fit(x, y)

  ab_scores_train = list(ab.staged_score(x, y))
  ab_scores_test1 = list(ab.staged_score(xt1, yt1))
  ab_scores_test2 = list(ab.staged_score(xt2, yt2))
  
  staged_scores_train.append(ab_scores_train)
  staged_scores_test1.append(ab_scores_test1)
  staged_scores_test2.append(ab_scores_test2)

```




```
fig, axs = plt.subplots(2,2,figsize=(10,8), sharey=True)

for i, ax in enumerate(axs.ravel()):
  ax.plot(x_plot, staged_scores_train[i], label='Training Set')
  ax.plot(x_plot, staged_scores_test1[i], label='Test Set-1')
  ax.plot(x_plot, staged_scores_test2[i], label='Test Set-2')
  ax.set_title('Depth = {}'.format(i+1))
  ax.set_xlabel('N_estimators')
  ax.set_ylabel('Accuracy Score')
  ax.legend();

plt.tight_layout()
plt.suptitle('Adaboost Models: Accuracy Score for Models with Different Base Tree Depths, \
by Number of Estimators/Iterations', x=.5, y=1.02);
```



![png](milst4_files/milst4_101_0.png)




```
staged_scores_test_array1 = np.array(staged_scores_test1)
staged_scores_test_array2 = np.array(staged_scores_test2)


for i, depth_scores in enumerate(staged_scores_test_array1):
  depth = i+1
  idx = (np.argmax(depth_scores))
  print("Depth = {}: best test set 1 accuracy at {} estimators.".format(depth, idx+1))
  print("train set accuracy score: {}\n".format(depth_scores[idx]))
  
  
for i, depth_scores in enumerate(staged_scores_test_array2):
  depth = i+1
  idx = (np.argmax(depth_scores))
  print("Depth = {}: best test set 2 accuracy at {} estimators.".format(depth, idx+1))
  print("train set accuracy score: {}\n".format(depth_scores[idx]))
```


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
    


##Neural Network


### Training Data Set



```
x,y = prepare_data(x_train,y_train,result3_std)
y_nn = []
for value in y:
  if value == 0:
    y_nn.append([1,0,0])
  elif value == 1:
    y_nn.append([0,1,0])
  else:
    y_nn.append([0,0,1])

y_nn = np.array(y_nn)

model_nn = Sequential([
    Dense(200, input_shape=(x.shape[1],), activation='relu'),
    # Dense(100, activation='relu'),
    # Dense(50, activation='relu'),
    Dense(3, activation='linear')
])


model_nn.compile(loss='mean_absolute_error', 
              optimizer='adam',metrics=['acc'])
model_nn.summary()

model_nn.fit(x, y_nn, 
          epochs=10*32, 
          batch_size=32, 
          validation_split=.2,
          verbose=False)

```


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





    <keras.callbacks.History at 0x7f271ef6fd68>





```
res=model_nn.evaluate(x, y_nn, verbose=False)

print("the loss of this model is:",res[0])
print("the accuracy of this model is:",res[1])
```


    the loss of this model is: 0.2612320419012095
    the accuracy of this model is: 0.6470258137593767


### regularized neural network train set



```
from keras.constraints import maxnorm

model2 = Sequential()
model2.add(Dense(60, input_shape=(x.shape[1],), kernel_initializer='normal', activation='relu', kernel_constraint=maxnorm(3)))
model2.add(Dropout(0.2))
model2.add(Dense(30, kernel_initializer='normal', activation='relu', kernel_constraint=maxnorm(3)))
model2.add(Dropout(0.2))
model2.add(Dense(3, kernel_initializer='normal', activation='linear'))
# Compile model
  
model2.compile(loss='mean_absolute_error', 
              optimizer='adam',metrics=['acc'])

model2.fit(x, y_nn, 
          epochs=10*32, 
          batch_size=32, 
          validation_split=.2,
          verbose=False)

print('Model 2')
res1=model2.evaluate(x, y_nn, verbose=False)
print("the loss of this model is:",res[0])
print("the accuracy of this model is:",res[1])
```


    Model 2
    the loss of this model is: 0.2612320419012095
    the accuracy of this model is: 0.6470258137593767


### World Cup 2018 - Group  Phase games



```
x,y = prepare_data(x_test1,y_test1,result3_std)
y_nn = []
for value in y:
  if value == 0:
    y_nn.append([1,0,0])
  elif value == 1:
    y_nn.append([0,1,0])
  else:
    y_nn.append([0,0,1])

y_nn = np.array(y_nn)

ypred_proba_nn = model2.predict(x)

ypred_nn = []
for value in ypred_proba_nn:
  if value[0] == 1:
    ypred_nn.append(0)
  elif value[1] == 1:
    ypred_nn.append(1)
  else:
    ypred_nn.append(2)

ypred_nn_test1 = np.array(ypred_nn)
    
print("The accuracy score of test set 1 of this neural net is:",accuracy_score(ypred_nn_test1,y))


```


    The accuracy score of test set 1 of this neural net is: 0.4583333333333333


### World Cup 2018 - Knockout Games 



```

x,y = prepare_data(x_test2,y_test2,result3_std)

ypred_nn_test2 = knockout_test(model2,x)

print("The accuracy score of test set 2 of this neural net is:",accuracy_score(ypred_nn_test2,y))

```


    The accuracy score of test set 2 of this neural net is: 0.625


# Model comparison

In this Section we apply 7-fold Cross Validation to all the models designed above in order to fund which ones has the best Training and Validation Accuracy 



```
models = [reg3, tree_clf, lda, qda, KNN3,rfml,model2]
labels = ['Logistic Regression' ,'Decision Tree','LDA','QDA','KNN','Random Forest']
x,y = prepare_data(x_train,y_train,result3)
cv_scores = []
for model, label in zip(models, labels):
  cv_score = cross_val_score(model, x, y, scoring='accuracy', cv=7).mean()
  cv_scores.append({'Model': label,
                        'CV_Accuracy': cv_score})
  
scores_df = pd.DataFrame(cv_scores, columns=['Model', 'CV_Accuracy'])
scores_df.sort_values(by='CV_Accuracy', ascending=False, inplace=True)
scores_df.reset_index(drop=True, inplace=True)
scores_df.index +=1 # So the indices will be model rankings by score


print("Mean CV Accuracy Score by Model:")
display(scores_df.round(4))
```


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



```
plt.figure(figsize = [21,8])
plt.subplot(1,3,1)
plt.title('Logisitic reg. Feature Importance for Draw')
plt.barh(result3.columns,reg3.coef_[0,:6],color='r')
plt.barh(result3.columns,reg3.coef_[0,6:],color='b')
plt.legend(['Home Team','Away Team'])
plt.subplot(1,3,2)
plt.title('Logisitic reg. Feature Importance for Home Team Win')
plt.barh(result3.columns,reg3.coef_[1,:6],color='r')
plt.barh(result3.columns,reg3.coef_[1,6:],color='b')
plt.legend(['Home Team','Away Team'])
plt.subplot(1,3,3)
plt.title('Logisitic reg. Feature Importance for Away Team Win')
plt.barh(result3.columns,reg3.coef_[2,:6],color='r')
plt.barh(result3.columns,reg3.coef_[2,6:],color='b')
plt.legend(['Home Team','Away Team'])

```





    <matplotlib.legend.Legend at 0x7f271e76d780>




![png](milst4_files/milst4_116_1.png)




```
# result3 coefficients

plt.figure(figsize = [9,9])
plt.title('Logisitic Feature Comparison (Age)')
plt.barh(result3.columns[1]+' Draw',reg3.coef_[0,1],color='c',edgecolor='k')
plt.barh(result3.columns[1]+' Draw',reg3.coef_[0,6],color='c')
plt.barh(result3.columns[1]+' Home win',reg3.coef_[1,1],color='m',edgecolor='k')
plt.barh(result3.columns[1]+' Home win',reg3.coef_[1,6],color='m')
plt.barh(result3.columns[1]+' Away win',reg3.coef_[2,1],color='y',edgecolor='k')
plt.barh(result3.columns[1]+' Away win',reg3.coef_[2,6],color='y')

```





    <Container object of 1 artists>




![png](milst4_files/milst4_117_1.png)


#Second Model built with non FIFA - Ranking Features

In this section we web-scrap data in order to find new feature apart from FIFA Ranking  feature with the goal of building a better model




```
# sofifa complete dataset
response = requests.get('https://drive.google.com/uc?export=download&id=12Km8bbBwLulQi2uFymPSCkXBTB5Ws-qj')
#have to decode the bytes before it can be read by pandas into df
df1 = pd.read_csv(BytesIO(response.content),index_col=0)
df1.head()
```





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
      <th>Name</th>
      <th>Age</th>
      <th>Photo</th>
      <th>Nationality</th>
      <th>Flag</th>
      <th>Overall</th>
      <th>Potential</th>
      <th>Club</th>
      <th>Club Logo</th>
      <th>Value</th>
      <th>...</th>
      <th>RB</th>
      <th>RCB</th>
      <th>RCM</th>
      <th>RDM</th>
      <th>RF</th>
      <th>RM</th>
      <th>RS</th>
      <th>RW</th>
      <th>RWB</th>
      <th>ST</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Cristiano Ronaldo</td>
      <td>32</td>
      <td>https://cdn.sofifa.org/48/18/players/20801.png</td>
      <td>Portugal</td>
      <td>https://cdn.sofifa.org/flags/38.png</td>
      <td>94</td>
      <td>94</td>
      <td>Real Madrid CF</td>
      <td>https://cdn.sofifa.org/24/18/teams/243.png</td>
      <td>€95.5M</td>
      <td>...</td>
      <td>61.0</td>
      <td>53.0</td>
      <td>82.0</td>
      <td>62.0</td>
      <td>91.0</td>
      <td>89.0</td>
      <td>92.0</td>
      <td>91.0</td>
      <td>66.0</td>
      <td>92.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>L. Messi</td>
      <td>30</td>
      <td>https://cdn.sofifa.org/48/18/players/158023.png</td>
      <td>Argentina</td>
      <td>https://cdn.sofifa.org/flags/52.png</td>
      <td>93</td>
      <td>93</td>
      <td>FC Barcelona</td>
      <td>https://cdn.sofifa.org/24/18/teams/241.png</td>
      <td>€105M</td>
      <td>...</td>
      <td>57.0</td>
      <td>45.0</td>
      <td>84.0</td>
      <td>59.0</td>
      <td>92.0</td>
      <td>90.0</td>
      <td>88.0</td>
      <td>91.0</td>
      <td>62.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Neymar</td>
      <td>25</td>
      <td>https://cdn.sofifa.org/48/18/players/190871.png</td>
      <td>Brazil</td>
      <td>https://cdn.sofifa.org/flags/54.png</td>
      <td>92</td>
      <td>94</td>
      <td>Paris Saint-Germain</td>
      <td>https://cdn.sofifa.org/24/18/teams/73.png</td>
      <td>€123M</td>
      <td>...</td>
      <td>59.0</td>
      <td>46.0</td>
      <td>79.0</td>
      <td>59.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>84.0</td>
      <td>89.0</td>
      <td>64.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>L. Suárez</td>
      <td>30</td>
      <td>https://cdn.sofifa.org/48/18/players/176580.png</td>
      <td>Uruguay</td>
      <td>https://cdn.sofifa.org/flags/60.png</td>
      <td>92</td>
      <td>92</td>
      <td>FC Barcelona</td>
      <td>https://cdn.sofifa.org/24/18/teams/241.png</td>
      <td>€97M</td>
      <td>...</td>
      <td>64.0</td>
      <td>58.0</td>
      <td>80.0</td>
      <td>65.0</td>
      <td>88.0</td>
      <td>85.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>68.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M. Neuer</td>
      <td>31</td>
      <td>https://cdn.sofifa.org/48/18/players/167495.png</td>
      <td>Germany</td>
      <td>https://cdn.sofifa.org/flags/21.png</td>
      <td>92</td>
      <td>92</td>
      <td>FC Bayern Munich</td>
      <td>https://cdn.sofifa.org/24/18/teams/21.png</td>
      <td>€61M</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 74 columns</p>
</div>





```
# rank players according to their overall score
df1.nlargest(100, columns='Overall').head()
```





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
      <th>Name</th>
      <th>Age</th>
      <th>Photo</th>
      <th>Nationality</th>
      <th>Flag</th>
      <th>Overall</th>
      <th>Potential</th>
      <th>Club</th>
      <th>Club Logo</th>
      <th>Value</th>
      <th>...</th>
      <th>RB</th>
      <th>RCB</th>
      <th>RCM</th>
      <th>RDM</th>
      <th>RF</th>
      <th>RM</th>
      <th>RS</th>
      <th>RW</th>
      <th>RWB</th>
      <th>ST</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Cristiano Ronaldo</td>
      <td>32</td>
      <td>https://cdn.sofifa.org/48/18/players/20801.png</td>
      <td>Portugal</td>
      <td>https://cdn.sofifa.org/flags/38.png</td>
      <td>94</td>
      <td>94</td>
      <td>Real Madrid CF</td>
      <td>https://cdn.sofifa.org/24/18/teams/243.png</td>
      <td>€95.5M</td>
      <td>...</td>
      <td>61.0</td>
      <td>53.0</td>
      <td>82.0</td>
      <td>62.0</td>
      <td>91.0</td>
      <td>89.0</td>
      <td>92.0</td>
      <td>91.0</td>
      <td>66.0</td>
      <td>92.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>L. Messi</td>
      <td>30</td>
      <td>https://cdn.sofifa.org/48/18/players/158023.png</td>
      <td>Argentina</td>
      <td>https://cdn.sofifa.org/flags/52.png</td>
      <td>93</td>
      <td>93</td>
      <td>FC Barcelona</td>
      <td>https://cdn.sofifa.org/24/18/teams/241.png</td>
      <td>€105M</td>
      <td>...</td>
      <td>57.0</td>
      <td>45.0</td>
      <td>84.0</td>
      <td>59.0</td>
      <td>92.0</td>
      <td>90.0</td>
      <td>88.0</td>
      <td>91.0</td>
      <td>62.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Neymar</td>
      <td>25</td>
      <td>https://cdn.sofifa.org/48/18/players/190871.png</td>
      <td>Brazil</td>
      <td>https://cdn.sofifa.org/flags/54.png</td>
      <td>92</td>
      <td>94</td>
      <td>Paris Saint-Germain</td>
      <td>https://cdn.sofifa.org/24/18/teams/73.png</td>
      <td>€123M</td>
      <td>...</td>
      <td>59.0</td>
      <td>46.0</td>
      <td>79.0</td>
      <td>59.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>84.0</td>
      <td>89.0</td>
      <td>64.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>L. Suárez</td>
      <td>30</td>
      <td>https://cdn.sofifa.org/48/18/players/176580.png</td>
      <td>Uruguay</td>
      <td>https://cdn.sofifa.org/flags/60.png</td>
      <td>92</td>
      <td>92</td>
      <td>FC Barcelona</td>
      <td>https://cdn.sofifa.org/24/18/teams/241.png</td>
      <td>€97M</td>
      <td>...</td>
      <td>64.0</td>
      <td>58.0</td>
      <td>80.0</td>
      <td>65.0</td>
      <td>88.0</td>
      <td>85.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>68.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M. Neuer</td>
      <td>31</td>
      <td>https://cdn.sofifa.org/48/18/players/167495.png</td>
      <td>Germany</td>
      <td>https://cdn.sofifa.org/flags/21.png</td>
      <td>92</td>
      <td>92</td>
      <td>FC Bayern Munich</td>
      <td>https://cdn.sofifa.org/24/18/teams/21.png</td>
      <td>€61M</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 74 columns</p>
</div>





```
# clean unnecessary columns and strip the value and wage column from non numeric signs to make the data analyzable
df1_clean=df1.drop(["Photo","Flag","Club Logo"],axis=1)
df1_clean['Value'] = df1_clean['Value'].str.replace('€', '')
df1_clean['Wage']=df1_clean['Wage'].str.replace('€','')

#parse string for millions and thousands to numeric values
def parseValueColumn(strVal):
    if 'M' in strVal:
        return int(float(strVal.replace('M', '')) * 1000000)
    elif 'K' in strVal:
        return int(float(strVal.replace('K', '')) * 1000)
    else:
        return int(strVal)   
#parse string for thousands to numeric values 
def parseWageColumn(strVal):
  if 'K' in strVal:
    return int(float(strVal.replace('K', '')) * 1000)
  else:
    return int(strVal)   
 
df1_clean['Value'] = df1_clean['Value'].apply(lambda x: parseValueColumn(x))
df1_clean['Wage']=df1_clean['Wage'].apply(lambda x: parseWageColumn(x))
```




```
# check for null data
df1.isnull().sum()
```





    Name                      0
    Age                       0
    Photo                     0
    Nationality               0
    Flag                      0
    Overall                   0
    Potential                 0
    Club                    248
    Club Logo                 0
    Value                     0
    Wage                      0
    Special                   0
    Acceleration              0
    Aggression                0
    Agility                   0
    Balance                   0
    Ball control              0
    Composure                 0
    Crossing                  0
    Curve                     0
    Dribbling                 0
    Finishing                 0
    Free kick accuracy        0
    GK diving                 0
    GK handling               0
    GK kicking                0
    GK positioning            0
    GK reflexes               0
    Heading accuracy          0
    Interceptions             0
                           ... 
    Vision                    0
    Volleys                   0
    CAM                    2029
    CB                     2029
    CDM                    2029
    CF                     2029
    CM                     2029
    ID                        0
    LAM                    2029
    LB                     2029
    LCB                    2029
    LCM                    2029
    LDM                    2029
    LF                     2029
    LM                     2029
    LS                     2029
    LW                     2029
    LWB                    2029
    Preferred Positions       0
    RAM                    2029
    RB                     2029
    RCB                    2029
    RCM                    2029
    RDM                    2029
    RF                     2029
    RM                     2029
    RS                     2029
    RW                     2029
    RWB                    2029
    ST                     2029
    Length: 74, dtype: int64





```
df1_clean.head()
```





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
      <th>Name</th>
      <th>Age</th>
      <th>Nationality</th>
      <th>Overall</th>
      <th>Potential</th>
      <th>Club</th>
      <th>Value</th>
      <th>Wage</th>
      <th>Special</th>
      <th>Acceleration</th>
      <th>...</th>
      <th>RB</th>
      <th>RCB</th>
      <th>RCM</th>
      <th>RDM</th>
      <th>RF</th>
      <th>RM</th>
      <th>RS</th>
      <th>RW</th>
      <th>RWB</th>
      <th>ST</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Cristiano Ronaldo</td>
      <td>32</td>
      <td>Portugal</td>
      <td>94</td>
      <td>94</td>
      <td>Real Madrid CF</td>
      <td>95500000</td>
      <td>565000</td>
      <td>2228</td>
      <td>89</td>
      <td>...</td>
      <td>61.0</td>
      <td>53.0</td>
      <td>82.0</td>
      <td>62.0</td>
      <td>91.0</td>
      <td>89.0</td>
      <td>92.0</td>
      <td>91.0</td>
      <td>66.0</td>
      <td>92.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>L. Messi</td>
      <td>30</td>
      <td>Argentina</td>
      <td>93</td>
      <td>93</td>
      <td>FC Barcelona</td>
      <td>105000000</td>
      <td>565000</td>
      <td>2154</td>
      <td>92</td>
      <td>...</td>
      <td>57.0</td>
      <td>45.0</td>
      <td>84.0</td>
      <td>59.0</td>
      <td>92.0</td>
      <td>90.0</td>
      <td>88.0</td>
      <td>91.0</td>
      <td>62.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Neymar</td>
      <td>25</td>
      <td>Brazil</td>
      <td>92</td>
      <td>94</td>
      <td>Paris Saint-Germain</td>
      <td>123000000</td>
      <td>280000</td>
      <td>2100</td>
      <td>94</td>
      <td>...</td>
      <td>59.0</td>
      <td>46.0</td>
      <td>79.0</td>
      <td>59.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>84.0</td>
      <td>89.0</td>
      <td>64.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>L. Suárez</td>
      <td>30</td>
      <td>Uruguay</td>
      <td>92</td>
      <td>92</td>
      <td>FC Barcelona</td>
      <td>97000000</td>
      <td>510000</td>
      <td>2291</td>
      <td>88</td>
      <td>...</td>
      <td>64.0</td>
      <td>58.0</td>
      <td>80.0</td>
      <td>65.0</td>
      <td>88.0</td>
      <td>85.0</td>
      <td>88.0</td>
      <td>87.0</td>
      <td>68.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M. Neuer</td>
      <td>31</td>
      <td>Germany</td>
      <td>92</td>
      <td>92</td>
      <td>FC Bayern Munich</td>
      <td>61000000</td>
      <td>230000</td>
      <td>1493</td>
      <td>58</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 71 columns</p>
</div>





```
df1_clean.columns
```





    Index(['Name', 'Age', 'Nationality', 'Overall', 'Potential', 'Club', 'Value',
           'Wage', 'Special', 'Acceleration', 'Aggression', 'Agility', 'Balance',
           'Ball control', 'Composure', 'Crossing', 'Curve', 'Dribbling',
           'Finishing', 'Free kick accuracy', 'GK diving', 'GK handling',
           'GK kicking', 'GK positioning', 'GK reflexes', 'Heading accuracy',
           'Interceptions', 'Jumping', 'Long passing', 'Long shots', 'Marking',
           'Penalties', 'Positioning', 'Reactions', 'Short passing', 'Shot power',
           'Sliding tackle', 'Sprint speed', 'Stamina', 'Standing tackle',
           'Strength', 'Vision', 'Volleys', 'CAM', 'CB', 'CDM', 'CF', 'CM', 'ID',
           'LAM', 'LB', 'LCB', 'LCM', 'LDM', 'LF', 'LM', 'LS', 'LW', 'LWB',
           'Preferred Positions', 'RAM', 'RB', 'RCB', 'RCM', 'RDM', 'RF', 'RM',
           'RS', 'RW', 'RWB', 'ST'],
          dtype='object')





```
# limit the players for only the players in the world cup
df1_clean=df1_clean[df1_clean['Nationality'].isin(countrylist)]
display(df1_clean.shape)
df1_clean.columns
df1_clean.dtypes
```



    (11686, 71)





    Name                    object
    Age                      int64
    Nationality             object
    Overall                  int64
    Potential                int64
    Club                    object
    Value                    int64
    Wage                     int64
    Special                  int64
    Acceleration            object
    Aggression              object
    Agility                 object
    Balance                 object
    Ball control            object
    Composure               object
    Crossing                object
    Curve                   object
    Dribbling               object
    Finishing               object
    Free kick accuracy      object
    GK diving               object
    GK handling             object
    GK kicking              object
    GK positioning          object
    GK reflexes             object
    Heading accuracy        object
    Interceptions           object
    Jumping                 object
    Long passing            object
    Long shots              object
                            ...   
    Vision                  object
    Volleys                 object
    CAM                    float64
    CB                     float64
    CDM                    float64
    CF                     float64
    CM                     float64
    ID                       int64
    LAM                    float64
    LB                     float64
    LCB                    float64
    LCM                    float64
    LDM                    float64
    LF                     float64
    LM                     float64
    LS                     float64
    LW                     float64
    LWB                    float64
    Preferred Positions     object
    RAM                    float64
    RB                     float64
    RCB                    float64
    RCM                    float64
    RDM                    float64
    RF                     float64
    RM                     float64
    RS                     float64
    RW                     float64
    RWB                    float64
    ST                     float64
    Length: 71, dtype: object



#Analysis of second model's full features dataframe



```
# change object type data to integer for pivot table and fill 0 in string values
df1_clean.iloc[:,9:43]=df1_clean.iloc[:,9:43].apply(pd.to_numeric, errors='coerce').fillna(0).astype(int)
```




```
players_pivot = pd.pivot_table(df1_clean,index='Nationality', aggfunc='mean')
players_pivot['country'] = players_pivot.index
players_pivot.head()
```





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
      <th>Acceleration</th>
      <th>Age</th>
      <th>Aggression</th>
      <th>Agility</th>
      <th>Balance</th>
      <th>Ball control</th>
      <th>CAM</th>
      <th>CB</th>
      <th>CDM</th>
      <th>CF</th>
      <th>...</th>
      <th>Special</th>
      <th>Sprint speed</th>
      <th>Stamina</th>
      <th>Standing tackle</th>
      <th>Strength</th>
      <th>Value</th>
      <th>Vision</th>
      <th>Volleys</th>
      <th>Wage</th>
      <th>country</th>
    </tr>
    <tr>
      <th>Nationality</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Argentina</th>
      <td>64.584456</td>
      <td>25.932642</td>
      <td>55.247668</td>
      <td>63.623834</td>
      <td>64.943005</td>
      <td>58.800000</td>
      <td>60.710465</td>
      <td>55.486047</td>
      <td>57.126744</td>
      <td>60.563953</td>
      <td>...</td>
      <td>1620.636269</td>
      <td>64.580311</td>
      <td>63.014508</td>
      <td>46.212435</td>
      <td>64.113990</td>
      <td>2.908699e+06</td>
      <td>53.920207</td>
      <td>44.503627</td>
      <td>12752.331606</td>
      <td>Argentina</td>
    </tr>
    <tr>
      <th>Australia</th>
      <td>64.303965</td>
      <td>24.312775</td>
      <td>53.154185</td>
      <td>61.814978</td>
      <td>62.607930</td>
      <td>52.779736</td>
      <td>55.578680</td>
      <td>54.482234</td>
      <td>54.989848</td>
      <td>55.223350</td>
      <td>...</td>
      <td>1515.881057</td>
      <td>65.105727</td>
      <td>61.832599</td>
      <td>47.422907</td>
      <td>64.894273</td>
      <td>7.339868e+05</td>
      <td>50.101322</td>
      <td>37.660793</td>
      <td>4114.537445</td>
      <td>Australia</td>
    </tr>
    <tr>
      <th>Belgium</th>
      <td>63.319853</td>
      <td>23.893382</td>
      <td>54.444853</td>
      <td>63.908088</td>
      <td>63.404412</td>
      <td>59.705882</td>
      <td>61.982684</td>
      <td>56.043290</td>
      <td>58.480519</td>
      <td>61.411255</td>
      <td>...</td>
      <td>1619.849265</td>
      <td>63.856618</td>
      <td>61.088235</td>
      <td>45.474265</td>
      <td>63.783088</td>
      <td>4.450092e+06</td>
      <td>54.628676</td>
      <td>44.919118</td>
      <td>19194.852941</td>
      <td>Belgium</td>
    </tr>
    <tr>
      <th>Brazil</th>
      <td>66.439655</td>
      <td>27.208128</td>
      <td>59.816502</td>
      <td>65.306650</td>
      <td>64.038177</td>
      <td>63.248768</td>
      <td>63.122340</td>
      <td>58.454787</td>
      <td>60.143617</td>
      <td>63.029255</td>
      <td>...</td>
      <td>1710.948276</td>
      <td>66.811576</td>
      <td>65.019704</td>
      <td>50.869458</td>
      <td>67.185961</td>
      <td>4.008898e+06</td>
      <td>57.556650</td>
      <td>49.998768</td>
      <td>18736.453202</td>
      <td>Brazil</td>
    </tr>
    <tr>
      <th>Colombia</th>
      <td>66.429054</td>
      <td>25.388514</td>
      <td>53.596284</td>
      <td>64.327703</td>
      <td>64.927365</td>
      <td>57.097973</td>
      <td>58.020408</td>
      <td>54.352505</td>
      <td>55.458256</td>
      <td>57.873840</td>
      <td>...</td>
      <td>1571.844595</td>
      <td>66.322635</td>
      <td>65.275338</td>
      <td>46.072635</td>
      <td>65.403716</td>
      <td>1.708860e+06</td>
      <td>51.471284</td>
      <td>40.246622</td>
      <td>5859.797297</td>
      <td>Colombia</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 68 columns</p>
</div>





```
# this dataframe englobes all the predictors that make our own model
result2 = pd.merge(df_wins, players_pivot, on='country')
result2 = pd.merge(result2,df_ranking, on='country')
result2= pd.merge(result2, players_features,on='country')
result2 = result2.set_index('country')
# drop non relevant columns
result2=result2.drop(['ranking','ID','age','caps','goals'], axis=1)
result2.loc['South Korea'] = result2.mean()
result2.head()
```





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
      <th># won</th>
      <th>Acceleration</th>
      <th>Age</th>
      <th>Aggression</th>
      <th>Agility</th>
      <th>Balance</th>
      <th>Ball control</th>
      <th>CAM</th>
      <th>CB</th>
      <th>CDM</th>
      <th>...</th>
      <th>Sprint speed</th>
      <th>Stamina</th>
      <th>Standing tackle</th>
      <th>Strength</th>
      <th>Value</th>
      <th>Vision</th>
      <th>Volleys</th>
      <th>Wage</th>
      <th>CL League 2017-2018</th>
      <th>Top League</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Egypt</th>
      <td>6.0</td>
      <td>66.562500</td>
      <td>27.250000</td>
      <td>59.187500</td>
      <td>64.906250</td>
      <td>66.468750</td>
      <td>61.718750</td>
      <td>62.620690</td>
      <td>58.551724</td>
      <td>60.241379</td>
      <td>...</td>
      <td>64.843750</td>
      <td>65.125000</td>
      <td>51.750000</td>
      <td>69.437500</td>
      <td>2.958437e+06</td>
      <td>56.875000</td>
      <td>47.937500</td>
      <td>16062.500000</td>
      <td>1.0</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>5.0</td>
      <td>62.169935</td>
      <td>25.232026</td>
      <td>52.862745</td>
      <td>60.238562</td>
      <td>61.692810</td>
      <td>54.127451</td>
      <td>59.816000</td>
      <td>57.020000</td>
      <td>58.032000</td>
      <td>...</td>
      <td>61.130719</td>
      <td>58.555556</td>
      <td>46.627451</td>
      <td>62.944444</td>
      <td>2.006209e+06</td>
      <td>51.111111</td>
      <td>41.415033</td>
      <td>16637.254902</td>
      <td>8.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>Saudi Arabia</th>
      <td>2.0</td>
      <td>63.793313</td>
      <td>25.252280</td>
      <td>51.954407</td>
      <td>62.747720</td>
      <td>65.243161</td>
      <td>50.693009</td>
      <td>54.134752</td>
      <td>52.198582</td>
      <td>52.755319</td>
      <td>...</td>
      <td>63.942249</td>
      <td>61.060790</td>
      <td>43.650456</td>
      <td>62.358663</td>
      <td>5.641641e+05</td>
      <td>47.890578</td>
      <td>36.796353</td>
      <td>6990.881459</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>Uruguay</th>
      <td>7.0</td>
      <td>65.196078</td>
      <td>26.124183</td>
      <td>57.660131</td>
      <td>62.333333</td>
      <td>64.287582</td>
      <td>60.836601</td>
      <td>61.507246</td>
      <td>56.028986</td>
      <td>57.550725</td>
      <td>...</td>
      <td>65.633987</td>
      <td>63.464052</td>
      <td>47.437908</td>
      <td>65.980392</td>
      <td>4.146046e+06</td>
      <td>52.588235</td>
      <td>46.594771</td>
      <td>17333.333333</td>
      <td>7.0</td>
      <td>11.0</td>
    </tr>
    <tr>
      <th>Iran</th>
      <td>4.0</td>
      <td>66.764706</td>
      <td>24.882353</td>
      <td>57.058824</td>
      <td>65.470588</td>
      <td>62.941176</td>
      <td>57.117647</td>
      <td>64.142857</td>
      <td>53.142857</td>
      <td>55.571429</td>
      <td>...</td>
      <td>67.352941</td>
      <td>59.000000</td>
      <td>39.411765</td>
      <td>64.647059</td>
      <td>2.822353e+06</td>
      <td>58.176471</td>
      <td>50.764706</td>
      <td>9117.647059</td>
      <td>2.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 69 columns</p>
</div>





```
# Standardize the feature dataset
result2_std = (result2-result2.mean())/result2.std()
result2_std
```





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
      <th># won</th>
      <th>Acceleration</th>
      <th>Age</th>
      <th>Aggression</th>
      <th>Agility</th>
      <th>Balance</th>
      <th>Ball control</th>
      <th>CAM</th>
      <th>CB</th>
      <th>CDM</th>
      <th>...</th>
      <th>Sprint speed</th>
      <th>Stamina</th>
      <th>Standing tackle</th>
      <th>Strength</th>
      <th>Value</th>
      <th>Vision</th>
      <th>Volleys</th>
      <th>Wage</th>
      <th>CL League 2017-2018</th>
      <th>Top League</th>
    </tr>
    <tr>
      <th>country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Egypt</th>
      <td>-0.213267</td>
      <td>0.590170</td>
      <td>2.047348</td>
      <td>0.754439</td>
      <td>0.594921</td>
      <td>1.138137</td>
      <td>0.841515</td>
      <td>1.037926</td>
      <td>1.374589</td>
      <td>1.473563</td>
      <td>...</td>
      <td>-0.072050</td>
      <td>0.879177</td>
      <td>1.606122</td>
      <td>1.296947</td>
      <td>0.298354</td>
      <td>1.174404</td>
      <td>0.914263</td>
      <td>0.699775</td>
      <td>-0.939500</td>
      <td>-0.755005</td>
    </tr>
    <tr>
      <th>Russia</th>
      <td>-0.528089</td>
      <td>-0.976277</td>
      <td>-0.045982</td>
      <td>-1.085022</td>
      <td>-1.363505</td>
      <td>-0.826752</td>
      <td>-1.116339</td>
      <td>0.010670</td>
      <td>0.653287</td>
      <td>0.469538</td>
      <td>...</td>
      <td>-1.380452</td>
      <td>-2.089858</td>
      <td>-0.187332</td>
      <td>-0.942367</td>
      <td>-0.444818</td>
      <td>-0.642398</td>
      <td>-0.736420</td>
      <td>0.807301</td>
      <td>0.205846</td>
      <td>-1.643808</td>
    </tr>
    <tr>
      <th>Saudi Arabia</th>
      <td>-1.472556</td>
      <td>-0.397359</td>
      <td>-0.024972</td>
      <td>-1.349199</td>
      <td>-0.310735</td>
      <td>0.633912</td>
      <td>-2.002107</td>
      <td>-2.070163</td>
      <td>-1.617160</td>
      <td>-1.928384</td>
      <td>...</td>
      <td>-0.389722</td>
      <td>-0.957627</td>
      <td>-1.229607</td>
      <td>-1.144390</td>
      <td>-1.570271</td>
      <td>-1.657523</td>
      <td>-1.905300</td>
      <td>-0.997344</td>
      <td>-1.103121</td>
      <td>-1.347540</td>
    </tr>
    <tr>
      <th>Uruguay</th>
      <td>0.101556</td>
      <td>0.102886</td>
      <td>0.879490</td>
      <td>0.310227</td>
      <td>-0.484600</td>
      <td>0.240774</td>
      <td>0.614002</td>
      <td>0.630112</td>
      <td>0.186610</td>
      <td>0.250829</td>
      <td>...</td>
      <td>0.206415</td>
      <td>0.128518</td>
      <td>0.096417</td>
      <td>0.104666</td>
      <td>1.225231</td>
      <td>-0.176802</td>
      <td>0.574450</td>
      <td>0.937523</td>
      <td>0.042225</td>
      <td>-0.162469</td>
    </tr>
    <tr>
      <th>Iran</th>
      <td>-0.842911</td>
      <td>0.662279</td>
      <td>-0.408713</td>
      <td>0.135345</td>
      <td>0.831701</td>
      <td>-0.313156</td>
      <td>-0.345144</td>
      <td>1.595440</td>
      <td>-1.172493</td>
      <td>-0.648638</td>
      <td>...</td>
      <td>0.812142</td>
      <td>-1.888993</td>
      <td>-2.713613</td>
      <td>-0.355172</td>
      <td>0.192146</td>
      <td>1.584633</td>
      <td>1.629763</td>
      <td>-0.599469</td>
      <td>-0.775880</td>
      <td>-1.643808</td>
    </tr>
    <tr>
      <th>Morocco</th>
      <td>-0.213267</td>
      <td>2.085774</td>
      <td>-0.526060</td>
      <td>0.233826</td>
      <td>3.033341</td>
      <td>1.878879</td>
      <td>2.587144</td>
      <td>1.784080</td>
      <td>-1.134430</td>
      <td>-0.119030</td>
      <td>...</td>
      <td>1.324769</td>
      <td>0.191118</td>
      <td>0.041850</td>
      <td>-1.170812</td>
      <td>1.003035</td>
      <td>2.276608</td>
      <td>2.078658</td>
      <td>0.745646</td>
      <td>-0.448638</td>
      <td>-0.014336</td>
    </tr>
    <tr>
      <th>Portugal</th>
      <td>1.046022</td>
      <td>0.424556</td>
      <td>-0.015324</td>
      <td>1.069197</td>
      <td>1.427668</td>
      <td>0.900446</td>
      <td>1.063736</td>
      <td>1.427749</td>
      <td>1.371113</td>
      <td>1.657102</td>
      <td>...</td>
      <td>0.237454</td>
      <td>0.536985</td>
      <td>1.290117</td>
      <td>-0.505030</td>
      <td>1.588975</td>
      <td>0.935531</td>
      <td>0.810093</td>
      <td>0.503042</td>
      <td>1.023950</td>
      <td>-0.162469</td>
    </tr>
    <tr>
      <th>Spain</th>
      <td>1.046022</td>
      <td>-0.373347</td>
      <td>-0.066785</td>
      <td>0.184453</td>
      <td>0.169805</td>
      <td>0.321504</td>
      <td>0.785477</td>
      <td>1.073893</td>
      <td>1.279596</td>
      <td>1.606238</td>
      <td>...</td>
      <td>-0.413635</td>
      <td>0.098417</td>
      <td>1.231532</td>
      <td>-0.558360</td>
      <td>1.515090</td>
      <td>0.886227</td>
      <td>-0.121715</td>
      <td>1.185801</td>
      <td>1.842054</td>
      <td>1.615137</td>
    </tr>
    <tr>
      <th>Australia</th>
      <td>-1.157733</td>
      <td>-0.215254</td>
      <td>-0.999560</td>
      <td>-1.000261</td>
      <td>-0.702086</td>
      <td>-0.450259</td>
      <td>-1.463925</td>
      <td>-1.541305</td>
      <td>-0.541769</td>
      <td>-0.912930</td>
      <td>...</td>
      <td>0.020266</td>
      <td>-0.608811</td>
      <td>0.091165</td>
      <td>-0.269913</td>
      <td>-1.437732</td>
      <td>-0.960688</td>
      <td>-1.686530</td>
      <td>-1.535451</td>
      <td>-0.775880</td>
      <td>-0.458737</td>
    </tr>
    <tr>
      <th>Denmark</th>
      <td>0.416378</td>
      <td>-1.117271</td>
      <td>-0.946255</td>
      <td>-0.709719</td>
      <td>-0.862133</td>
      <td>-0.699180</td>
      <td>-0.752658</td>
      <td>-1.098784</td>
      <td>-0.707098</td>
      <td>-0.805509</td>
      <td>...</td>
      <td>-0.621945</td>
      <td>-0.230116</td>
      <td>-0.559802</td>
      <td>0.021720</td>
      <td>-0.764767</td>
      <td>-0.585410</td>
      <td>-1.068932</td>
      <td>-0.957790</td>
      <td>-0.285017</td>
      <td>0.726334</td>
    </tr>
    <tr>
      <th>France</th>
      <td>1.675667</td>
      <td>-0.381993</td>
      <td>-0.665335</td>
      <td>-0.173490</td>
      <td>-0.461433</td>
      <td>0.090908</td>
      <td>0.008738</td>
      <td>0.151400</td>
      <td>0.555029</td>
      <td>0.539678</td>
      <td>...</td>
      <td>-0.500524</td>
      <td>-0.810884</td>
      <td>0.299654</td>
      <td>-0.240856</td>
      <td>0.596582</td>
      <td>-0.062160</td>
      <td>-0.337372</td>
      <td>0.366144</td>
      <td>1.842054</td>
      <td>1.615137</td>
    </tr>
    <tr>
      <th>Peru</th>
      <td>-0.528089</td>
      <td>1.114688</td>
      <td>0.716092</td>
      <td>-0.308321</td>
      <td>0.718170</td>
      <td>2.138387</td>
      <td>-0.736557</td>
      <td>0.145889</td>
      <td>0.347371</td>
      <td>0.337179</td>
      <td>...</td>
      <td>0.147454</td>
      <td>-0.081209</td>
      <td>0.795034</td>
      <td>-1.394540</td>
      <td>-0.310613</td>
      <td>-2.169387</td>
      <td>0.305825</td>
      <td>-1.089179</td>
      <td>-0.939500</td>
      <td>-1.643808</td>
    </tr>
    <tr>
      <th>Argentina</th>
      <td>0.416378</td>
      <td>-0.115227</td>
      <td>0.680797</td>
      <td>-0.391402</td>
      <td>0.056857</td>
      <td>0.510424</td>
      <td>0.088747</td>
      <td>0.338280</td>
      <td>-0.069065</td>
      <td>0.058156</td>
      <td>...</td>
      <td>-0.164881</td>
      <td>-0.074652</td>
      <td>-0.332633</td>
      <td>-0.539016</td>
      <td>0.259536</td>
      <td>0.243041</td>
      <td>0.045230</td>
      <td>0.080509</td>
      <td>1.187571</td>
      <td>0.281932</td>
    </tr>
    <tr>
      <th>Croatia</th>
      <td>1.046022</td>
      <td>-0.866751</td>
      <td>-0.305707</td>
      <td>0.414409</td>
      <td>-1.240341</td>
      <td>-2.164852</td>
      <td>0.216045</td>
      <td>0.362104</td>
      <td>0.816856</td>
      <td>0.723949</td>
      <td>...</td>
      <td>-0.660329</td>
      <td>0.246349</td>
      <td>0.675440</td>
      <td>1.136571</td>
      <td>2.058715</td>
      <td>0.285543</td>
      <td>0.442502</td>
      <td>2.354641</td>
      <td>0.205846</td>
      <td>0.578200</td>
    </tr>
    <tr>
      <th>Iceland</th>
      <td>-1.472556</td>
      <td>-0.619708</td>
      <td>0.417937</td>
      <td>1.034643</td>
      <td>0.088022</td>
      <td>0.308759</td>
      <td>0.471229</td>
      <td>0.190184</td>
      <td>0.115298</td>
      <td>0.074753</td>
      <td>...</td>
      <td>0.428472</td>
      <td>2.229686</td>
      <td>0.372481</td>
      <td>0.911806</td>
      <td>-0.728861</td>
      <td>0.969967</td>
      <td>0.992156</td>
      <td>-0.356742</td>
      <td>-1.103121</td>
      <td>-0.606871</td>
    </tr>
    <tr>
      <th>Nigeria</th>
      <td>-0.842911</td>
      <td>2.758506</td>
      <td>-1.925016</td>
      <td>0.720682</td>
      <td>1.536667</td>
      <td>0.886513</td>
      <td>1.153537</td>
      <td>0.170373</td>
      <td>-1.986328</td>
      <td>-2.013006</td>
      <td>...</td>
      <td>2.983843</td>
      <td>1.428865</td>
      <td>-1.587934</td>
      <td>1.458096</td>
      <td>-0.202489</td>
      <td>-0.142074</td>
      <td>1.337815</td>
      <td>0.022909</td>
      <td>-0.775880</td>
      <td>-0.310603</td>
    </tr>
    <tr>
      <th>Brazil</th>
      <td>1.990489</td>
      <td>0.546362</td>
      <td>2.003913</td>
      <td>0.937376</td>
      <td>0.762918</td>
      <td>0.138165</td>
      <td>1.236118</td>
      <td>1.221662</td>
      <td>1.328941</td>
      <td>1.429137</td>
      <td>...</td>
      <td>0.621375</td>
      <td>0.831589</td>
      <td>1.297836</td>
      <td>0.520440</td>
      <td>1.118193</td>
      <td>1.389263</td>
      <td>1.435922</td>
      <td>1.200019</td>
      <td>1.842054</td>
      <td>0.726334</td>
    </tr>
    <tr>
      <th>Costa Rica</th>
      <td>-0.842911</td>
      <td>1.865216</td>
      <td>1.108374</td>
      <td>1.391895</td>
      <td>1.458930</td>
      <td>0.831793</td>
      <td>0.700611</td>
      <td>0.965547</td>
      <td>0.734428</td>
      <td>0.874477</td>
      <td>...</td>
      <td>2.060843</td>
      <td>-0.050040</td>
      <td>-0.745046</td>
      <td>0.147105</td>
      <td>0.360665</td>
      <td>0.833386</td>
      <td>0.327933</td>
      <td>1.068694</td>
      <td>-0.612259</td>
      <td>-0.903139</td>
    </tr>
    <tr>
      <th>Serbia</th>
      <td>-1.157733</td>
      <td>-1.082461</td>
      <td>0.142303</td>
      <td>1.259711</td>
      <td>-1.507764</td>
      <td>-2.169705</td>
      <td>0.652266</td>
      <td>0.541997</td>
      <td>2.029125</td>
      <td>1.693339</td>
      <td>...</td>
      <td>-1.097979</td>
      <td>0.707149</td>
      <td>1.333010</td>
      <td>1.620595</td>
      <td>1.050895</td>
      <td>0.417499</td>
      <td>0.427733</td>
      <td>0.991912</td>
      <td>-0.285017</td>
      <td>0.133798</td>
    </tr>
    <tr>
      <th>Switzerland</th>
      <td>0.416378</td>
      <td>-1.136363</td>
      <td>-1.266138</td>
      <td>-1.274776</td>
      <td>-1.782328</td>
      <td>-0.679181</td>
      <td>-1.042947</td>
      <td>-1.028127</td>
      <td>-0.340758</td>
      <td>-0.566341</td>
      <td>...</td>
      <td>-1.057498</td>
      <td>-1.305148</td>
      <td>-0.295815</td>
      <td>-0.516162</td>
      <td>-0.448039</td>
      <td>-0.812701</td>
      <td>-1.203148</td>
      <td>-0.369362</td>
      <td>-0.121396</td>
      <td>1.022602</td>
    </tr>
    <tr>
      <th>Germany</th>
      <td>0.101556</td>
      <td>-0.730003</td>
      <td>-1.113817</td>
      <td>-0.339956</td>
      <td>-0.547535</td>
      <td>-0.772187</td>
      <td>-0.576609</td>
      <td>-0.633114</td>
      <td>0.094397</td>
      <td>-0.095809</td>
      <td>...</td>
      <td>-0.556999</td>
      <td>-0.229875</td>
      <td>0.000531</td>
      <td>0.195833</td>
      <td>0.035906</td>
      <td>-0.857419</td>
      <td>-0.800608</td>
      <td>-0.168219</td>
      <td>1.514812</td>
      <td>1.615137</td>
    </tr>
    <tr>
      <th>Mexico</th>
      <td>1.046022</td>
      <td>-0.394975</td>
      <td>-0.361592</td>
      <td>-1.368254</td>
      <td>-0.234023</td>
      <td>1.032140</td>
      <td>-0.418471</td>
      <td>-0.123973</td>
      <td>0.457025</td>
      <td>0.394893</td>
      <td>...</td>
      <td>-0.619954</td>
      <td>-0.868348</td>
      <td>0.448815</td>
      <td>-1.463516</td>
      <td>-0.680235</td>
      <td>-0.308807</td>
      <td>-0.369046</td>
      <td>-0.081024</td>
      <td>-0.448638</td>
      <td>-0.903139</td>
    </tr>
    <tr>
      <th>Sweden</th>
      <td>-0.213267</td>
      <td>-0.788800</td>
      <td>-0.162643</td>
      <td>0.276300</td>
      <td>-0.686024</td>
      <td>-1.101722</td>
      <td>-0.588540</td>
      <td>-0.611047</td>
      <td>-1.030791</td>
      <td>-0.723226</td>
      <td>...</td>
      <td>-0.744734</td>
      <td>0.197573</td>
      <td>-1.068221</td>
      <td>0.255752</td>
      <td>-0.938902</td>
      <td>0.185108</td>
      <td>-0.287163</td>
      <td>-1.104938</td>
      <td>-0.612259</td>
      <td>0.281932</td>
    </tr>
    <tr>
      <th>Belgium</th>
      <td>2.305311</td>
      <td>-0.566201</td>
      <td>-1.434615</td>
      <td>-0.624889</td>
      <td>0.176122</td>
      <td>-0.122575</td>
      <td>0.322381</td>
      <td>0.804248</td>
      <td>0.193346</td>
      <td>0.673363</td>
      <td>...</td>
      <td>-0.419897</td>
      <td>-0.945223</td>
      <td>-0.591074</td>
      <td>-0.653137</td>
      <td>1.462525</td>
      <td>0.466353</td>
      <td>0.150381</td>
      <td>1.285777</td>
      <td>1.678433</td>
      <td>1.022602</td>
    </tr>
    <tr>
      <th>England</th>
      <td>1.046022</td>
      <td>0.055933</td>
      <td>-1.407385</td>
      <td>-0.993182</td>
      <td>-0.288800</td>
      <td>0.027556</td>
      <td>-0.937187</td>
      <td>-1.237465</td>
      <td>-1.173741</td>
      <td>-1.366502</td>
      <td>...</td>
      <td>-0.156430</td>
      <td>-0.349881</td>
      <td>-0.783967</td>
      <td>-0.721358</td>
      <td>-0.922884</td>
      <td>-1.046770</td>
      <td>-1.058977</td>
      <td>-0.123478</td>
      <td>1.514812</td>
      <td>1.615137</td>
    </tr>
    <tr>
      <th>Panama</th>
      <td>-1.157733</td>
      <td>0.055250</td>
      <td>0.556168</td>
      <td>1.863249</td>
      <td>-0.336002</td>
      <td>-0.983217</td>
      <td>0.269283</td>
      <td>-1.615906</td>
      <td>-0.033062</td>
      <td>-0.794707</td>
      <td>...</td>
      <td>-0.016990</td>
      <td>1.302876</td>
      <td>1.124721</td>
      <td>2.116033</td>
      <td>-1.383528</td>
      <td>-0.972924</td>
      <td>-0.098043</td>
      <td>-1.685497</td>
      <td>-1.103121</td>
      <td>-1.643808</td>
    </tr>
    <tr>
      <th>Tunisia</th>
      <td>-0.528089</td>
      <td>0.159502</td>
      <td>1.955326</td>
      <td>1.168997</td>
      <td>0.580118</td>
      <td>-0.142970</td>
      <td>1.230196</td>
      <td>0.419909</td>
      <td>-0.360736</td>
      <td>-0.060033</td>
      <td>...</td>
      <td>0.096681</td>
      <td>0.866420</td>
      <td>0.146390</td>
      <td>0.901311</td>
      <td>-0.669450</td>
      <td>1.284979</td>
      <td>1.174993</td>
      <td>-0.313701</td>
      <td>-1.103121</td>
      <td>-0.606871</td>
    </tr>
    <tr>
      <th>Colombia</th>
      <td>-0.842911</td>
      <td>0.542581</td>
      <td>0.116349</td>
      <td>-0.871683</td>
      <td>0.352180</td>
      <td>0.503989</td>
      <td>-0.350218</td>
      <td>-0.646989</td>
      <td>-0.602859</td>
      <td>-0.700068</td>
      <td>...</td>
      <td>0.449081</td>
      <td>0.947122</td>
      <td>-0.381578</td>
      <td>-0.094217</td>
      <td>-0.676886</td>
      <td>-0.528870</td>
      <td>-1.032118</td>
      <td>-1.208948</td>
      <td>-0.121396</td>
      <td>0.133798</td>
    </tr>
    <tr>
      <th>Japan</th>
      <td>0.416378</td>
      <td>-1.315934</td>
      <td>1.336802</td>
      <td>-2.083181</td>
      <td>-0.304187</td>
      <td>0.893531</td>
      <td>-1.375222</td>
      <td>-1.354988</td>
      <td>-1.598661</td>
      <td>-1.442170</td>
      <td>...</td>
      <td>-1.272489</td>
      <td>-0.982210</td>
      <td>-1.123651</td>
      <td>-1.779152</td>
      <td>-1.387052</td>
      <td>-0.905255</td>
      <td>-1.386460</td>
      <td>-1.549699</td>
      <td>-0.939500</td>
      <td>0.133798</td>
    </tr>
    <tr>
      <th>Poland</th>
      <td>-0.528089</td>
      <td>-1.176561</td>
      <td>-0.046576</td>
      <td>-0.774118</td>
      <td>-0.897115</td>
      <td>-0.970169</td>
      <td>-1.299209</td>
      <td>-0.757275</td>
      <td>-0.411958</td>
      <td>-0.476202</td>
      <td>...</td>
      <td>-1.282217</td>
      <td>-0.863062</td>
      <td>-0.778935</td>
      <td>-0.220146</td>
      <td>-0.846168</td>
      <td>-0.716704</td>
      <td>-0.783631</td>
      <td>-0.976764</td>
      <td>0.042225</td>
      <td>0.430066</td>
    </tr>
    <tr>
      <th>Senegal</th>
      <td>-0.528089</td>
      <td>1.290783</td>
      <td>-0.238424</td>
      <td>1.592704</td>
      <td>0.221190</td>
      <td>-1.079891</td>
      <td>0.764105</td>
      <td>-0.152329</td>
      <td>1.243898</td>
      <td>0.396359</td>
      <td>...</td>
      <td>2.039932</td>
      <td>1.744093</td>
      <td>1.528093</td>
      <td>1.881268</td>
      <td>0.646847</td>
      <td>-0.386651</td>
      <td>0.227744</td>
      <td>0.867912</td>
      <td>-0.448638</td>
      <td>0.874468</td>
    </tr>
    <tr>
      <th>South Korea</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>32 rows × 69 columns</p>
</div>



# Results of second model

##Logistic regression- Second model

### Training Data Set



```
# fit the logistic regression model
reg2 = LogisticRegression(penalty='l2',multi_class='multinomial',solver='lbfgs',random_state=123456)
x,y = prepare_data(x_train,y_train,result2_std)
reg2.fit(x,y)
ypredmf1 = reg2.predict(x)
df_pred_full_lr = confusion_matrix_model(reg2,x,y)
display("conf matrix of logisitic regression of our model",df_pred_full_lr) 
print("accuracy score or the model logistic regression multinomial model is:",accuracy_score(ypredmf1,y))

```



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



```
# test set code
x,y = prepare_data(x_test1,y_test1,result2_std)
ypredmf1_test = reg2.predict(x)
df_predmf1_test = encoder(ypredmf1_test,y_test1,x_test1)
display(df_predmf1_test.head())
df_pred_full_lr_test = confusion_matrix_model(reg2,x,y)
display("conf matrix of logisitic regression of our model",df_pred_full_lr_test) 
print("accuracy score of the model logistic regression multinomial model on test set of our model is:",accuracy_score(ypredmf1_test,y))
```



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




```
# finding top 2 components
var_explained = []
total_comp = 20
x,y = prepare_data(x_train,y_train,result2_std)
pca = PCA(n_components = total_comp).fit(x)

plt.plot(np.linspace(1, total_comp, total_comp), np.cumsum(pca.explained_variance_ratio_))
plt.xlabel('number of components')
plt.ylabel('variance explained')
plt.title('Cumulative variance explained',fontsize=15)

print("number of components that explain at least 90% of the variance=",\
    len(np.where(np.cumsum(pca.explained_variance_ratio_)<=0.9)[0])+1)
```


    number of components that explain at least 90% of the variance= 10



![png](milst4_files/milst4_137_1.png)


## KNN model- Second model

### Training Data Set



```
# fit knn
KNN2=KNeighborsClassifier(n_neighbors=8,p=1,weights='uniform')
x,y = prepare_data(x_train,y_train,result2_std)
KNN2.fit(x,y)
ypredmf3 = KNN2.predict(x)
df_pred_mat_knn = confusion_matrix_model(KNN2,x,y)
display("conf matrix of the KNN model of our model",df_pred_mat_knn)  
print("accuracy score of the knn model:",accuracy_score(ypredmf3,y))  
```



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


    accuracy score of the knn model: 0.6156004489337823


### World Cup 2018 - Group  Phase games



```
# test set code
x,y = prepare_data(x_test1,y_test1,result2_std)
ypredmf2_test = KNN2.predict(x)
df_predmf2_test = encoder(ypredmf2_test,y_test1,x_test1)
display(df_predmf2_test.head())
df_pred_full_KNN_test = confusion_matrix_model(KNN2,x,y)
display("conf matrix of KNN of our model",df_pred_full_KNN_test) 
print("accuracy score or the model KNN model on test set is:",accuracy_score(ypredmf2_test,y))
```



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



```
# test set code
x,y = prepare_data(x_test2,y_test2,result2_std)
ypredmf2_test2 = KNN2.predict(x)
df_predmf2_test2 = encoder(ypredmf2_test2,y_test2,x_test2)
display(df_predmf2_test2.head())
df_pred_full_KNN_test2 = confusion_matrix_model_knockout(KNN2,x,y)
display("conf matrix of KNN of our model",df_pred_full_KNN_test2) 
print("accuracy score or the model KNN model on test set is:",accuracy_score(ypredmf2_test2,y))
```



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



```
x,y = prepare_data(x_train,y_train,result2_std)
# fitting DecisionTreeClassifier class for depths 1-25, storing results
tree_cv_scores_om = []
for i in range (1,35):
  clf = DecisionTreeClassifier(criterion='gini', max_depth=i)
  clf.fit(x, y)
  train_score = clf.score(x, y)
  cv_score = cross_val_score(clf, x, y, scoring='accuracy', cv=5)
#   test_score = clf.score(x_test, y_test)
  tree_cv_scores_om.append({'Depth': i,
                         'Train Score': train_score,
                        'CV Mean Accuracy': cv_score.mean(),
                        'CV std.': cv_score.std(),
#                         'Test Score': test_score
                        })

columns=['Depth', 'Train Score', 'CV Mean Accuracy', 'CV std.'] # to preserve order
tree_scores_df_om = pd.DataFrame(tree_cv_scores_om, columns=columns)
```




```
# plot
num_SD = 2 # number of CV std for plotting

plt.figure(figsize =(10,6))
plt.plot(tree_scores_df_om['Depth'], tree_scores_df_om['Train Score'], label='Training Set Score')
plt.plot(tree_scores_df_om['Depth'], tree_scores_df_om['CV Mean Accuracy'],label='Mean CV Score (5 folds)',color='green')
plt.fill_between(
    tree_scores_df_om['Depth'],
    tree_scores_df_om['CV Mean Accuracy'] -  num_SD * tree_scores_df_om['CV std.'],
    tree_scores_df_om['CV Mean Accuracy'] +  num_SD * tree_scores_df_om['CV std.'],
    alpha=.1, color='green')

plt.legend()
plt.xlabel("Max Tree Depth")
plt.ylabel("Mean CV Accuracy Score +/- {} SD".format(num_SD))
plt.title("Accuracy Scores vs. Maximum Tree Depth on Decision Tree Classifier, Training Set");
```



![png](milst4_files/milst4_148_0.png)




```
best_index = tree_scores_df_om['CV Mean Accuracy'].idxmax() 
best_depth = tree_scores_df_om['Depth'][best_index]

tree_clf_om = DecisionTreeClassifier(max_depth=best_depth).fit(x,y)
best_depth
```





    18



### *** World Cup 2018 - Group  Phase games ***



```
x,y = prepare_data(x_test1,y_test1,result2_std)
ypred_test_om = tree_clf_om.predict(x)
df_pred_tree_test = encoder(ypred_test_om,y_test1,x_test1)
display(df_pred_tree_test.head())
df_pred_tree_test = confusion_matrix_model(tree_clf_om,x,y)
display("conf matrix of Tree of our own model",df_pred_tree_test) 
print("accuracy score for our own model with Decision Tree on test set is:",accuracy_score(ypred_test_om,y))
```



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



```
x,y = prepare_data(x_test2,y_test2,result2_std)
ypred_test2_om = tree_clf_om.predict(x)
df_pred_tree_test2 = encoder(ypred_test2_om,y_test2,x_test2)
display(df_pred_tree_test2.head())
df_pred_tree_test2_conf = confusion_matrix_model_knockout(tree_clf_om,x,y)
display("conf matrix of Tree of our own model",df_pred_tree_test2_conf) 
print("accuracy score for our own model with Decision Tree on test set is:",accuracy_score(ypred_test2_om,y))
```



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


    accuracy score for our own model with Decision Tree on test set is: 0.25


## *** LDA and QDA - Second Model ***

## LDA

###*** Training Data Set ***



```
x,y = prepare_data(x_train,y_train,result2_std)
lda_om = LinearDiscriminantAnalysis()
lda_om.fit(x, y)
```





    LinearDiscriminantAnalysis(n_components=None, priors=None, shrinkage=None,
                  solver='svd', store_covariance=False, tol=0.0001)





```
# print("THe CV accurary on LDA is: ", cross_val_score(lda_om, x, y, cv=7).mean())

ypred_train_ldaom = lda_om.predict(x)
df_pred_lda_train = encoder(ypred_train_ldaom,y_train,x_train)
display(df_pred_lda_train.head())
df_pred_lda_train = confusion_matrix_model(lda_om,x,y)
display("conf matrix of Tree of our own model",df_pred_lda_train) 
print("accuracy score for our own model with LDA  on train is:",accuracy_score(ypred_train_ldaom,y))

```



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



```
x,y = prepare_data(x_test1,y_test1,result2_std)

#lda Test set 1

ypred_test_ldaom = lda_om.predict(x)
df_pred_lda_test = encoder(ypred_test_ldaom,y_test1,x_test1)
display(df_pred_lda_test.head())
df_pred_lda_test1 = confusion_matrix_model(lda_om,x,y)
display("conf matrix of lda of our own model on test 1",df_pred_lda_test1.head()) 
print("accuracy score for our own model with LDA  on test set 1 is:",accuracy_score(ypred_test_ldaom,y))

#lda test set 2
x,y = prepare_data(x_test2,y_test2,result2_std)
ypred_test_ldaom = lda_om.predict(x)
df_pred_lda_test = encoder(ypred_test_ldaom,y_test2,x_test2)
display(df_pred_lda_test.head())
df_pred_lda_test = confusion_matrix_model(lda_om,x,y)
display("conf matrix of Tree of our own model",df_pred_lda_test) 
print("accuracy score for our own model with LDA  on test set 2 is:",accuracy_score(ypred_test_ldaom,y))


```



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



    'conf matrix of lda of our own model on test 1'



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



```
x,y = prepare_data(x_train,y_train,result2_std)
qda_om = QuadraticDiscriminantAnalysis()
qda_om.fit(x, y)
```





    QuadraticDiscriminantAnalysis(priors=None, reg_param=0.0,
                   store_covariance=False, store_covariances=None, tol=0.0001)





```
ypred_train_qdaom = qda_om.predict(x)
df_pred_qda_train = encoder(ypred_train_qdaom,y_train,x_train)
display(df_pred_qda_train.head())
df_pred_qda_train = confusion_matrix_model(qda_om,x,y)
display("conf matrix of Tree of our own model",df_pred_qda_train) 
print("accuracy score for our own model with QDA  on train is:",accuracy_score(ypred_train_qdaom,y))

```



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



```
# qda Test1

x,y = prepare_data(x_test1,y_test1,result2_std)
ypred_test_qdaom = qda_om.predict(x)
df_pred_qda_test = encoder(ypred_test_qdaom,y_test1,x_test1)
display(df_pred_qda_test.head())
df_pred_qda_test = confusion_matrix_model(qda_om,x,y)
display("conf matrix of Tree of our own model",df_pred_tree_test) 
print("accuracy score for our own model with qda  on test set 1 is:",accuracy_score(ypred_test_qdaom,y))

```



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



```
# qda test 2
x,y = prepare_data(x_test2,y_test2,result2_std)
ypred_test2_qdaom = qda_om.predict(x)
df_pred_qda_test2 = encoder(ypred_test2_qdaom,y_test2,x_test2)
display(df_pred_qda_test2.head())
df_pred_qda_test2 = confusion_matrix_model_knockout(qda_om,x,y)
display("conf matrix of Tree of our own model",df_pred_qda_test2) 
print("accuracy score for our own model with qda  on test set 2 is:",accuracy_score(ypred_test2_qdaom,y))



```



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



    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-211-463516d102f4> in <module>()
          3 df_pred_qda_test2 = encoder(ypred_test2_qdaom,y_test2,x_test2)
          4 display(df_pred_qda_test2.head())
    ----> 5 df_pred_qda_test2 = confusion_matrix_model_knockout(qda_om,x,y)
          6 display("conf matrix of Tree of our own model",df_pred_qda_test2)
          7 print("accuracy score for our own model with qda  on test set 2 is:",accuracy_score(ypred_test2_qdaom,y))


    <ipython-input-44-a8dacae91cce> in confusion_matrix_model_knockout(model_used, x, y)
         11     col=["Predicted Team 1","Predicted Team 2"]
         12     cm=pd.DataFrame(cm)
    ---> 13     cm.columns=col
         14     cm.index=["Actual Team 1","Actual Team 2"]
         15     return cm.T


    /usr/local/lib/python3.6/dist-packages/pandas/core/generic.py in __setattr__(self, name, value)
       3625         try:
       3626             object.__getattribute__(self, name)
    -> 3627             return object.__setattr__(self, name, value)
       3628         except AttributeError:
       3629             pass


    pandas/_libs/properties.pyx in pandas._libs.properties.AxisProperty.__set__()


    /usr/local/lib/python3.6/dist-packages/pandas/core/generic.py in _set_axis(self, axis, labels)
        557 
        558     def _set_axis(self, axis, labels):
    --> 559         self._data.set_axis(axis, labels)
        560         self._clear_item_cache()
        561 


    /usr/local/lib/python3.6/dist-packages/pandas/core/internals.py in set_axis(self, axis, new_labels)
       3072             raise ValueError('Length mismatch: Expected axis has %d elements, '
       3073                              'new values have %d elements' %
    -> 3074                              (old_len, new_len))
       3075 
       3076         self.axes[axis] = new_labels


    ValueError: Length mismatch: Expected axis has 3 elements, new values have 2 elements


##*** Random FOREST  -  Second Model***

###*** Training Data set ***



```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_train,y_train,result2_std)

max_rf_score= 0 
index = 0
for i in range(20):
    model = RandomForestClassifier(n_estimators=100, oob_score=True, max_depth=i+1,random_state=1234)
    model.fit(x, y)
    ypred=model.predict(x)
    if (accuracy_score(ypred,y)) > max_rf_score:
        index = i
        max_rf_score = accuracy_score(ypred,y)
        

```




```
# fit the random forest model and get its accuracy score
#x,y = prepare_data(x_train,y_train,result2_std)
rfml_om = RandomForestClassifier(n_estimators=50, oob_score=True, max_depth=12,random_state=1234)
rfml_om.fit(x, y)
ypredml2_om=rfml_om.predict(x)

print("accuracy score for the random forest model:",accuracy_score(ypredml2_om,y))
```


    accuracy score for the random forest model: 0.6745230078563412


###*** World Cup 2018 - Group  Phase games ***



```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_test1,y_test1,result2_std)
ypredml2_om=rfml_om.predict(x)

print("accuracy score for the random forest Test model:",accuracy_score(ypredml2_om,y))
```


    accuracy score for the random forest Test model: 0.5208333333333334




```
# fit the random forest model and get its accuracy score
x,y = prepare_data(x_test2,y_test2,result2_std)
ypredml2_om=rfml_om.predict(x)

print("accuracy score for the random forest Test model:",accuracy_score(ypredml2_om,y))
```


    accuracy score for the random forest Test model: 0.1875


##*** Bagging - Second Model ***



```

x,y = prepare_data(x_train,y_train,result2_std)
xt1,yt1 = prepare_data(x_test1,y_test1,result2_std)
xt2,yt2 = prepare_data(x_test2,y_test2,result2_std)

np.random.seed(0)

N_bootstraps = 45
bagging_train_om = np.zeros((x.shape[0], N_bootstraps),dtype=int)
bagging_test1_om = np.zeros((xt1.shape[0], N_bootstraps),dtype=int)
bagging_test2_om = np.zeros((xt2.shape[0], N_bootstraps),dtype=int)
bagging_trees_om = []

simpletree = DecisionTreeClassifier(max_depth=16)

# bootstrapping, saving results each time
for i in range(N_bootstraps):
  boot_xx, boot_y = resample(x, y)
  bagging_trees_om.append(simpletree.fit(boot_xx, boot_y))
  bagging_train_om[:, i] = simpletree.predict(x)
  bagging_test1_om[:, i] = simpletree.predict(xt1)
  bagging_test2_om[:, i] = simpletree.predict(xt2)


# format table
columns_om = ["Bootstrap-Model_"+str(i+1) for i in range(N_bootstraps)]
rows_train_om = ["Training-Row_" + str(i+1) for i in range(len(x))]
rows_test1_om = ["Test-Row_" + str(i+1) for i in range(len(xt1))]
rows_test2_om = ["Test-Row_" + str(i+1) for i in range(len(xt2))]


bagging_train_om = pd.DataFrame(bagging_train, columns=columns, index=rows_train)
bagging_test1_om = pd.DataFrame(bagging_test1, columns=columns, index=rows_test1)
bagging_test2_om = pd.DataFrame(bagging_test2, columns=columns, index=rows_test2)


print("train bootstrapped table:")
display(bagging_train_om.head())
print("\ntest1 bootstrapped table:")
display(bagging_test1_om.head())
print("\ntest2 bootstrapped table:")
display(bagging_test2_om.head())

```


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

