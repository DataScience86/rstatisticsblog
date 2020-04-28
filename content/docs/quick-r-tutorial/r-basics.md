---
title: "R Basics"
weight: 2
---

# Overview
Ross Ihaka and Robert developed R at the University of Auckland in New Zealand. They started working on the tool in 1933 to help their students. However, they were then encouraged to make it open source. The language is based on another single letter programming language called S, primarily it is called S+, and it still exists.

One of the major reasons for the popularity of R is that R and its packages are **Open Source and Free**.

{{< hint info >}}
**Do You Know**
R, in its original form is a command-line language.
{{< /hint >}}

# Getting Help in R
R has an extensive help system, and this is one of the best features of R programming. One can quickly access the documentation of functions and packages by using `help()` or `?`. These functions provide access to the documentation pages for R functions, data sets, and other objects. Almost all the documents of R packages and functions contain a couple of examples showcasing how to use the function.
```
help(mean)
?mean
```

# Operators in R
R supports almost all the popular binary and logical operators. I am sure you will be familiar with nearly all of them. The operators mentioned below can be used with scalars, vectors, and matrices.

## Binary/Arithmetic Operators

| Operator |       Description       |
|:--------:|:-----------------------:|
|     +    |         Addition        |
|     -    |       Subtraction       |
|     *    |      Multiplication     |
|     /    |         Division        |
|  ** or ^ |      Exponentiation     |
|   X%/%Y  |     Integer Division    |
| X%%Y     | Modulus gives remainder |

## Arithmetic operators in action
```
# adding two values
2 + 2
# Multiplying
23*34
# Integer division
1990%/%23
# Calculating Modulus
7%%2
```

Although R is a remarkable statistical tool, there is one exasperating thing about R that it is a case-sensitive language. That means that **view** (with smaller case v)and **View**(with capital V) are considered as two different objects.

## Logical Operators
Logical operators return TRUE if the condition is met and return FALSE if the condition is not met. They can be used with both numbers and strings.

| Operator |        Description       |
|:--------:|:------------------------:|
|     >    |       Greater than       |
|    >=    | Greater than or equal to |
|     <    |         Less than        |
|    <=    |   Less than or equal to  |
|    ==    |         Equal to         |
|    !=    |       Not equal to       |
|   x | y  |          x OR y          |
|   x & y  |          x and y         |
|    !x    |           Not x          |

## Logical Operators in action
```
# Using Great than
10 > 11
# Using equal to
"Hanna"=="hanna"
# Using not x
!10 == 11
#Using AND operator
(10 == 10) & (2 ==2)
```

## Assignment operator
The **assignment operator** is used in programming languages to save or assign a value to the variable. The variable can then be used for further processing. In R, we use **assignment operator (<-)** to assign a value. We can also use **equal to (=) symbol** as assignment operator. However, **assignment operator (<-)** are far more prevalent in R than the equal to sign.

```
# Assigning number values
num <- 23
num

# Assigning string value
strng <- "Hanna"
strng
```

{{< hint info >}}Strings like most other programming languages are defined using double or single quotes. {{< /hint >}}

# Numbers and Strings
Numbers and strings are what constitute any dataset in general. So it becomes crucial we understand some of the most common tasks and functions you will be required to execute while dealing with them in general.

## Working With Numbers

### Generating sequence of numbers
To generate sequence of numbers one can either use **semicolon(:)** or can use `seq()` function.

```
# Using (:) to generate sequence of integer numbers
1:10
# Using seq() function to generate sequence of numbers
seq(10, 20, by = 0.7)
```

### Generating uniformly distributed random numbers
Among many functions, the functions which I like the most are `runif()` and `sample()` functions.

```
# Using runif() function to generate 10 random numbers
# By default generates number between 0 and 1
runif(10)

# Generating numbers between 200 to 500
runif(10, min = 200, max = 500)

# Generating four random numbers **REPLACEMENT**
sample(10:15, 4, replace=TRUE)

# Generating three random numbers **WITHOUT REPLACEMENT**
sample(10:15, 4, replace=FALSE)
```

