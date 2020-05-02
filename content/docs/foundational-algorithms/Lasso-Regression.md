---
title: "Lasso Regression"
date: 2020-02-16
weight: 3
tags: ["Lasso", "L1 regularization", "R Code"]
category: ["R", "Supervised Algorithm", "Regression"]
---

> **LASSO** stands for **Least Absolute Shrinkage and Selection Operator**. The algorithm is another variation of linear regression, just like ridge regression. We use lasso regression when we have a large number of predictor variables.

# Overview - Lasso Regression
Lasso regression is a parsimonious model that performs L1 regularization. The L1 regularization adds a penalty equivalent to the absolute magnitude of regression coefficients and tries to minimize them. The equation of lasso is similar to ridge regression and looks like as given below.

      LS Obj + λ (sum of the absolute values of coefficients)

Here the objective is as follows:
If λ = 0, We get the same coefficients as linear regression
If λ = vary large, All coefficients are shrunk towards zero

The two models, lasso and ridge regression, are almost similar to each other. However, in lasso, the coefficients which are responsible for large variance are converted to *zero*. On the other hand, coefficients are only shrunk but are never made zero in ridge regression.

**Lasso regression analysis is also used for variable selection as the model imposes coefficients of some variables to shrink towards zero.**

## What does a large number of variables mean?
1. The large number here means that the model tends to over-fit. Theoretically, a minimum of ten variables can cause an overfitting problem.
2. When you face computational challenges due to the presence of n number of variables. Although, given today's processing power of systems, this situation arises rarely.

*The following diagram is the visual interpretation comparing OLS and lasso regression*.

<figure>
  <img src="/images/regression/lasso.JPG" alt="Geometric representation of OLS vs lasso Regression" >
</figure>

<br/>
{{< hint info >}}
The LASSO is not very good at handling variables that show a correlation between them and thus can sometimes show very wild behavior.
{{< /hint >}}


# Training Lasso Regression Model
The training of the lasso regression model is exactly the same as that of ridge regression. We need to identify the optimal lambda value and then use that value to train the model. To achieve this, we can use the same `glmnet` function and pass`alpha = 1` argument. When we pass `alpha  = 0`, `glmnet()` runs a ridge regression, and when we pass `alpha = 0.5`, the glmnet runs another kind of model which is called as **elastic net** and is a combination of ridge and lasso regression.

1. We use `cv.glmnet()` function to identify the optimal lambda value
2. Extract the best lambda and best model
3. Rebuild the model using `glmnet()` function
4. Use predict function to predict the values on future data

For this example, we will be using `swiss` dataset to predict fertility based upon Socioeconomic Indicators for the year 1888.

```
# Loading the library
library(glmnet)

# Loading the data
data(swiss)

x_vars <- model.matrix(Fertility~. , swiss)[,-1]
y_var <- swiss$Fertility
lambda_seq <- 10^seq(2, -2, by = -.1)

# Splitting the data into test and train
set.seed(86)
train = sample(1:nrow(x_var), nrow(x_var)/2)
x_test = (-train)
y_test = y_var[test]

cv_output <- cv.glmnet(x_vars[train,], y_var[train],
            alpha = 1, lambda = lambda_seq)

# identifying best lamda
best_lam <- cv_output$lambda.min
```
```
# Output
[1] 1.995262
```
*Using this value, let us train the lasso model again*.

```
# Rebuilding the model with best lamda value identified
lasso_best <- glmnet(x_vars[train,], y_var[train], alpha = 1, lambda = best_lam)
pred <- predict(lasso_best, s = best_lam, newx = x_vars[test,])
```

Finally, we combine the predicted values and actual values to see the two values side by side, and then you can use the R-Squared formula to check the model performance. Note - you must calculate the R-Squared values for both the train and test dataset.

```
final <- cbind(y_var[test], pred)
# Checking the first six obs
head(final)
```
```
# Output
            Actual    Pred
Courtelary   80.2   69.92666
Delemont     83.1   76.15793
Franches-Mnt 92.5   75.16697
Moutier      85.8   70.33981
Glane        92.4   76.61480
Veveyse      87.1   76.34404
```

## Sharing the R Squared formula
*The function provided below is just indicative, and you must provide the actual and predicted values based upon your dataset*.

```
actual <- test$actual
preds <- test$predicted
rss <- sum((preds - actual) ^ 2)
tss <- sum((actual - mean(actual)) ^ 2)
rsq <- 1 - rss/tss
rsq
```

## Getting the list of important variables
To get the list of important variables, we just need to investigate the beta coefficients of the final best model.

```
# Inspecting beta coefficients
coef(lasso_best)
```
```
# Output
6 x 1 sparse Matrix of class "dgCMatrix"
                          s0
(Intercept)      55.16706057
Agriculture       .         
Examination      -0.30124968
Education         .         
Catholic          0.04700893
Infant.Mortality  0.84730322
```

*The model indicates that the coefficients of Agriculture and Education have been shrunk to zero. Thus we are left with three variables, namely; Examination,  Catholic, and Infant.Mortality*

In this chapter, we learned how to build a lasso regression using the same glmnet package, which we used to build the ridge regression. We also saw what's the difference between the ridge and the lasso is. In the next chapter, we will discuss how to predict a dichotomous variable using logistic regression.
