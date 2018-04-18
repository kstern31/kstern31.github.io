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

### Feature Engineering ###

The level of information that WhoScored provides is extremely granular, with stats such as how many passes were made into the attacking third of the pitch, and how many passes were made on the ground as opposed to in the air. Below is an example of some of the other stats that are available:

![WhoScored Stats]({{ site.baseurl }}/images/project2/whoscored.png "WhoScored Stats")

Luckily, after the lengthy scraping process, the data itself was fairly clean, so I could immediately get into data exploration and feature engineering.

The first step was to create labels for the outcomes, based on the number of goals scored by the home and away team. The three labels would be "Home Win", "Away Win", and "Draw".

![Historical Match Outcomes]({{ site.baseurl }}/images/project2/outcomes.png "Match Outcomes")

After taking a look at the dependent variable distribution, you can see that the most common outcome is that the home team wins. As a result, my baseline model would be predicting that the home team wins every game.

This is in line with the concept of "home team advantage", which describes the benefit the home team is said to gain over the visiting team. This phenomenom has been attributed to psychological effects, playing in more familiar situations, or the disadvantage of having to travel. A more in-depth discussion of "home team advantage" is probably best left for another time. I just needed to keep in mind that in general, teams perform differently when playing at home versus when playing away. Because of this, I wanted to only take into account past _home_ performances for the home team and past _away_ performances for the away team when predicting match outcomes. 

To measure past performance, I decided to use moving averages. The three options that I decided to explore were simple moving averages, exponential moving averages, and expanding window moving averages. In simple terms:

* Simple Moving Average: calculated by taking the arithmetic mean over a specific number of values (e.g. averaged over the past 5 games)
* Exponential Moving Average: window includes all past data, but gives more weight to recent values to make it more responsive to new information
* Expanding Window Moving Average: similar calculation to a simple moving average, but the window includes all past data

Pandas has fairly easy-to-use functions to calculate these moving averages, and I just created my own function so I could do calculations for each different type of moving average.

```python
if avgtype == 'rolling':
  homepaststats = season.groupby('homeTeam', as_index = False).rolling(window_decay, min_periods = 1).mean().reset_index(level = 0, drop = True)
  awaypaststats = season.groupby('awayTeam', as_index = False).rolling(window_decay, min_periods = 1).mean().reset_index(level = 0, drop = True)
elif avgtype == 'ewm':
  homepaststats = season.groupby('homeTeam', as_index = False).apply(lambda x: x.ewm(halflife = window_decay).mean()).reset_index(level = 0, drop = True)
  awaypaststats = season.groupby('awayTeam', as_index = False).apply(lambda x: x.ewm(halflife = window_decay).mean()).reset_index(level = 0, drop = True)
elif avgtype == 'expanding':
  homepaststats = season.groupby('homeTeam', as_index = False).expanding(min_periods = 1).mean().reset_index(level = 0, drop = True)
  awaypaststats = season.groupby('awayTeam', as_index = False).expanding(min_periods = 1).mean().reset_index(level = 0, drop = True)
```

The window_decay variable was one of the arguments of my function, and was used to calculate the window for the simple moving average, and the halflife for the exponential moving average (half-life is the period of time for the exponential weight to reduce to one half).

I was also able to engineer some features such as creating an indicator of whether a "top player" was missing that week through injury or suspension.




