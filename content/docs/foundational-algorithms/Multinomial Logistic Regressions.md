---
title: "Multinomial Logistic Regression"
date: 2020-02-22
weight: 5
tags: ["Multinomial Regression", "Case Study", "R Code"]
category: ["R", "Supervised Algorithm", "Classification"]
---

> **Multinomial logistic** regression is used when the target variable is categorical with more than two levels. It is an extension of binomial logistic regression.

# Overview - Multinomial Regression
Multinomial regression is used to predict the nominal target variable. In case the target variable is of ordinal type, then we need to use ordinal logistic regression. In this tutorial, we will see how we can run multinomial logistic regression. As part of data preparation, ensure that data is free of multicollinearity, outliers, and high influential leverage points.

# Case Study - What is UCI Breast Tissue?
In this tutorial, we will be using [Breast Tissu](https://archive.ics.uci.edu/ml/machine-learning-databases/00192/)e data from UCI machine learning repository the classification of breast tissue. Originally, the breast tissues have been classified into  6 groups.

However, we will merge the fibro-adenoma, mastopathy, and glandular classes as their discrimination are not important. Check the tutorial on [Dataframe Manipulations](../dataframe-manipulations) to learn about the merging of levels and other tasks related to dataframe in R programming.

## Reading the breast tissue data
```
library(readr)
tissue <- read_csv("./static/data/BreastTissue.csv")
# Checking the structure of adult data
str(tissue)
```
```
# Output
Classes ‘tbl_df’, ‘tbl’ and 'data.frame':    106 obs. of  11 variables:
 $ Case #: int  1 2 3 4 5 6 7 8 9 10 ...
 $ Class : chr  "car" "car" "car" "car" ...
 $ I0    : num  525 330 552 380 363 ...
 $ PA500 : num  0.187 0.227 0.232 0.241 0.201 ...
 $ HFS   : num  0.0321 0.2653 0.0635 0.2862 0.2443 ...
 $ DA    : num  229 121 265 138 125 ...
 $ Area  : num  6844 3163 11888 5402 3290 ...
 $ A/DA  : num  29.9 26.1 44.9 39.2 26.3 ...
 $ Max IP: num  60.2 69.7 77.8 88.8 69.4 ...
 $ DR    : num  220.7 99.1 253.8 105.2 103.9 ...
 $ P     : num  557 400 657 494 425 ...
```

Combining levels of target variable and deleting the **case #** as it is a unique variable.

```
tissue <- tissue[, -1]
tissue$Class <- as.factor(tissue$Class)
levels(tissue$Class)[levels(tissue$Class) %in% c("fad", "gla", "mas")] <- "other"
levels(tissue$Class)
```
```
# Output
[1] "adi"   "car"   "con"   "other"
```

## Building Multinomial Regression Model
We will be predicting **Class** of the breast tissue using Breast Tissue data from the UCI machine learning repository.

### Splitting the data in train and test
```
#Splitting the data using a function from dplyr package
library(caret)

index <- createDataPartition(tissue$Class, p = .70, list = FALSE)
train <- tissue[index,]
test <- tissue[-index,]
```
### Setting the reference level
Unlike binary logistic regression in multinomial logistic regression, we need to define the reference level. Please note this is specific to the function which I am using from **nnet** package in R. There are some functions from other R packages where you don't really need to mention the reference level before building the model.

```
# Setting the reference
train$Class <- relevel(train$Class, ref = "adi")
```

### Training the multinomial classification model
To train the model, we will be using `multinom` function from `nnet` package. Once the model is trained, then we will use the summary() function to check the model coefficients.

```
require(nnet)
# Training the multinomial model
multinom_model <- multinom(Class ~ ., data = tissue)

# Checking the model
summary(multinom_model)
```
```
# Output
Call:
multinom(formula = Class ~ ., data = tissue)

Coefficients:
      (Intercept)         I0     PA500        HFS         DA
car      86.73299 -1.2415518 34.805551 -31.338876 -3.3819409
con      65.23130 -0.1313008  3.504613   5.178805  0.6902009
other    94.25666 -0.7356228 -9.929850  47.648766 -0.7567586
             Area     `A/DA`  `Max IP`         DR           P
car   -0.01439290 -0.6831729 3.1996740  3.9293080  0.92505697
con   -0.01189647  2.3845927 0.4270486 -0.1631782 -0.03680047
other -0.01590076  1.6362184 0.8789358  1.3702359  0.51944534

Std. Errors:
      (Intercept)         I0       PA500          HFS
car   0.038670563 0.11903866 0.019490301 0.0378583878
con   0.001741294 0.09413872 0.000350851 0.0005646109
other 0.038878992 0.11367934 0.019507244 0.0378385159
              DA       Area     `A/DA`  `Max IP`        DR
car   0.65220286 0.02077879 0.55684441 0.4874646 0.6119659
con   0.09834323 0.01535892 0.06383315 0.4114490 0.1080821
other 0.65428634 0.02068448 0.55666884 0.4881945 0.6102327
              P
car   0.1770202
con   0.1120580
other 0.1728985

Residual Deviance: 8.24364
AIC: 68.24364
```

Just like binary logistic regression, we need to convert the coefficients to odds by taking the exponential of the coefficients.

```
exp(coef(multinom_model))
```
The predicted values are saved as fitted.values in the model object. Let's see the top 6 observations.

```
head(round(fitted(multinom_model), 2))
```
```
# Output
  adi  car con other
1   0 0.97   0  0.03
2   0 1.00   0  0.00
3   0 1.00   0  0.00
4   0 1.00   0  0.00
5   0 1.00   0  0.00
6   0 0.97   0  0.03
```
The multinomial regression predicts the probability of a particular observation to be part of the said level. This is what we are seeing in the above table. Columns represent the classification levels and rows represent the observations. This means that the first six observation are classified as car.

### Predicting & Validating the model
To validate the model, we will be looking at the accuracy of the model. This accuracy can be calculated from the classification table.
```
# Predicting the values for train dataset
train$ClassPredicted <- predict(multinom_model, newdata = train, "class")

# Building classification table
tab <- table(train$Class, train$ClassPredicted)

# Calculating accuracy - sum of diagonal elements divided by total obs
round((sum(diag(tab))/sum(tab))*100,2)
```
```
# Output
[1] 98.68
```
*Our model accuracy has turned out to be 98.68% in the training dataset*.

### Predicting the class on test dataset.

```
# Predicting the class for test dataset
test$ClassPredicted <- predict(multinom_model, newdata = test, "class")

# Building classification table
tab <- table(test$Class, test$ClassPredicted)
tab
```
```
# Output

        adi car con other
  adi     6   0   0     0
  car     0   6   0     0
  con     0   0   4     0
  other   0   0   0    14
```

*We were able to achieve 100% accuracy in the test dataset and this number is very close to train, and thus we conclude that the model is good and is also stable.*

In this tutorial, we learned how to build the multinomial logistic regression model, how to validate and make a prediction on the unseen dataset.
