---
title: "Data Frame Manipulations"
weight: 5
---

# Data Manipulation Tasks With Example Code

As a data analyst, you will be working mostly with data frames. And thus, it becomes vital that you learn, understand, and practice data manipulation tasks. This chapter will focus on listing down some of the most common data manipulation tasks along with example code.

{{< hint info >}}Before, we start and dig into how to accomplish tasks mentioned above. Let's see how to access the datasets which come along with the R packages.{{< /hint>}}

You can get the list of all the datasets by using `data()` function and to get the list of datasets belonging to a particular package use argument `data(package = "MASS")` function.

# Structure of a data frame
With the structure of a data frame, we mean how to check the dimension of a data frame. The total number of observations present in a data frame and things like that. This information can be extracted by using `dim()`and `str()` functions. The `str()` function gives much more detailed information as compared to `dim()` function.

```
# Getting number of rows and columns - dimension
dim(iris)

# Getting more elaborate information
str(iris)
```

## Getting column and row names
To print the column and row names, you can use the `colnames()` and `rownames()` function. There exist another function called `names()` that can get the names of the columns. Both functions return a character vector.

```
# Printing columns names
colnames(iris)
# Printing columns names
names(iris)
# Getting more elaborate information
rownames(mtcars)
```

# Converting row names to columns
The function `add_rownames()`  from `dplyr` can add row names as a column into the data frame. This function will add the new column at the beginning of the data frame.
```
library(dplyr)
# Adding row names as a column in mtcars
mtcars <- add_rownames(mtcars, "carNames")
```

The above task can also be accomplished by using functions like `rownames()` and `cbind()` from base R package. So let us see how one can achieve the desired output using these functions.

```
# Resetting the data to the original form
data(mtcars)
# Extracting row names
names <- rownames(mtcars)
#Setting the row names to NULL - deleting basically
rownames(mtcars) <- NULL
# Combining the row names back to the mtcars dataset
mtcars <- cbind(mtcars,names)
```

# Accessing columns and rows in a data frame
A row or a column of a data frame can be accessed using either the index or name. For example,

**Extract mileage data from mtcars dataset**
```
# Using index
mtcars[, 1] # mileage is the first columns

# Using column name
mtcars[, "mpg"]

# Using dollar notation(to call by variable name)
mtcars$mpg
```

**Extract two columns - mileage and cyl from mtcars dataset**
```
# Using index
mtcars[, c(1,2)] # mileage is the first columns

# Using column name
mtcars[, c("mpg", "cyl")]
```
**Extract data at row level - Hornet 4 Drive from mtcars dataset**

```
# Using index
mtcars[4, ] # Hornet 4 Drive is the fourth observation
# Using index for calling multiple rows
mtcars[c(3,4), ] # Hornet 4 Drive is the fourth observation

# Using row name
mtcars["Hornet 4 Drive",]

# Using row nams for calling multiple observations
mtcars[c("Datsun 710","Hornet 4 Drive"),]
```

# Renaming and rearranging columns of a data frame
One can rename the columns using `colnames()` function. The same task can be achieved through `rename()` function from `dplyr` package in R.

```
# Renaming all the columns
colnames(cars) <- c("carSpeed", "distanceCovered")
# Renaming only 2nd column
colnames(cars)[2] <- "distCovered"

# Using rename() function from dplyr
require(dplyr)
iris = rename(iris, flowerSpecies = Species, SepalLength = Sepal.Length)
```

{{< hint info >}}Columns of a data frame can be rearranged by using column names or by using column index, as shown in the code snippet.{{< /hint >}}

```
data(cars)
# Checking current column names
names(cars)
# Reordering by using column names
cars <- cars[c("dist", "speed")]

# Reordering by using column index
cars <- cars[c(2, 1)]
```

# How to identify missing values
You can look for missing values by column or by row. To check by column we can use `describe()` function from `Hmisc` R package or `summary()` function from stats R package. The `describe()` function returns a column called as missing, whereas `summary()` function indicates the presence of the missing values by **NA** count.

```
library(Hmisc)
# using describe() function
describe(mtcars)
# Using summary() function
summary(mtcars)
```
Apart from these two functions you also use `is.na()` function along with `apply()` and `any()` function to identify if a variable contains missing value or not. If a variable contains a missing value it will return **TRUE** else it will return **FALSE**.

```
# Column wise missing value check
apply(mtcars, 2, function(x){any(is.na(x))})
# Row wise missing value check
apply(mtcars, 1, function(x){any(is.na(x))})
```
# How to filtering missing values
To filter missing values we can make use of `is.na()` function or we can also use a function named `complete.cases()`. Lets us see how to use these functions.

```
# Using is.na to get complete observations
CO2[!is.na(CO2), ]
# Using complete.cases to get complete rows
CO2[complete.cases(CO2), ]
```

