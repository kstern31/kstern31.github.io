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

To come up with a baseline model, I looked at the correlations between each independent variable and the dependent variable. Below are a few of the independent variables that had a high correlation with total cost. The baseline model to beat is using just the average financial need awarded to predict total cost, with an R² of 0.51.

![Correlations]({{ site.baseurl }}/images/cost-corr.png "Total Cost Predicted by Independent Variables")

The next model to test was using all the independent variables that I had imported, and it performed slightly better, with a R² of 0.620. The residuals also seemed to be randomly distributed. Although this was not a perfect model, I decided to explore the polynomial transformations and regularization before I dropped any independent variables.

I split my data into a train/test set, and plotted the mean squared error for each degree of polynomial transformation. As can be seen from the graph below, the MSE skyrockets after transforming the data, so I did not polynomially transform the data.

![Polynomial Transform]({{ site.baseurl }}/images/poly-degree.png "MSE by Polynomial Degree Transform")

I then wanted to run my model through lasso regularization to see if my model was overfit. After normalizing the data, I ran the model through 5 K-folds to see how the MSE changed with different alphas. The alpha that minimized MSE was 0, indicating that regularization is not particularly necessary for this model.

![Regularization]({{ site.baseurl }}/images/alpha-reg.png "MSE by Alpha - Lasso Regularization")

Although I didn't end up using either of these methods to alter my model, it gave me slight confidence that my model wasn't overfit. To see if I could get a more interpretable model, I decided to explore removing some features based on the p-values from the initial model.
