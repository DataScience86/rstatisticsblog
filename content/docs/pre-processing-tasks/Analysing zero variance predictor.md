---
title: "Analysing zero variance predictor"
date: 2019-12-01
weight: 6
tags: ["Zero Variance", "R Code"]
category: ["R", "Statistics", "Machine Learning"]
---

> Some variables in the dataset contain very little information because they mostly consist of a single value (e.g. zero). These variables are called a zero variance variable.

# Overview
Many a time it happens that in the dataset, we have variables which either have unique values or have only a handful of unique values. The variables with only one exceptional value, when passed to fit the model, can cause problems like unstable models or, in some cases, can also cause the model to crash. Such predictor variables are called as Zero-variance variables.

The variables which have a few unique values with low frequency can also lead to problems. For example, in the survey dataset, some of the levels of exercise variable have a low-frequency rate. These variables may become zero-variance predictors during the train/test split or at the time of the cross-validation process. They are also sometimes termed as near-zero variance predictors.

# Methods to identify such variables
All such variables which fall into these categories need to be identified and excluded from the datasets. There are many different things one can do to identify these variables like you can check unique values, generate frequency tables, etc. Also, one can use the following metrics to determine the zero variance or near-zero variance predictors –

1. **Frequency Ratio** – It is the ratio of the most common value over the second most prevalent value. If the value is close to one, then the predictor is good to use. However, a substantial value indicates that the variable is unbalanced and should be eliminated.

2. **Percentage of Unique Values** – It is calculated by dividing the number of unique values divided by the total number of samples that approaches zero as the granularity of the data increases.
Any variable which crosses the predefined threshold for frequency ratio and has a frequency of unique values percentage less than the limit should be considered as zero variance predictor.

{{< hint warning>}}Not all low granularity variables are near-zero variance predictors. These variables can often have evenly distributed distribution and can lead to false identification of zero variance predictors. To avoid any mistakes, one must use both the metrics mentioned above.{{< /hint >}}


`caret` package in R provides a function called `nearZeroVar()` to identify and to remove such problematic variables before modeling. Some of the arguments which you must be aware or are -

1. **x** = Dataset
2. **freqCut** = the cutoff for the ratio of the most common value to the second most common value. The default value is set at 95/5
3. **uniqueCut** = the cutoff for the percentage of distinct values out of the number of total samples. The default value is 10.
4. **saveMetrics** = takes logical input. If false, only the position of zero- and near-zero variables is returned. If true, a dataframe with predictor information is returned.<br>

The default values mentioned for `freqCut` and `uniqueCut` are fairly conservative. So, based upon your requirements, feel free to change these numbers.

```
# Identifying near-zero variance variable
nearZeroVar(iris[, -5], saveMetrics = TRUE)
```
```
#Output
             freqRatio percentUnique zeroVar   nzv
Sepal.Length  1.111111      23.33333   FALSE FALSE
Sepal.Width   1.857143      15.33333   FALSE FALSE
Petal.Length  1.000000      28.66667   FALSE FALSE
Petal.Width   2.230769      14.66667   FALSE FALSE
```
# Identify zero variance conditioned on group variable
`checkConditionalX()` function can be used to look at the distribution of the columns of x conditioned on the levels of y. This function identifies columns of data that are sparse within groups of y variable.

```
set.seed(1)
classes <- factor(rep(letters[1:3], each = 30))
x <- data.frame(x1 = rep(c(0, 1), 45),
                x2 = c(rep(0, 10), rep(1, 80)))

lapply(x, table, y = classes)
checkConditionalX(x, classes)
```

```
# Output
lapply(x, table, y = classes)
$x1
   y
     a  b  c
  0 15 15 15
  1 15 15 15

$x2
   y
     a  b  c
  0 10  0  0
  1 20 30 30

checkConditionalX(x, classes)
[1] 2
```

In this chapter, you learned about what is a zero variance and near-zero variance variables. You also learned how to identify such variables conditioned on a grouped variable or at an individual level.
