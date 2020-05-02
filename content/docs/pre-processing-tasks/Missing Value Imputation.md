---
title: "Missing Value Imputation"
date: 2019-12-22
weight: 2
tags: ["Missing value", "Missing imputation", "Chained imputation", "VIM", "R Code"]
category: ["R", "Statistics"]
---

# Overview
Having **missing values** in a data set is a very common phenomenon. There are many reasons due to which a missing value occurs in a dataset. It is vital to figure out the reason for missing values. However, in this article, we will only focus on how to **identify and impute the missing values**.

Before we start, let us first, randomly add some missing values to the iris dataset.

```
# Injecting missing value
set.seed(86)
iris[sample(1:nrow(iris), 5), "Sepal.Width"] <- NA
iris[sample(1:nrow(iris), 10), "Petal.Length"] <- NA
iris[sample(1:nrow(iris), 8), "Sepal.Length"] <- NA
```
Now that we have added few missing values in the dataset let's proceed further to see how a data analyst or a data scientist deals with this problem of missing values.


# How to identify missing values
* The very first thing is to determine if a dataset has a missing value. We can check this using functions like `is.na` or `complete.cases`.

```
# Using is.na() function
any(is.na(iris))
```
```
# Output
[1] TRUE
```
*Yes, we have missing values in the iris data*
```
# Using complete.cases() function to get percentage of missing value
nrow(iris[!complete.cases(iris), ])/nrow(iris)*100
```
```
# Output
[1] 14
```
*Overall 14% of the data is missing*
* Next is to identify which variables and what percentage of observations from each variable are missing. To achieve this we can use `md.pattern` function from `mice` package in R.
```
# Looking at the missing data pattern
library(mice)
md.pattern(iris)
```
```
# Output
            Petal.Width Species Sepal.Width Sepal.Length Petal.Length   
129           1       1           1            1            1       0
8             1       1           1            1            0       1
7             1       1           1            0            1       1
1             1       1           1            0            0       2
4             1       1           0            1            1       1
1             1       1           0            1            0       2
              0       0           5            8           10       23
```

The above table states that there are **5** missing values in **Sepal.Width**, **8** missing values in **Sepal.Length**, and **10** missing values in **Petal.Length**. In the last column, it is mentioned that overall, 23 values are missing.

# How to delete missing observations
When the total number of missing observations is significant, then we can think of removing those observations from the data. Imputing too many missing observations can lead to bias in the dataset. Also, it can result in poor statistical models. We can delete the missing values at the data preparation stage or at the time of building the model. However, not all algorithms provide this option of deleting missing values while we train the model.

```
# Deleting missing observations
iris <- iris[complete.cases(iris), ]
# or we can use na.omit() function
iris <- na.omit(iris)

# Ignoring missing values while building lm model
lm(mpg ~ cyl + disp, data=mtcars, na.action=na.omit)
```
*For showcasing linear regression example we used mtcars dataset*

# How to delete variables with missing values
Sometimes one or two variables contribute to the most number of missing values. In such cases, deleting these variables with a high percentage of missing values will help save lots of observations. According to one thumb rule we delete all variable which has more than 30% of missing values.
```
## Removing columns with more than 30% NA
iris[, -which(colMeans(is.na(iris)) > 0.3)]
```

# Imputing missing values
Replacing missing values with a rough approximate value is acceptable and could result in a satisfactory result. Let us look at some of the ways in which we can replace the missing values.

## Using mean/median/mode
To replace missing values with mean, median, or mode, we can use `impute` function from `Hmisc` package. This can also be achieved by using **square brackets[]** or `ifelse` statement.
```
# Using impute function from Hmisc package
library(Hmisc)
impute(iris$Sepal.Length, mean)  # replace with mean
impute(iris$Sepal.Length, median)  # median
```
Other ways are using which we can replace missing values.

```
# Filling missing values with Mean
iris$Sepal.Length[is.na(iris$Sepal.Length)] = mean(iris$Sepal.Length, na.rm=TRUE)
# alternative way is to use ifelse
iris = transform(iris, y = ifelse(is.na(iris), mean(iris, na.rm=TRUE), Sepal.Length))
```

## Multivariate Imputation By Chained Equations(mice R Package)
The `mice` function from the package automatically detects the variables which have missing values. Once identified, the missing values are then replaced by Predictive Mean Matching (PMM). The other methods which mice support are listed below:

* logreg(Logistic Regression) – Used For Binary Variables
* Proportional odds model – Used For ordered levels >= 2
* polyreg(Bayesian polytomous regression) – Used For unordered levels>= 2

