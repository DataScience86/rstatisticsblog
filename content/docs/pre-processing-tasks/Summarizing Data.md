---
title: "Summarizing data"
date: 2020-01-12
weight: 3
tags: ["Summary", "Mean", "Histogram", "standard Deviation",  "Skewness", "R Code"]
category: ["R", "Statistics"]
---

> The very first task in any project related to data modeling is to explore the data, formally known as,

# Overview
**Exploratory Data Analysis(EDA)** - There are many statistics that we calculate as part of EDA. However, in this chapter we will learn how to summarize data using descriptive statistics.

Data is a collection of observations and there features (known as variables). When we try to summarize the data, the variable type plays an important role in deciding which statistic we will be considering to summarize the variable. For qualitative variables, we prepare a contingency table and try to visualize them. On the other hand, when summarizing quantitative data, we need to do much more than just create tables. We need to consider **central tendency**, **variance**, and **shape** for each of these values.

Identifying the variable type is the first and foremost task. You can use `str` function to quickly understand the data and each variable type.

```
# Quick look into data structure
str(ToothGrowth)
```

<figure>
  <img src="/images/summaryStats/str.PNG" alt="Checking the data structure in R">
</figure>

The above output mentions that `toothGrowth` is a data frame that has 60 observations and 3 variables. The function also prints the variable names, their type, and the top 10 values of each variable.

# Frequency & contingency table for qualitative variables

To summarise the categorical variable, we can generate a **frequency table** or a **contingency table**. A frequency table helps us understand and represent categorical data in a compact form. It represents the number of times each level of a categorical variable appears in the respective variable. On the other hand, a contingency table is a special case of frequency table which represents two categorical variables simultaneously.

```
# Building table for one-way table - Frequency Table
table(CO2$Treatment)
```
```
# Output
nonchilled    chilled
        42         42
```
```
# Building table for two-way table - Contingency Table
table(CO2$Treatment, CO2$Type)
```
```
# Output
            Quebec Mississippi
  nonchilled     21          21
  chilled        21          21
```

You can also get the proportion of each level by passing the output of `table` function to the `prop.table` function. Below are a few examples of how you can use this function.

```
# creating the freq table
tab <- table(CO2$Treatment)
# Proportion by row
prop.table(tab)
```
```
# Output
nonchilled    chilled
       0.5        0.5
```

To make the above output a little bit more readable, we will multiply the table by 100 and then round the value to one decimal point. An example of the same is given below, where we print the column-wise proportion.

```
# Proportion by column
round(prop.table(table(mtcars$cyl, mtcars$am), 2)*100, 1)
```
```
# Output
        0     1
  4   15.8  61.5
  6   21.1  23.1
  8   63.2  15.4
```


# Summarizing numeric variables
We use descriptive statistics for summarising the variables. As a data analyst, you must look into the three things while trying to understand your data.

**1. Measures of central tendency** - These statistics help us understand the central value of a given variable. For numeric variables, we can look into a mean or median. For categorical variables, we use mode.

```
# Calculating the mean and median values
mean(CO2$uptake, na.rm = TRUE)
median(CO2$uptake, na.rm = TRUE)
)
```
For mode, we don't have a straight forward function. However, we can use `which.max` along with the `table` function to get the level that has the maximum count.

```
# Getting the mode
which.max(table(mtcars$cyl))
```

**2. Measures of dispersion**- This measure helps us quantify the spread of the data. The stats which are used to understand the dispersion in a variable are - variance, standard deviation, and coefficient of covariance. Below are a few example functions which you can use to get this information.

```
# For variance
var(mtcars$mpg)
```
```
# Output
[1] 36.3241
```

```
# For standard deviation
sd(mtcars$mpg)
```
```
# Output
[1] 6.026948
```
The coefficient of variance is also known as the relative standard deviation. It is a standardized measure of deviation. We will be using the formula to calculate the CV in the below code.

```
# For coefficient of variance
sd(mtcars$mpg)/mean(mtcars$mpg)*100
```
```
# Output
[1] 29.99881
```

**3. Measures of shape**- Using these statistics, we try to look for the **skewness** and the **peakedness** of the distribution(also known as kurtosis). We have statistical measures that are used to define these parameters. But most of the time, we use **histogram**, **density plots**, and **boxplots** to understand **the shape of the distribution**.

```
# Generating histogram with density line
hist(AirPassengers, col = "lightblue", prob = TRUE)
lines(density(AirPassengers))
```

<figure>
  <img src="/images/summaryStats/hist.jpeg" alt="Checking the shape of the distribution using skewness and kurtosis">
</figure>

So far, we have been using one variable and one measure at a time to generate the summary statistics. However, in R, we have many functions that can generate these statistics for all the variables in a dataset. I am sharing some of the most popular and widely used **summary** functions.

**1. `summary()` function** - generates a five number summary, available in base R package.

```
# Using summary function to generate summary of toothGrowth
summary(ToothGrowth)
```
```
# Output
      len        supp         dose      
 Min.   : 4.20   OJ:30   Min.   :0.500  
 1st Qu.:13.07   VC:30   1st Qu.:0.500  
 Median :19.25           Median :1.000  
 Mean   :18.81           Mean   :1.167  
 3rd Qu.:25.27           3rd Qu.:2.000  
 Max.   :33.90           Max.   :2.000  
```

**2. `describe()` function** - The function comes from `Hmisc` R package and provides a little more detailed information about the variable of interest.