sample() function is often used for creating random samples from the dataset. The sample data can then be used for training Machine Learning models or to deep dive and understand the data.

### Generate random numbers from a normal distribution
A normal distribution is a distribution that follows a bell curve. Statistically speaking, its mean, median, and mode are all the same.

```
# Using rnorm() function to generate 10 random numbers
rnorm(10)
# Setting the desired standard deviation and mean
rnorm(10, mean = 5, sd = 2)
```

### Generating the same sequence of random numbers
The `set.seed()` function can be used to generate the same set of random numbers. A useful function that can help you to generate reproducible results. The function takes one argument, which is an integer number. Keeping that number the same gives you identical results.

```
# With set.seed()
# Output 1
set.seed(23)
rnorm(10, mean = 5, sd = 2)
# Output 2
set.seed(23)
rnorm(10, mean = 5, sd = 2)

# Without set.see()
# Output 1
rnorm(10, mean = 5, sd = 2)
# Output 2
rnorm(10, mean = 5, sd = 2)
```


### Rounding numbers to the nearest value
We have a couple of ways to achieve this. One can round the values to the nearest integer, to the upper side, to the lower side, or towards zero. The following set of functions can be used to achieve either of the said tasks.

```
# Generating a sequence of numbers
numerSeq<- seq(0, 1, by=.05)

# Rounding to nearest integer - it uses that .5 rule
round(numerSeq)
# Rounding to one decimal point
round(numerSeq, 1)
# Rounding towards upper side value
ceiling(numerSeq)
# Rounding towards lower side value
floor(numerSeq)
# Rounding towards Zero
trunc(numerSeq)
```

## Working With Strings
The two tasks which are very critical from the data analysis point of view are:
Combining Strings
Searching & Replacing Strings

### Combining strings
Knowing how to combine strings or a string with a number can be of great help. I often use this to represent or print my final output. Another use comes from the analysis point of view. Considering these two tasks in mind the two most widely used functions are `paste()`(space is a default separator) or `paste0()`(there is no separator) function and `sprintf()` function.

```
# Combing two strings using paste() function
paste("Hanna", "Ask")
# Choosing different separator
paste("Hanna", "Ask", sep = "$")

# Using paste0() function
paste0("Hanna", "Ask")
```

You can also pass a collection of string inside the `paste()` function.  This collection of similar elements in R is formally called a vector. More on this later.

```
# Creating a vector of string
strgVec <- c("Cat", "Dog", "Fish", "Cow")
# Combing the values by +
paste(strgVec, collapse = "+")
```

sprintf() function is derived from c programming.

```
# Using sprintf() function to combine two string
sprintf("My name is %s", "Hanna")

# Combining a string and an integer
sprintf("My name is %s and I am %d years old", "Hanna", 30)
```

### Searching and Replacing strings
We will cover three very useful functions here are those are `sub()`, `gsub()` and `grep()`.

```
# Defining a string
strng <- “You’re gonna need a bigger boat boat.”

# Replacing boat with car
sub("boat", "car", strng)

# Replacing boat by  with car at all instances
gsub("boat", "car", strng)

# Returns the index where the string matches
grep("[car]", letters)
```

# Data types and structure
In R, there are six data types and four data structures.

## Data Types
1. **Character** - it the collection of string. Example - "Hanna", "Dog", "Male".  
2. **Numeric** - it is a numeric value which is represented by decimal points. Example - 10.4, 12.45.  
3. **Integer** - its is also a number but only the integer part. Example - 109, 123, 34.  
4. **Logical** - the Boolean values. Example  - TRUE, FALSE
5. **Factor** - qualitative variable which can be either of nominal or ordinal type. If  it is ordinal then it is called as ordered factor. Example Nominal - "Male" and "Female". Example Ordinal - "Good", "Average" and "Best".  
6. **Complex** - a number which has got an imaginary part to it.  