Let us look at an example:
```
library(mice)
# Imputing the values using mice
imputed_iris <- mice(iris, m=5, method = 'pmm', seed = 101)
# checking the summary
summary(imputed_iris)
```
```
# Output
Class: mids
Number of multiple imputations:  5
Imputation methods:
Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species
       "pmm"        "pmm"        "pmm"           ""           ""
PredictorMatrix:
             Sepal.Length Sepal.Width Petal.Length Petal.Width Species
Sepal.Length            0           1            1           1       1
Sepal.Width             1           0            1           1       1
Petal.Length            1           1            0           1       1
Petal.Width             1           1            1           0       1
Species                 1           1            1           1       0
```

*You can also look at the imputed values by using the following code*

```
# Checking imputed values of sleep variable
imputed_iris$imp
```
```
# Output
$`Sepal.Length`
      1   2   3   4   5
29  4.6 4.7 4.6 5.1 4.9
32  4.6 5.0 5.0 4.9 4.9
65  5.7 5.6 4.8 5.7 5.7
73  6.9 6.4 6.4 6.8 6.8
81  4.8 5.1 5.5 5.1 5.5
91  5.8 5.8 6.3 6.2 6.0
108 7.2 7.4 7.2 7.2 7.2
123 7.6 7.4 7.9 7.7 7.7

$Sepal.Width
      1   2   3   4   5
33  3.2 3.8 3.8 3.1 3.4
68  3.0 2.5 2.2 2.2 2.4
74  3.0 2.2 3.0 3.0 2.6
115 3.0 2.8 2.5 3.1 2.8
134 3.0 2.6 2.5 2.5 2.5

$Petal.Length
      1   2   3   4   5
32  1.4 1.6 1.5 1.5 1.5
33  1.5 1.5 1.5 1.4 1.2
41  1.5 1.5 1.5 1.4 1.4
48  1.6 1.6 1.5 1.4 1.4
50  1.4 1.5 1.6 1.4 1.6
60  3.5 4.5 3.9 3.5 4.2
82  3.0 3.5 4.1 4.1 3.5
106 6.4 6.4 6.7 6.4 6.1
109 5.8 5.8 5.6 6.0 5.1
133 5.3 5.6 6.0 6.0 5.1

$Petal.Width
[1] 1 2 3 4 5
<0 rows> (or 0-length row.names)

$Species
[1] 1 2 3 4 5
<0 rows> (or 0-length row.names)
```

{{< hint info >}}
Each missing value is filled with 5 different numbers and is represented by columns
{{< /hint >}}

## Using Machine Learning Algorithms
There are actually a couple of nice and very popular machine learning algorithms that we can use to fill the missing values. Algorithms like KNN and random forest are some such popular algorithms.

### Using KNN to fill the missing values
```
library(bnstruct)
knn.impute(iris, k = 5, cat.var = 1:ncol(iris), to.impute = 1:nrow(iris),
  using = 1:nrow(iris))
```

### Using random forest to fill the missing values
```
# Imputing values using Random forest
set.seed(86)
iris <- rfImpute(Species ~ ., iris.na)
```

# Analysing imputed values using graphical methods
When it comes to visualizing the missing and imputed values, my personal favorite is **VIM** package in R. The package is very useful in identifying the underlying mechanism responsible for missing values. It has many built-in functions to visualize the missing and imputed values. Also, this package has some nice functions for the imputation of missing values. Below I am providing a few examples on how to generate some beautiful visualizations. However, I would encourage you to read the [documentation](https://cran.r-project.org/web/packages/VIM/VIM.pdf) or for quick in-depth reference read this article [visualization of imputed values using vim](https://datasciencebeginners.com/2018/11/07/visualization-of-imputed-values-using-vim/)

## Generating boxplot comparing distribution of all observations with imputed values
```
library(VIM)
# imputting values in iris dataset using knn
iris_imputed <- kNN(iris[, c("Petal.Length", "Sepal.Width", "Sepal.Length")])
# Using pbox function to draw boxplot
pbox(iris_imputed, delimiter="imp", selection="any")
```

## Generating a graph show cashing scatter and box plot in one graph
```
library(VIM)
# imputting values in iris dataset using knn
iris_imputed <- kNN(iris[, c("Petal.Length", "Sepal.Width")])
# Using tao dataset to draw the margin plot
marginplot(iris_imputed, delimiter="imp",
            alpha = 0.8)
```

<figure>
  <img src= "/images/missingValues/vim2.jpeg" alt = "Changing from default theme to other in ggplot2">
</figure>

The above graph is fascinating and provides a detailed insight into the actual and imputed values. There are many other types of visualization that VIM package supports. It is undoubtedly helpful to spend some time on learning and exploring this powerful R Package.

{{< hint info >}}
**Which is the best imputation method?**
It is a very frequently asked question, and although there are many ways in which we can impute missing values, one cannot say with certainty that a particular method provides the best result. Therefore, it is advised to test out some of these methods and see which one is providing the best result. As we know, statistics is all about trial and error.
{{< /hint >}}
