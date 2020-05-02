---
title: "Splitting Data"
date: 2020-01-03
weight: 9
tags: ["Sample", "Test and Train"]
category: ["R", "Machine Learning"]
---


> To create the best model which generalizes well to new unseen data. You must ensure that your test set serves as a proxy for actual dataset IE it represents the new dataset.

# Overview

The very first step after pre-processing of the dataset is to split the data into training and test datasets. We usually split the data around 70%-30% between training and testing stages. The training set is the one that we use to learn the relationship between independent variables and the target variable. This relationship is either stored in terms of mathematical function or is captured as a set of rules.

We then use the learnings from the training dataset and test it on the testing dataset. Our results are considered satisfactory if we get comparable results in training and testing datasets.

While we look for this split, we need to make sure that our test set meets the following two conditions:

1. Test data should be large enough to yield statistically meaningful results.
2. We need to pick a test set such that it represents the data set as a whole. This means that the characteristics of testing and training datasets should be similar.

# Six functions in R for splitting data into train and test

## 1. Using `sample()` function
```
set.seed(222)

sample_size = round(nrow(mtcars)*.70) # setting what is 70%
index <- sample(seq_len(nrow(mtcars)), size = sample_size)

train <- mtcars[index, ]
test <- mtcars[-index, ]
```


## 2. Using `sample.int()` function
```
set.seed(222)

sample <- sample.int(n = nrow(CO2),
                     size = floor(.70*nrow(CO2)), # Selecting 70% of data
                     replace = F)

train <- CO2[sample, ]
test  <- CO2[-sample, ]
```


## 3. Using `sample_n()` function from {dplyr} package
```
library(dplyr)

sample_n(mtcars, 3)
sample_n(mtcars, 10, replace = TRUE)

# Using the above function to create 70 - 30 slipt into test and train
sample_size = round(nrow(iris)*.70) # setting what is 70%

train <- sample_n(iris, sample_size)
sample_id <- as.numeric(rownames(train)) # rownames() returns character so as.numeric
test <- iris[-sample_id,]
```


## 4. Using `sample_frac()` function from {dplyr} package
```
library(dplyr)

sample_frac(mtcars, .3)
sample_frac(mtcars, .3, replace = TRUE)

# Using the above function to create 70 - 30 slipt into test and train
train <- sample_frac(iris, 0.7)
sample_id <- as.numeric(rownames(train)) # rownames() returns character so as.numeric
test <- iris[-sample_id,]
```

## 5. Using `createDataPartition()` function from {caret} package
```
library(caret)

index = createDataPartition(iris$Species, p = 0.70, list = FALSE)
train = iris[index, ]
test = iris[-index, ]
```

## 6. Using `sample.split()` function from {caTools} package
```
require(caTools)

set.seed(101)
sample = sample.split(iris$Species, SplitRatio = .75)
train = subset(iris, sample == TRUE)
test  = subset(iris, sample == FALSE)
```
