---
title: "Ridge Regression"
date: 2020-03-29
weight: 2
tags: ["Ridge Regression", "L2 Regularization", "Case Study", "R Code"]
category: ["R", "Supervised Algorithm", "Regression"]
---

> **Ridge Regression** is a variation of linear regression. We use ridge regression to tackle the multicollinearity problem. Due to multicollinearity, the model estimates (least square) see a large variance. Ridge regression is a method by which we add a degree of bias to the regression estimates.

# Overview - Ridge Regression
Ridge regression is a parsimonious model that performs L2 regularization. The L2 regularization adds a penalty equivalent to the square of the magnitude of regression coefficients and tries to minimize them. The equation of ridge regression looks like as given below.

      LS Obj + λ (sum of the square of coefficients)

Here the objective is as follows:

1. If λ = 0, the output is similar to simple linear regression.

2. If λ = very large, the coefficients will become zero.

*The following diagram is the visual interpretation comparing OLS and ridge regression*.

<figure>
  <img src="/images/regression/ridge.JPG" alt="Geometric representation of OLS vs Ridge Regression" >
</figure>

# Training Ridge Regression Model
To build the ridge regression in r, we use `glmnet`function from **glmnet** package in R. Let's use ridge regression to predict the mileage of the car using mtcars dataset.

```
# Loaging the library
library(glmnet)
# Getting the independent variable
x_var <- data.matrix(mtcars[, c("hp", "wt", "drat")])
# Getting the dependent variable
y_var <- mtcars[, "mpg"]

# Setting the range of lambda values
lambda_seq <- 10^seq(2, -2, by = -.1)
# Using glmnet function to build the ridge regression model
fit <- glmnet(x_var, y_var, alpha = 0, lambda  = lambda_seq)
# Checking the model
summary(fit)
```
```
# Output
          Length Class     Mode   
a0         41    -none-    numeric
beta      123    dgCMatrix S4     
df         41    -none-    numeric
dim         2    -none-    numeric
lambda     41    -none-    numeric
dev.ratio  41    -none-    numeric
nulldev     1    -none-    numeric
npasses     1    -none-    numeric
jerr        1    -none-    numeric
offset      1    -none-    logical
call        5    -none-    call   
nobs        1    -none-    numeric
```

# Choosing Optimal Lambda Value
The `glmnet` function trains the model multiple times for all the different values of lambda, which we pass as a sequence of vector to the `lambda =` argument in the `glmnet` function. The next task is to identify the optimal value of lambda that will result in a minimum error. This can be achieved automatically by using `cv.glmnet()` function.

```
# Using cross validation glmnet
ridge_cv <- cv.glmnet(x_var, y_var, alpha = 0, lambda = lambdas)
# Best lambda value
best_lambda <- ridge_cv$lambda.min
best_lambda
```
```
# Output
[1] 79.43000
```

# Extracting the best model using K-cross validation
The best model can be extracted by calling the `glmnet.fit` from the cross-validation object.  Once you have that, we can rebuild the model by passing lambda as **79.43000**.
```
best_fit <- ridge_cv$glmnet.fit
head(best_fit)
```
```
# Output
      Df   %Dev    Lambda
 [1,]  3 0.1798 100.00000
 [2,]  3 0.2167  79.43000
 [3,]  3 0.2589  63.10000
 [4,]  3 0.3060  50.12000
 [5,]  3 0.3574  39.81000
 [6,]  3 0.4120  31.62000
```
# Building the final model
```
# Rebuilding the model with optimal lambda value
best_ridge <- glmnet(x_var, y_var, alpha = 0, lambda = 79.43000)
```

## Checking the coefficients
```
coef(best_ridge)
```
```
# Output
4 x 1 sparse Matrix of class "dgCMatrix"
                      s0
(Intercept) 20.099502946
hp          -0.004398609
wt          -0.344175261
drat         0.484807607
```

The next task is to use the predict function and compute the R2 value for both the train and test dataset. In this, the example we did not create the train and test split. So, I am only providing a sample code. However, you can read the [linear regression](../linear-regression) chapter to understand this step in detail.

```
# here x is the test dataset
pred <- predict(best_ridge, s = best_lambda, newx = x)

# R squared formula
actual <- test$Price
preds <- test$PreditedPrice
rss <- sum((preds - actual) ^ 2)
tss <- sum((actual - mean(actual)) ^ 2)
rsq <- 1 - rss/tss
rsq
```

# Bias and variance of ridge regression
Bias and variance trade-off is generally complicated when it comes to building ridge regression models on an actual dataset. However, following the general trend which I would like to highlight here:

1. The bias increases as λ increases.
2. The variance decreases as λ increases.

# Assumptions of Ridge Regressions
The assumptions of ridge regression are the same as that of linear regression:  linearity, constant variance, and independence. However, as ridge regression does not provide confidence limits, the distribution of errors to be normal need not be assumed.


In this chapter, we learned about ridge regression in R using functions from glmnet package. We also saw how to use cross-validation to get the best model. In the next chapter, we will learn how to lasso regression.
