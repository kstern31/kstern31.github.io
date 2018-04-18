---
layout: post
title: Predicting Match Outcomes in the English Premier League
---

Every year, the English Premier League (EPL), one of the most popular football (soccer) leagues in the world, takes place from August until May. With it, comes many group chats with my friends trying to predict the outcomes of matches every week. Since we have been watching football week in, week out for at least a couple years, we like to think that we can use our knowledge to accurately predict who would win. Alas, the long term outcome remains the same, we would routinely lose money by placing money on our predictions.

**Could I use classification analysis to make predictions that were more accurate than my own?**














This is a continuation of my last blog post, which can be found [here](https://kstern31.github.io/CollegeCosts-Part1/).

## Data Exploration ##
Since this was a linear regression analysis, one of the assumptions is to make sure that the dependent variable is normally
distributed. If it isn't, a non-linear transformation might be necessary.

![Dependent Variable Distribution]({{ site.baseurl }}/images/dependent-dist.png "Dependent Variable Distribution")

Although the distribution of the dependent variable is slightly right-skewed, it still roughly follows a normal distribution, so I will proceed without transforming my variable.

To come up with a baseline model, I looked at the correlations between each independent variable and the dependent variable. Below are a few of the independent variables that had a high correlation with total cost. The baseline model to beat is using just the average financial need awarded to predict total cost, with an R² of 0.51.

![Correlations]({{ site.baseurl }}/images/cost-corr.png "Total Cost Predicted by Independent Variables")

The next model to test was using all the independent variables that I had imported, and it performed slightly better, with a R² of 0.62. The residuals also seemed to be randomly distributed. Although this was not a perfect model, I decided to explore the polynomial transformations and regularization before I dropped any independent variables.

I split my data into a train/test set, and plotted the mean squared error for each degree of polynomial transformation. As can be seen from the graph below, the MSE skyrockets after transforming the data, so I did not polynomially transform the data.

![Polynomial Transform]({{ site.baseurl }}/images/poly-degree.png "MSE by Polynomial Degree Transform")

I then wanted to run my model through lasso regularization to see if my model was overfit. After normalizing the data, I ran the model through 5 K-folds to see how the MSE changed with different alphas. The alpha that minimized MSE was 0, indicating that regularization is not particularly necessary for this model.

![Regularization]({{ site.baseurl }}/images/alpha-reg.png "MSE by Alpha - Lasso Regularization")

Although I didn't end up using either of these methods to alter my model, it gave me slight confidence that my model wasn't overfit. To see if I could get a more interpretable model, I decided to explore removing some features based on the p-values from the initial model.

I removed over half of the features, and ended up with an R² of 0.619, and a cross-validated R² of 0.528 . This seemed like a good compromise to increase interpretability. I explored the same polynomial and regularization methods on this model, which gave similar results.

The most important features in predicting the cost of tuition were:
* Admission Rate
* Percent of International Students
* Percent of Undergrad Women
* Percent Graduating

These made sense to me as the most important features. 

The more exclusive colleges (those that had a low admission rate) would be more likely to have higher tuition costs, as there is a huge component of reputational competition across colleges.

The effect of international students on the cost of tuition is tied to the practice of tuition discounting. This is when a college awards financial aid from its own funds. More costly colleges usually award more financial need to low-income students so they can afford to attend. For example, students whose parents make less than a certain amount ($100,000) usually pay no tuition. However, this means that the paying students are making up the difference by paying the sticker price, and the students that are most likely to be able to afford paying this price are international students.

I suspect that the percent of undergraduate women as an important feature was driven by all womens schools like Scripps College, Barnard, and Juilliard. Anecdotally, there seems to be more reputable all womens colleges compared to all mens colleges, so this makes sense to me.

The percent of students graduating seems fairly straightforward as being an important feature, as the more successful a college is, the higher the cost of tuition should be.

I wanted to investigate deeper into what schools my model wasn't able to predict well, so I turned to the residual plot to find some patterns. I color coded the residual plot by admission rate to see if this would help visualize any insights.

![Residual Plot]({{ site.baseurl }}/images/res-plot.png "Residual Colored by Admission Rate")

I found that the schools that weren't predicted well were generally schools that had an extremely high admission rate, schools that admitted all students that applied. Below are some of the schools that weren't predicted well.

```
Yeshiva D'Monsey Rabbinical College	
Talmudical Institute of Upstate New York	
Torah Temimah Talmudical Seminary	
Rabbinical Academy Mesivta Rabbi Chaim Berlin	
Central Yeshiva Tomchei Tmimim-Lubavitch	
```

The majority of these are religious schools with a low number of students. These schools probably don't adhere to the traditional structure of a university, and it makes sense that my model wasn't able to accurately predict the cost of tuition for these schools. 

Some interesting next steps might be to include data around the political landscape, as loan limits and funding from the government likely have a substantial effect on the cost of tuition. In addition, there seems to be somewhat of a recursive effect regarding the cost of tuition, where one college increases the cost of tuition to become more exclusive, and because other colleges want to compete with this exclusive reputation, they also increase the cost of tuition. Getting data over time might be one way to try and account for this effect.







