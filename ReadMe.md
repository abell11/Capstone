
# Capstone Project
## Trump Twitter Analysis and Predictability of Stock Market (SPY) Move 
### Problem Statement
The idea for this project stemmed from my previous job.  Ever since the announcement and further election of current President of the United States Donald Trump, I became amazed at the way a man in position of power used social media, specifically Twitter, to express his views and thoughts.  With every passing day information is more easily accessible and with that much more easy to spread.  Because of this, I thought it would be a great idea to analyze Donald Trump's twitter feed and see if there is any type of correlation in a stock market move.  To gauge this, I used the exchange traded fund (etf) SPY.  This etf is connected with the bigger SP500 and mirrors the same moves up and down.

### Executive Summary
First and foremost, I will state that I am not a financial advisor and the results of this project should not be used to make financial decisions. Please consult one if you would like further information...

The goal for this project is to take Donald Trump's twitter texts and dissect and anaylze them.  I used a Count Vectorizer and VADER analysis to do this.  In doing so, I plan to look for words that correlate postively or negatively to moves in SPY etf.  Using the analyzed text data, I used each day's SPY data (Open, High, Low, Close, and Volume) and its numerical features to gauge the impact of the twitter texts.  Having this framework, the ultimate goal is to be able to take a tweet from President Trump and then be able to accurately predict a move in the SPY etf either postively or negatively.  

The data collected from this project came from two different sources.  One is all of Donald Trump's twitter feed dating back to June 15, 2015 and going to August 20, 2019 (the last day collected for this project).  Together this accounted for over 17,000 tweets and instagram posts.  The data for this was taken from the website http://www.trumptwitterarchive.com/archive and using the parameters of the dates posted above.  This data was parsed and then exported via csv.  The other data needed for this project was market data on the SPY etf.  This includes the open, high, low, close, and volume for each day.  To get this, I used the yahoo website https://finance.yahoo.com/quote/SP/history?p=SP and exported this data via csv.  Once I had both csv's, I was able to import them both into a jupyter notebook and my analysis could begin.  Additional data and charts were used at www.stockcharts.com.

To create this into a classification problem, I created a new column called 'up_day'.  This column would be my target variable for each of my models going forward.  To create it, I took the Close price for each day of the SPY and with the Datetime index, I used a .pct_change() method for all the numerical data.  This would simply tell you the percent change move from the day previous.  Any positive number would be considered a positive move in the market, or an "up day."  Any negative number would be considered a negative move in market, or "down day." To get this into binary terms, I made an up_day equal to 1 and down_day equal to 0.  

Exploratory Data Analysis, or EDA, on the data took longer and was much more involved than I originally thought it would be.  After taking out the columns that I felt were an unncessary fit for my problem and working around a few nulls, my biggest problems came from using Datetime as an index as well as combining all of the text data together.  The first problem of Datetime indexing was hard but necessary as it was the only similar column within both data sets that I would be able to group on.  Once I joined the data sets together on date, the next problem that came up was what to do with the multiple texts that came from each day.  Since the same amount of texts weren't printed each day, I felt the best way to take care of the unbalanced issue was to take each days worth of tweets and combine them into one single string of text.  From here, my data would consist of the SPY market data and the grouped together tweets from that day. The texts from the tweets would undergo some sentiment analysis to determine the "feeling" of it. I used Valence Aware Dictionary and sEntiment Analysis, or VADER, to do this and added this info back into the data set. My text dissection wasn't complete yet.  Next I used a Count Vectorizer to find the most used and correlated words within all the text.  After a lot of trial and error, the parameters kept that performed the best were keeping stopwords in, having max features at 12,000, and having an n gram range of 3. 

The models used in this project were two types of Logistic Regression, a Gradient Boost Classifier, and a Random Forest Classifier.  All models used the metric of accuracy score to show whether they were a good at predicting an up SPY move or not.  The baseline accuracy score came out to be 54.18% and would be used to compare all the models against one another. This  score makes sense since if one looks at a chart of the SPY or even the overall market, the trend is to the positive just slightly.   