{{< hint danger>}}
Integer values can also represent the factor variable. So always make a habit of going back and looking at the metadata for variable information. {{< /hint >}}


## Data Structure
The data structures in R are defined based on the dimensionality and homogeneity of data type it can hold.

1. **Vector** - They are also formally known as **Atomic Vectors**. A Vector can hold only **one type of data** and is **one-dimensional**.

2. **List** - List is also **one-dimensional** structure; however it can store **multiple data types**.

3. **Matrix** - Matrix is **two-dimensional** structure and can only save **one data type**.

4. **Data Frame** - Data Frame is also a **two-dimensional** structure but can save **multiple types of data**.

## Vector manipulation
Now we will learn about some of the most basic data manipulation functions. The knowledge of these functions is an absolute must for anyone to move forward and perform any kind of data analysis task.

### Defining vectors
Here is a collection of all the functions which are used to define different data types and structures in R programming.

```
# Defining character vectors
characterVector <- c("Football", "Cricket", "Tennis", "Badminton")
# Defining numeric vectors
numericVector <- c(12.3, 23.4, 17.9, 89.7)
# Defining integer vectors
integerVector <- c(12L, 23L, 17L, 89L)
# Defining logical vectors
logicVector <- c(TRUE, FALSE, TRUE, TRUE)

# Defining factor - nominal
factorVector <- factor(characterVector)
# Defining factor - ordinal
orderedFactorVector <- factor(characterVector, ordered = TRUE)
```

### Verifying and checking the class of the vectors
For vectors, when we check the data structure type, it returns the type of the data which it holds. For checking the class of the vector we can use either `class()` function or `typeof()` function. There are other functions, but these are the common ones.

```
# Using class() function to check the object type
class(numericVector)

# Using typeof function to check the object type
typeof(numericVector)
```

If you just wish to check the type of vector, then we can use `is` family functions. These functions will return TRUE if the vector belongs to a specific kind else it returns FALSE.

```
# Checking if the vector is character type
is.character(numericVector)
# Checking if the vector is numeric type
is.numeric(numericVector)
```


### Accessing the elements of a vector
The elements inside a vector can be accessed using an index. Unlike other programming languages like C or Python, the indexing in R starts from 1 and not zero.

```
# Extracting third elements
characterVector[3]
# Extracting multiple elements
characterVector[c(1,3)]

# Deleting element
characterVector[-1]
# Deleting multiple element
characterVector[-c(1,3)]
```

{{< hint warning>}}
**Note**
One is not allowed to pass both positive and negative index values.
{{< /hint >}}

### Replacing and adding values to a vector
To replace existing values in a vector. First, call the value using **square brackets []** and then simply assign a new value to it.

```
# Replacing football with basketball
characterVector[1] <- "Basketball"
characterVector
# Replacing more than one values
numericVector[c(1,4)] <- c(55, 66)
numericVector
```

To add new value to a vector, you can use either of the below approaches based upon your requirement.

### Using Index
The **numericVector** contains 4 elements. We will add a new element to this vector by using the index. However, this method only allows us to add a new element at the end of the vector.
```
# Adding element at the end.
numericVector[5] <- 77
numericVector
````

### Using c() function
By using `c()` function, you can add new element either at the beginning or at the end of a vector.
```
# Adding element at the end.
numericVector <- c(numericVector, 99)
numericVector

# Adding element at the beginning
numericVector <- c(99, numericVector)
numericVector
```

### Using append() function
If you wish to add a new element at any given index in a vector, then `append()` function is the correct choice. The function takes three arguments.

```
# Using append function to add value after 4th positon
numericVector <- append(numericVector, # vector
                        99, # element to be interted
                        4) # index after which to be inserted