# How to impute missing values
There are many ways in which one can fill the missing values. However, in this section, we will learn how to **replace missing values by mean** and how to use a **machine learning algorithm like KNN or Random Forest** to impute the missing values.

```
# Filling missing values with Mean
iris$Sepal.Length[is.na(iris$Sepal.Length)] = mean(iris$Sepal.Length, na.rm=TRUE)
# alternative way is to use ifelse
iris = transform(iris, y = ifelse(is.na(Sepal.Length), mean(Sepal.Length, na.rm=TRUE), Sepal.Length))

# Using knn imputation to fill missing values
library(bnstruct)
knn.impute(iris, k = 3, cat.var = 1:ncol(iris),
          to.impute = 1:nrow(iris),
          using = 1:nrow(iris))

# Using random forest to fill missing value
set.seed(50)
iris <- rfImpute(Species ~ ., iris.na)
```
# Sorting variables in a data frame
You can sort a column in ascending or descending order. Also, one can use more than one column to sort the dataset. We will be demonstrating how to sort the data frame by using `order()` and `arrange()` functions. **The arrange() function is from `dplyr` package in R.**

```
data(mtcars)
# Sorting mtcars by mpg(ascen) variable - order function
mtcars[order(mtcars$mpg),]
# Sorting mtcars by mpg(desc) variable - order function
mtcars[order(mtcars$mpg, decreasing = TRUE),]
# we can use (-)ve sign
mtcars[order(-mtcars$mpg),]

# Sorting by more than two variables
mtcars[order(mtcars$mpg, mtcars$cyl),]

# Using arrange function from dplyr package
require(dplyr)
arrange(mtcars, mpg) # ascending order one variable
arrange(mtcars, mpg, cyl) # ascending order two variables
arrange(mtcars, desc(mpg)) # descending order one variable
```

# Removing duplicated values from a data frame
Duplicate values are mostly considered from a row's perspective, but one may require to identify duplicate columns. In this example, we will see how to remove duplicated rows as well as duplicated columns.

```
data(CO2)
# Adding duplicated row
CO2 <- rbind(CO2, CO2[2,])
nrow(CO2)
# Removing duplicated rows
CO2[!duplicated(CO2), ]
nrow(CO2)

# Adding duplicate columns for demonstration
data(CO2)# Resting the data
CO2 <- cbind(CO2, CO2[,2])
colnames(CO2)[6] <- "Type"
# Removing duplicated columns
CO2[, !duplicated(colnames(CO2))]


data(CO2) # Resting the data
CO2 <- cbind(CO2, CO2[,2])
colnames(CO2)[6] <- "Type" # naming the column
# Removing duplicated columns and keeping the last columns
CO2[, !duplicated(colnames(CO2), fromLast = TRUE)]
```

# Renaming levels of a factor variable in a data frame
To rename levels of factor variable can be tricky but is pretty simple. First, you need to call the levels of a factor variable by using `levels()` function and then assign new names to the desired levels. Remember, the order of new variable names should be the same.

```
# Renaming Mississippi level to Miss
levels(CO2$)[2] <- "Miss"
# Renaming all the levels
levels(CO2$) <- c("Queb", "Missi") # follow the order
```
# Generating frequency table(one-way, two-way etc)
To generate a frequency table use `table()` or `CrossTable()` function from `gmodels` package in R. The output of a `CrossTable()` functions resembels the output of **ctable in SAS**.  The output also includes Row Totals, Column Totals, Table Total and chi-sqaure contribution information.

```
# Building frequency table - univariate
table(mtcars$cyl) # ingnors NA
table(mtcars$cyl, useNA = "ifany") # gives freq of NA if present
table(mtcars$cyl, useNA = "always") # always prints NA count in a table if NA is missing 0 count is considered.

# Building frequency table - Bivariate
table(mtcars$cyl, mtcars$am)
# Building frequency table - multivariate
table(mtcars$cyl, mtcars$am, mtcars$gear)

# Using crosstable function to generate contigency table
library(gmodels)
CrossTable(mtcars$cyl, mtcars$am)
```

# Adding and deleting columns in a data frame
The addition and deletion of the new column is a part of a regular job for a data analyst. Let us see how we can **add a column called time** to the cars dataset by using the distance over speed formula.

```
# Adding new column
cars$time <- round(cars$dist/cars$speed, 1)

# Deleting a column
cars$time <- NULL
```

# Merging, combining and appending data frames
You can merge function to combine the two dataframe in R. This function is available in the base package. Using merge function, one can get the inner, outer, left, right, and cross joins.

```
# Inner Join
merge(x = data1, y = data2, by = "common Variable")

# Outer join
merge(x = data1, y = data2, by = "common Variable", all = TRUE)

# Left outer join
merge(x = data1, y = data2, by = "common Variable", all.x = TRUE)

# Right outer
merge(x = data1, y = data2, by = "common Variable", all.y = TRUE)

# Cross join
merge(x = data1, y = data2, by = NULL)
```