```
# Loading the psych package
library(Hmisc)
# Using describe function to generate a summary of toothGrowth
Hmisc::describe(ToothGrowth)
```
```
# Output

 3  Variables      60  Observations
-----------------------------------------------------------------------------
len
       n  missing distinct     Info     Mean      Gmd      .05      .10
      60        0       43    0.999    18.81    8.839     6.37     8.11
     .25      .50      .75      .90      .95
   13.07    19.25    25.27    27.30    29.57

lowest :  4.2  5.2  5.8  6.4  7.0, highest: 29.4 29.5 30.9 32.5 33.9
-----------------------------------------------------------------------------
supp
       n  missing distinct
      60        0        2

Value       OJ  VC
Frequency   30  30
Proportion 0.5 0.5
-----------------------------------------------------------------------------
dose
       n  missing distinct     Info     Mean      Gmd
      60        0        3    0.889    1.167    0.678

Value        0.5   1.0   2.0
Frequency     20    20    20
Proportion 0.333 0.333 0.333
-----------------------------------------------------------------------------
```

**3. `describe()` function** - The function comes from `psych` R package and provides information about all the above-mentioned measures like mean, median, variance, standard deviation, min, max, range, skewness and kurtosis among others.

```
# Loading the psych package
library(psych)
# Using describe function to generate summary of toothGrowth
describe(ToothGrowth)
```
```
# Output
      vars  n  mean   sd median trimmed  mad min  max range  skew kurtosis   se
len      1 60 18.81 7.65  19.25   18.95 9.04 4.2 33.9  29.7 -0.14    -1.04 0.99
supp*    2 60  1.50 0.50   1.50    1.50 0.74 1.0  2.0   1.0  0.00    -2.03 0.07
dose     3 60  1.17 0.63   1.00    1.15 0.74 0.5  2.0   1.5  0.37    -1.55 0.08
```
{{< hint info >}}
As the function names in two different packages is same we used double colon(::) to ensure that the function is called from the respective package.
{{< /hint >}}

# Summarizing data by a grouped variable
At times it may make much more sense to understand data from grouped variables perspectives. For example - it makes much more sense to look into **central tendency**, **variance**, and **shape** of mileage variable type of cylinder. Below is a collection of functions that can be used to get insight into grouped variables.

**1. aggregate() function**

* **Getting the aggregated value of one variable by a single grouped variable**.
```
# Average mileage by cylinder type
aggregate(mtcars$mpg, by = list(cyl = mtcars$cyl), FUN = mean)
```
```
# Output
cyl        x
1   4 26.66364
2   6 19.74286
3   8 15.10000
```
* **Getting the aggerated value for multiple variables by a single grouped variable**.
```
# Average mileage, displacement, and weight for each cylinder type
aggregate(mtcars[, c("mpg", "disp", "wt")],
            by = list(cyl = mtcars$cyl), FUN = mean)
```
```
# Output
 cyl      mpg     disp       wt
1   4 26.66364 105.1364 2.285727
2   6 19.74286 183.3143 3.117143
3   8 15.10000 353.1000 3.999214
```

* **Getting the aggreagted value by more than one grouping variable**.
```
# Average mileage, displacement, and weight for each cylinder and am type
aggregate(mtcars[, c("mpg", "disp", "wt")],
            by = list(cyl = mtcars$cyl, am = mtcars$am), FUN = mean)
```
```
# Output
cyl am      mpg     disp       wt
1   4  0 22.90000 135.8667 2.935000
2   6  0 19.12500 204.5500 3.388750
3   8  0 15.05000 357.6167 4.104083
4   4  1 28.07500  93.6125 2.042250
5   6  1 20.56667 155.0000 2.755000
6   8  1 15.40000 326.0000 3.370000
```

**2. describe.by() function**
The `describe.by` function is present in `psych` package. The function generate summary statistics by group variable.

```
# loading library
library(psych)
describe.by(mtcars[, c("mpg", "disp")], mtcars$cyl)
```
```
# Output
Descriptive statistics by group
group: 4
     vars  n   mean    sd median trimmed   mad  min   max range skew kurtosis   se
mpg     1 11  26.66  4.51     26   26.44  6.52 21.4  33.9  12.5 0.26    -1.65 1.36
disp    2 11 105.14 26.87    108  104.30 43.00 71.1 146.7  75.6 0.12    -1.64 8.10
---------------------------------------------------------------------------------
group: 6
     vars n   mean    sd median trimmed   mad   min   max range  skew kurtosis    se
mpg     1 7  19.74  1.45   19.7   19.74  1.93  17.8  21.4   3.6 -0.16    -1.91  0.55
disp    2 7 183.31 41.56  167.6  183.31 11.27 145.0 258.0 113.0  0.80    -1.23 15.71
---------------------------------------------------------------------------------
group: 8
     vars  n  mean    sd median trimmed   mad   min   max range  skew kurtosis    se
mpg     1 14  15.1  2.56   15.2   15.15  1.56  10.4  19.2   8.8 -0.36    -0.57  0.68
disp    2 14 353.1 67.77  350.5  349.63 73.39 275.8 472.0 196.2  0.45    -1.26 18.11
```

**3. tapply() function**
The `tapply` function computes a summary statistics or a function for each factor variable in a vector.
```
# Getting average mileage for each cylinder type
tapply(mtcars$mpg, mtcars$cyl, FUN = mean)
```
```
# Output
       4        6        8
  26.66364 19.74286 15.10000
```

In this chapter, we looked into some of the basic and advanced functions which can be used to provide summary statistics. We learned how to generate a frequency table for categorical variables, how to summarise numerical data and deep dive into the same by producing descriptive statistics by grouped variables.