The first logistic model performed was a basic one with default parameters and had an accuracy score of 50%.  This was worse than the baseline model. I then used Gridsearch to find the best parameters and the best logistic regression model came with lasso regularization.  The accuracy score was 57% so just slightly better than baseline model.  With this model, I found the highest and lowest coefficient words within the texts of the tweets and market data in general.  The words with the highest likelihood of having an up SPY move were "healthcare", "york", "hillary clinton", "meeting", "mexico", "republican", and "border".  The words with the lowest likelihood for an up move (so highest for a down move in SPY) were Volume column, "justice", "president trump", "honor", "ohio", "wall", "country", "security", and "joe".  These words for the most part don't have large odds of predicting the move in the SPY etf.  Most of them though had odds very low and close to 0 likelihood of predicting correctly.  However the top words out of these lists for an up move were "healthcare" with a 1.23 chance of being positive if mentioned in tweet, followed by "hillary clinton" with 1.11, "meeting" with 1.08, and "mexico" with 1.07.  For a negative move in SPY, the Volume column had highest correlation out of not only least correlated, but of all the potential coefficients. A higher volume on a date had odds of 1.59 more likely being a down day, followed by "justice" with 1.27, and "president trump" with 1.15.   

Moving on to the other models, I used the same data into a Gradient Boost Classifier and a Random Forest model.  The accuracy scores for both of these were much better than the logistic model and the baseline.  The random forest model gave an accuracy score of 65% and the Gradient Boost model gave the best accuracy score of the bunch with 81%.  I ended up using this model and the logistic regression for my analyses.  The gradient boost because of its high accuracy and the logistic regression becuase of its interpretability. I performed confusion matrices on these and also got ROC AUC scores for them.  Gradient boost model yielded a score of 0.87 and the logistic model gave 0.55.  The goal for this score is to be as close to 1 as possible.  The closer to 1, the better the model is at classifying between the two classes (up day vs no up day).

After performing all of the modeling and looking at the results, it is hard to say whether the impact a tweet has an impact on a move in the SPY or stock market.  I do believe that certain tweets had a large impact, however, with combining all of the tweets from each day together, I feel that impact may have been died down.  This I believe is a large downside to this overall project and further knowledge of use in TimeSeries would be key in alleviating that issue.  One good thing picked up though was that certain topics were talked about around major market moves, both up and down.  This allowed for the logistic regression model to find the top coefficients talked about above.  Seeing Volume column having the highest absolute value coefficient wasn't surprising, especially to the downside.  This is because most down days in the market have the highest amounts of volume.  With the markets crumbling down, investors are forced to manage their positions as they change.  "Healthcare" being the top positive coefficient was interesting.  I guess that means seemingly most people are excited about that field and what can come from it.  With all this being said, this model still needs much more work but I do believe the model can work well at predicting SPY moves with better text analyses and the ability to work with TimeSeries better.  

### Web data used
    -http://www.trumptwitterarchive.com/archive
    -https://finance.yahoo.com/quote/SP/history?p=SP
    -www.stockcharts.com
    -www.stackoverflow.com
    -www.github.com
    -General Assembly lessons from global and local instructors
    -CSV's attached (trump_data.csv, SPY_csv, agg_data.csv)

### Imports used
    -import pandas as pd
    -import numpy as np
    -import matplotlib.pyplot as plt
    -import seaborn as sns
    -import string
    -import re
    -import demoji
    -import emoji
    -import regex
    -import time
    -import gensim

    -from nltk.sentiment.vader import SentimentIntensityAnalyzer
    -from sklearn.feature_extraction.text import CountVectorizer
    -from sklearn.linear_model import LogisticRegression
    -from sklearn.model_selection import GridSearchCV, train_test_split
    -from sklearn.pipeline import Pipeline
    -from sklearn.ensemble import GradientBoostingClassifier
    -from sklearn.metrics import roc_auc_score
    -from sklearn.metrics import confusion_matrix
    -import sklearn.metrics as metrics



```python

```
