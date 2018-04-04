---
layout: post
title: What is Driving College Costs Higher? Part 2 - Data Exploration and Analysis
---

This is a continuation of my last blog post, which can be found [here](https://kstern31.github.io/CollegeCosts-Part1/).

## Data Exploration ##
Since this was a linear regression analysis, one of the assumptions is to make sure that the dependent variable is normally
distributed. If it isn't, a non-linear transformation might be necessary.

![Dependent Variable Distribution]({{ site.baseurl }}/images/dependent-dist.png "Dependent Variable Distribution")

Although the distribution of the dependent variable is slightly right-skewed, it still roughly follows a normal distribution, so I will proceed without transforming my variable.

![Correlations]({{ site.baseurl }}/images/cost-corr.png "Total Cost Predicted by Independent Variables")