```

### Getting the index of a particular element
The below code can be used to get the index of an element in R.
```
# Printing the index of values which are equal to 99
which(numericVector == 99.0)
```

### Other important vector manipulation functions
Below is the list of essential and useful functions for the manipulation of vectors in R.

### Sorting a vector
```
# Sorting in ascending order
numericVector[order(numericVector)]
# Sorting in descending order
numericVector[order(numericVector, decreasing = TRUE)]
```

### Checking and Removing missing values
```
# Adding NA value to a vector
numericVector[2] <- NA
# Checking if missing value is present
is.na(numericVector)

# Removing NA values using ! not
numericVector[!is.na(numericVector)]
# Removing NA values using na.omit() function
na.omit(numericVector)
```

### Sub setting the vector and getting length of a vector
```
# Getting elements greater than 30
numericVector <- numericVector[numericVector > 30]

# Checking the total number of elements in the new vector
length(numericVector)
```

## List manipulation

### Defining list
To define a list we use `list()` function. The function can be used to create simple list or named list.
```

# Defining list
example1 <- list(c(2,3,4), c("aa", "bb", "cc", "dd"), c(TRUE, TRUE))
example1

# Defining vectors
empName <- c("Chris", "Robin", "Matt")
empSalary <- c(2000, 4000, 6000)
bonusGiven <- c(TRUE, TRUE, FALSE)

# Defining list using vectors
listStruct <- list(empName, empSalary, bonusGiven)
listStruct
# Defining Named list
namedListStruct <- list("empName" = empName,
                   "empSalary" = empSalary,
                   "bonusGiven" = bonusGiven)
namedListStruct
```

### Referencing values of a list
A value inside a list can be accessed using an index or by using the name(if it is a named list). Fundamentally list is nothing but a collection of vectors. That means we can apply all the data manipulations, which we have just learned in the **Vector Manipulation** section.

### Extracting values from a list
```
# Extracting a value list of emp names from unnamed list
listStruct[[1]]

# Extracting a value list of emp names from the named list
namedListStruct$empName

# Extract Robin from the emp names
listStruct[[1]][2]

# Extracting a value list of emp names from named list
namedListStruct$empName[2]
```

### Replacing values in a list

```
# Replace salary for Robin by 8000
listStruct[[2]][2] <- 8000
listStruct

# or in named list
namedListStruct$empSalary[2] <- 8000
namedListStruct
```

### Unlisting the list
`Unlist()` function can be used to flatten out the list to one level.
```
unlist(listStruct)
```

### Checking the class of each vector in a list
```
lapply(listStruct, class)
```

{{< hint info >}}
A list can consist of multiple levels, and one can also create a nested list. Also, lists can be used to bundle objects of different lengths.
{{< /hint >}}


## Matrix Manipulation


### Defining Matrix
As it is a two-dimensional structure while defining, we need to mention the number of rows and number of columns.

```
# Defining a matrix
matStruct <- matrix(integerVector,
                    nrow = 2, ncol = 2,
                    byrow = TRUE)

# Defining a matrix
matStruct1 <- matrix(integerVector,
                    nrow = 2, ncol = 2,
                    byrow = FALSE)
```

### Basic operations related to matrix
In the below code snippet we are sharing some functions which are good to know and will help you with your data science work.
```
# naming columns
colnames(matStruct) <- c("col1", "col2")
# naming rows
rownames(matStruct) <- c("row1", "row2")
# Getting the dimension of the matrix
dim(matStruct)
# Getting the count of the rows
nrow(matStruct)
# Getting the count of the columns
ncol(matStruct)
# Accessing 2 column values
matStruct[, 2]
# Accessing 1 row values
matStruct[1, ]
# Combing two matrix by columns
cbind(matStruct, matStruct1)
# Combing two matrix by rows - appending
rbind(matStruct, matStruct1)
```

In this chapter, we looked at all the basic concepts of R Programming. We learned and spent some time looking at things like different operators, data types, structures, and some must-know functions, which will enable you to manipulate these structures.  We hope you got some sense of how powerful this software can be. In the next chapter, we’ll look at an extensive list of data manipulation tasks related to data frames.
