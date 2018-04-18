---
layout: post
title: Predicting Match Outcomes in the English Premier League
---

Every year, the English Premier League (EPL), one of the most popular football (soccer) leagues in the world, takes place from August until May. With it, comes many group chats with my friends trying to predict the outcomes of matches every week. Since we have been watching football week in, week out for at least a couple years, we like to think that we can use our knowledge to accurately predict who would win. Alas, the long term outcome remains the same, we would routinely lose money by placing money on our predictions.

**Could I use classification analysis to make predictions that were more accurate than my own?**

The EPL consists of 20 teams, and each team plays each other twice (once at each team's home stadium), which totals 380 games. Unlike other sports such as basketball or american football, each game has 3 possible outcomes; a home team win, an away team win, or a draw.

There are many different ways to bet on the outcome of a match, but for the sake of simplicity, time, and approaching this from a classification analysis standpoint, I decided to stick with betting on the aforementioned 3 outcomes. My simple betting strategy was to place $15 on each prediction that my model made. 

For those without much background on sports betting, you can't always bet on the favorites, as the odds for that outcome would give you a very measly return. Basically, you would have to risk a lot of money to only get back a small amount if that outcome occurred.

## Data Sources and Exploration ##

The general approach to this problem is to get as much team-level data as possible, and use features from past performances to try and predict the outcome of a future game. I also incorporated the betting odds prior to the game starting. 

The site with the most in-depth team-level information that I could find was [WhoScored.](https://www.whoscored.com/) They produce match reports for every game from the 1999 season until today. Starting from the 2010/2011 season, they provided additional information prior to a match, so I started gathering data from this point onward. To get the betting odds, I used [OddsPortal,](http://www.oddsportal.com/) which gives the odds for a particular game averaged across several popular bookmakers.

To scrape this data, I used similar techniques as described in my [first project.](https://kstern31.github.io/CollegeCosts-Part1/) The code for data scraping is available on my [github.](https://github.com/kstern31/Predicting-Match-Outcomes-in-EPL/tree/master/data_collection_and_analysis) As a forewarning, the scraping process for getting data from WhoScored takes a couple of days, so be prepared to run the code overnight for multiple nights.

![Historical Match Outcomes]({{ site.baseurl }}/images/project2/outcomes.png "Match Outcomes")













## Data Exploration ##
Since this was a linear regression analysis, one of the assumptions is to make sure that the dependent variable is normally
distributed. If it isn't, a non-linear transformation might be necessary.

![Dependent Variable Distribution]({{ site.baseurl }}/images/dependent-dist.png "Dependent Variable Distribution")

Although the distribution of the dependent variable is slightly right-skewed, it still roughly follows a normal distribution, so I will proceed without transforming my variable.

To come up with a baseline model, I looked at the correlations between each independent variable and the dependent variable. Below are a few of the independent variables that had a high correlation with total cost. The baseline model to beat is 


The most important features in predicting the cost of tuition were:
* Admission Rate
* Percent of International Students
* Percent of Undergrad Women
* Percent Graduating


```
Yeshiva D'Monsey Rabbinical College	
Talmudical Institute of Upstate New York	
Torah Temimah Talmudical Seminary	
Rabbinical Academy Mesivta Rabbi Chaim Berlin	
Central Yeshiva Tomchei Tmimim-Lubavitch	
```

The majority of these are religious schools with a low number of students. These schools probably don't adhere to the traditional structure of a university, and it makes sense that my model wasn't able to accurately predict the cost of tuition for these schools. 

Some interesting next steps might be to include data around the political landscape, as loan limits and funding from the government likely have a substantial effect on the cost of tuition. In addition, there seems to be somewhat of a recursive effect regarding the cost of tuition, where one college increases the cost of tuition to become more exclusive, and because other colleges want to compete with this exclusive reputation, they also increase the cost of tuition. Getting data over time might be one way to try and account for this effect.