# Summarizing data
Many different packages in R provide a different set of functions with divergent statistics. Here is the list of functions that you can consume directly.

* `summary()` function - available in basic R
* `describe()` function - there are two different function by same name. One is available in `psych` package and the other is available in `Hmisc` package
* `fivenum()` function - available in base R - `stats` package

# Summarizing data by grouped variable
It is always a good idea to look at the data over different slices. For example, you may want to look at the average mileage of a car by cylinder variable. Below is a list of functions that we will be using to achieve the task mentioned above.

* Using `aggregate()` function
* Using `tapply()` function
* Using `group_by()` and `summary()` function from `dplyr` package

{{< hint info >}}You can use these functions to aggregate data by multiple variables as well. {{< /hint >}}

```
# Using aggregate function
aggregate(x = mtcars$mpg, by = list(mtcars$cyl), FUN = "mean")

# Using aggregate() to group by more than one variable
aggregate(x = mtcars$mpg, by = list(mtcars$cyl, mtcars$gear), FUN = "mean")

# Using tapply function
tapply(mtcars$mpg, mtcars$cyl, mean)

# Using functions from dplyr package
# Loading library
library(dplyr)
mtcars %>%
  dplyr::group_by(cyl) %>%
    dplyr::summarize(mean_mileage = mean(mpg))

# Using multiple variables to group
mtcars %>%
  dplyr::group_by(cyl, gear) %>%
    dplyr::summarize(mean_mileage = mean(mpg))
```

# Transforming data between long and wide format
While working on data in R, you will realize that a lot of functions expect you to pass data in the long-form. These tasks can be achieved using functions from `tidyr` package and `rehape2` package in R.

We will first create two data frames, one with a wide format and one with the long format containing the same information.

**Creating data frame with wide format**

```
wide <- read.table(header=TRUE, text='
 subject sex control cond1 cond2
       1   M     7.9  12.3  10.7
       2   F     6.3  10.6  11.1
       3   F     9.5  13.1  13.8
       4   M    11.5  13.4  12.9 ')
# Coneverting subject variable to factor variable
wide$subject <- factor(wide$subject)
```

**Creating data frame with long format**

```
long <- read.table(header=TRUE, text='
 subject sex condition measurement
       1   M   control         7.9
       1   M     cond1        12.3
       1   M     cond2        10.7
       2   F   control         6.3
       2   F     cond1        10.6
       2   F     cond2        11.1
       3   F   control         9.5
       3   F     cond1        13.1
       3   F     cond2        13.8
       4   M   control        11.5
       4   M     cond1        13.4
       4   M     cond2        12.9 ')
# Coneverting subject variable to factor variable
long$subject  <- factor(long$subject)
```

{{< hint warning>}}
**Note**
{tidyr} package is just a new interface for the reshape2 package.
{{< /hint >}}

## Using gather() function from tidyr package to convert the data from wide to long format.

```
# loading package
library(tidyr)

wide_to_long <- gather(wide, condition, measurement, control:cond2, factor_key=TRUE)
wide_to_long
```

## Using spread() function from tidyr package to convert the data from long to wide format.
```
long_to_wide <- spread(long, condition, measurement)
long_to_wide
```

Functions which can be used from `reshape2` package in R are listed below:

* `melt()` function - used for converting wide formate to long format

* `dcast()` function - used for converting long formate to wide format


# Sub setting or filtering a data frame
Although the subset of the data (based upon a condition) can be done in many different ways in R. In this chapter, we will be exploring three different approaches or say functions. For example, say you want to take a subset of CO2 data by the Treatment variable. Say you wish to create a new data frame that has all the observations belonging to a nonchilled treatment category.

```
# Using square brackets
sub1 <- CO2[CO2$Treatment == "nonchilled", ]

# Using subset function
sub2 <- subset(CO2, CO2$Treatment == "nonchilled")

# Using filter function from dplyr package
library(dplyr)
sub3 <- filter(CO2, Treatment == "nonchilled")
```

You can use logical operators to use more than one condition spread across multiple variables. For example, get the subset of data where Treatment is nonchilled, and Type is Quebec.

```
# Using square brackets
sub1 <- CO2[CO2$Treatment == "nonchilled" & CO2$Type == "Quebec", ]

# Using subset function
sub2 <- subset(CO2, CO2$Treatment == "nonchilled" & CO2$Type == "Quebec" )

# Using filter function from dplyr package
library(dplyr)
sub3 <- filter(CO2, Treatment == "nonchilled", CO2$Type == "Quebec")
```
# Inspecting top and bottom rows of a data frame
You can use head() and tail() functions to inspect the top and bottom n observations. By default both these function print top 6 and bottom 6 observations. However, you can fetch data for more or less observations.

```
head(mtcars) # default top 6 observations
head(mtcars, 10) # prints top 10 observations
tail(mtcars) # default bottom 6 observations
tail(mtcars, 10) # prints bottom 10 observations
```
