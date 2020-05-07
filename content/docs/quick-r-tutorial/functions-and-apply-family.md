---
title: "Its All About Functions"
date: 2019-10-05
weight: 4
---

# Functions in R

By now you must have figured it out that R programming is not a traditional programming language. The language is a collection of functions that are packed together to form a package. All thanks to the open and free community, which has contributed to over 9000 packages over the years. Today one can find any function to achieve almost any statistical task in R by doing a little research. However, you may still be interested in creating your custom function in R. The custom function defined by the user is termed as **User Defined Function** in R. In this chapter, we will learn how to create a custom function.

# R Packages - A collection of similar functions

Before we move forward and see how to define a custom function, let us see how to download an R package and start using the functions from that package. The task is fairly straightforward. One can use `install.packages` function to download the R package from CRAN to the local system. Once you have the package downloaded, all you need to do is load the package in your environment to have access to the functions from the package.

{{< hint warning >}}To load a package you can use either `library()` or `require()` function.{{< /hint >}}

{{< hint info >}}These two functions are a tad different in terms that the require function returns FALSE and gives a warning if the package does not exist. On the other hand, library function returns an error.{{< /hint >}}

Given the above information, it is recommended to use *require()* function inside of a function in R. This way, when you call the function without loading the package, the program will return a warning message instead of throwing an error and exiting the program without completing the execution.

# Installing and loading R Packages
```
# Installing {devtools} package, passing arguments
install.packages("devtools", # Package name
                  dependencies = TRUE, # Download Pacakes used to build this package
                  quiet = TRUE) # Control the info printed on the console

# Loading a package
library(devtools)
# Loading using require()
require(devtools)
```

# Installing R Packages from GitHub
One can download an R package from the GitHub repository directly by using `install_github()` function. The function is available in **devtools package**.

```
# Installing Package from github
install_github("tidyverse/dplyr")
```

# How to create a user-defined function in R
A function in R consists of three parts. First is the name of the function. Second, function() keyword for defining the function. Inside this keyword, we pass our parameters. The third is the execution code, and it goes inside the curly brackets.

For example, Let’s create a function to add two numbers. This function thus requires two parameters.

```
# Creating a function to add two numbers
addTwoNumber <- function(a = 1, b = 1){
  return(a + b)
}

# Calling this function
addTwoNumber(a = 10, b = 10)
```

1. addTwoNumber - is the name of my function
2. function(a = 1, b = 1) - here a and b are the arguments which this function can take. We have also provided one as a default value to both the parameters.
3. return(a + b) - this section return the value after adding a and b.

# Defining an anonymous function in R
An anonymous function is a function which does not have any name and is also at times called as inline functions. Let’s see how we can create and use these functions. For example - You want to calculate the sum of each column in a data frame by ignoring the NA values in them.

```
# Getting column wise sum for each variable
output <- apply(mtcars, 2, function(x){ sum(x, na.rm = TRUE)})
```

# Apply family functions
In the previous example, we just saw an apply function that executes like a for loop but in a vectorized way. That makes the apply family functions faster and easy to write in most cases. Below is a list of my favorite functions from apply family.

## Using apply function
Using **apply function**, one can apply almost any function to either all the rows or columns. These functions can also be user-defined. In the above code, we saw how to use `apply` function to apply another function on all the columns. In the next example, we will apply the same function to all the rows. That should return 32 values as we have 32 rows in the mtcars dataset.
```
# Getting row-wise sum for each variable
output <- apply(mtcars, 1, function(x){ sum(x, na.rm = TRUE)})
```
## Using lapply and sapply functions
The **lapply()** and **sapply()** functions are very similar to the apply() function which we just learned. However, these functions apply the requested function only on columns. Also, these two functions are different from each other in terms of the output they produce. The **lapply function generates a list output** whereas **sapply function generates a vector output**.

### Working example
In this example, we will achieve the same task of getting sum by each column using sapply() and tapply functions. It will allow us to compare the final outputs of the functions.

```
# Using lapply() function
lapply(mtcars, FUN = function(x){ sum(x, na.rm = TRUE)})

# Using sapply() function
sapply(mtcars, FUN = function(x){ sum(x, na.rm = TRUE)})
```

## Using tapply functions
The `tapply()` function is useful in applying a function by a grouped variable. The function splits the data by a factor variable and returns the function output by each level.

### Working example
In this example, we will see how to get the average sepal length of flowers by the species.

```
# Using tapply function to get the average
# sepal length by flower species
tapply(iris$Sepal.Length, iris$Species, mean)
```

In this chapter, we reviewed the concept of functions, packages, and learned how to use some of the important apply family functions. Now you’ve gathered enough knowledge about R tool and are ready to enter the exciting world of data analysis!  In last chapters, we intend to create a list of useful functions for working with data objects. This will be a continuous effort and we request you to contribute to the same.
