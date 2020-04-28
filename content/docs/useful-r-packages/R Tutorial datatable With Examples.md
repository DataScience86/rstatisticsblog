---
title: "Tutorial data.table Package"
weight: 2
---


# Overview
If you are looking for fast execution of your code on large datasets, then you must read through this tutorial. Data manipulation tasks such as aggregations, add/remove/update of columns, joins, reading large files, etc., are all very important for any data science-related project. Keeping all these operations into mind, Matt Dowle and Arun Shrinivasan created a package called `data.table`.

**data.table** package is an extension of **data.frame** package in R. It is one of the first choices for data scientists while they work on large datasets. Some of the notable features which makes the `data.table` a package so popular and easy to learn are:-

1. Irrespective of what set of operations you like to perform the `data.table` offers a concise and consistent syntax.
2. It automatically provides fast **primary** and **secondary** indexing of rows.
3. The package is capable of automatically optimizing the internal operations, leading to fast and memory-efficient code. Especially, tasks like **join** and **group by**.

So, if you are looking to reduce the execution and programming time of your r code, then this package named **data.table** is for you.

# Reading large datasets
Generally, the performance of R programming is not up to the mark when it comes to working with large datasets as everything is loaded into the RAM. As part of the solution, the data.table package was designed. Lets us see how you can load large datasets using a `fread()` function from this package.

Below is a code using which we will upload a file containing **22,489,348 rows and 11 columns**. The dataset we are using here is **UK Housing prices paid**. It is an extensive collection of records of all the individual transactions in England and Wales since 1995.

```
# Reading a large file using fread() function
ukHousing <- fread("price_paid_records.csv")
```

When you read a file using `fread()` function from **data.table** package the function loads it as a *data table* object. We will talk about object type in the coming sessions. For now, it is important to note that if an object is not in a data table format, then you must convert it in order to take advantage of data.table package.

1. Apart from reading the csv and text files fread() can also accepts `http` and `https` URLs as input.
2. Unlike read.csv(), the columns of character data types are not converted to factors by default while reading the data file.
3. data.table never uses row names.
4. To visually separate the row numbers from the first columns, row numbers are printed with a colon(`:`).

Below is the comparison of time taken by at least three different functions in R:

```
# Reading a large file using fread() function
system.time(fread("price_paid_records.csv"))
# Time difference of 2.711095 mins

# reading a large file using read.csv() function
system.time(read.csv("price_paid_records.csv"))
#Time difference of 22.23424 mins
```

```
> system.time(fread("price_paid_records.csv"))
   user  system elapsed
  51.04    3.61   40.63

> system.time(read.csv("price_paid_records.csv"))
   user  system elapsed
1053.73    9.36 1073.19
```

# Querying A Data Table

Mostly when we use DT(short form for data table object), we refer to it as **"querying DT"**. DT is designed to do a lot more than just subjecting the data frames by row and columns. To understand the querying bit, we need to look and understand the general form of data.table syntax. The syntax is given below:

```
DT[i, j, by]
```

The above syntax can be compared to an SQL query. Here **i** inside the square [] brackets is an equivalent for **WHERE** or **ORDER BY**. Similarly, **j** represents **SELECT** or **UPDATE**, and finally, **by** represents the **GROUP BY** from the SQL. Individuals who are familiar with SQL will now understand why it is referred to as **"querying DT"**. The above statement can also be read as *subset/reorder rows using i, then calculate j, grouped by*.


You can use `as.data.table()` or simply `data.table()` and `setDT()` functions to convert any regular R data frame to a data table.


```
# Using as.data.table() function
dataTableIris = as.data.table(iris)
# Printing top 6 rows
head(dataTableIris)
```

```
   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1:          5.1         3.5          1.4         0.2  setosa
2:          4.9         3.0          1.4         0.2  setosa
3:          4.7         3.2          1.3         0.2  setosa
4:          4.6         3.1          1.5         0.2  setosa
5:          5.0         3.6          1.4         0.2  setosa
6:          5.4         3.9          1.7         0.4  setosa

```

Notice that a `data.table` prints the row numbers with a colon. This is done to visually separate row numbers from the first column. By using any of the above two functions, one can easily convert existing data.frame object to a data.table object.

# Data manipulation - Common Tasks

In data.table package columns are referred to as if they are variables, much like SQL. This means that when we pass column names for extracting the information from a data.table object, we need not add data table names as prefix(e.g. `matcars$` ). Nevertheless, using `mtcars$mpg` and `mtcars$cyl` would work just fine. Below we have provided the list of some of the everyday tasks related to data manipulation.

## Get first 10 rows of Uk Housing data

```
# Getting the top 10 rows
ukHousing[1:10]
```

We passed row indices in the `i`, and as there are no conditions, the code returns the top 10 rows from the row indices. You can also use a `head()` to get the top 10 rows.

## Sorting data in data.table

1.**Sorting data by single variable** - For example, sorting house price in descending order - This task can be achieved using multiple functions. We can use either `order()` from `{base}` package or `setorder()` function from `{data.table}` package to achieve this task.

```
# Sorting data by using order()
sort1 <- ukHousing[order(-Price)] # (-)ve sign is used to get data in descending order
head(sort1)

# Sorting data by using setorder()
sort2 <- setorder(ukHousing, -Price) # For Ascending order remove (-)ve sign
head(sort2)
```

2.**Sorting data by multiple variables** - For example, sort house price data by duration and price. To add various variables, just pass column names separated by comma(,) in both the functions.

```
# Sorting data by using order()
sort1 <- ukHousing[order(Duration, -Price, )]
head(sort1)

# Sorting data by using setorder()
sort1 <- setorder(ukHousing, Duration, -Price)
```


## Subsetting data by column(s) in data.table using `j`

As mentioned earlier, we can directly use column(s) names for sub-setting in data.table. Also, if you want all the rows, then you can skip `i` as a section.

1.**Select single column** - When we select a single column, the output returned is a **vector**. In case you wish to keep the structure as data.table, you need to wrap the variable name within `list()` function. If you find `list()` to boring you can instead use **.()**, it is an alias `list()` in data.table package. Most people prefer using **.()** instead of **list()** and going forward we will be continue to use **.()**, hereafter.

```
# Subsetting  data.table by selecting one column
singleColumn <- ukHousing[ , County]
head(singleColumn)

# Output
[1] "GREATER MANCHESTER" "THURROCK"           "SOMERSET"          
[4] "BEDFORDSHIRE"       "WEST YORKSHIRE"     "WILTSHIRE"

# Sub setting  data.table by selecting one column keeping structure as data.table
singleColumn <- ukHousing[ , list(County)]
head(singleColumn)

# Output
               County
1: GREATER MANCHESTER
2:           THURROCK
3:           SOMERSET
4:       BEDFORDSHIRE
5:     WEST YORKSHIRE
6:          WILTSHIRE
```

2.**Select multiple columns** - This task is easy, and all we need to do is pass the column names separated by comma(,) inside the .().

You must pass the column names inside list alias to avoid the second column going into `by` section.

```
# Subsetting  data.table by selecting one column
multiColumn <- ukHousing[ , .(County, Price)]
head(multiColumn)

# Output

                      County  Price
       1: GREATER MANCHESTER  25000
       2:           THURROCK  42500
       3:           SOMERSET  45000
       4:       BEDFORDSHIRE  43150
       5:     WEST YORKSHIRE  18899
      ---                          
22489344:     WEST YORKSHIRE 175000
22489345:     WEST YORKSHIRE 586945
22489346:     WEST YORKSHIRE 274000
22489347:     WEST YORKSHIRE  36000
22489348:     WEST YORKSHIRE 145000
```

3.**Creating new columns using expressions** - You can use **:=** operator to create new variables by applying operations on rows. In the below example, we are creating a new variable c, which is a sum of a and b variable. This is also called as **Assignment by reference**.

```
df <- data.table(a  = c(1,2,3), b = c(4,5,6))
df[, c := a + b]

# Output
   a b c
1: 1 4 5
2: 2 5 7
3: 3 6 9
```


## Rename column in data.table

As column names n data.table is passed inside *list*, we can rename columns as we would do while creating *list*. In below renaming **country** and **price** columns to **A_Country** and **A_Price**.

```
# Renaming Columns in data.table
renameColumn <- ukHousing[ , .(A_Country = County, A_Price = Price)]
colnames(renameColumn)

# Output
[1] "A_Country" "A_Price"
```

## Using expressions with columns in data.table

The in `j` in data.table can handle much more than just selecting columns, and it can also be used for computing on columns, also referred to as using expressions. Let's say; you want to combine country and district variables separated by "\_".


```
# concatinating two Columns in data.table
expressionExample1 <- ukHousing[ , .(paste(County, District, sep = "_"))]
head(expressionExample1)
# Output
                                V1
1:       GREATER MANCHESTER_OLDHAM
2:               THURROCK_THURROCK
3:              SOMERSET_SEDGEMOOR
4: BEDFORDSHIRE_NORTH BEDFORDSHIRE
5:            WEST YORKSHIRE_LEEDS
6:             WILTSHIRE_SALISBURY
```

## Sub setting data using `i` and applying expressions using `j`

While working with data.table you can subject the data using `i` IE identify rows on which you want to perform your further data analysis. It could also be phrased as *subset* in `i` and *do* in `j`.

1.**Calculate and compare the average price of houses in OLDHAM District**

In the below code, we first subset the data and get row where District is "OLDHAM". We then take these rows and apply the mean function in the `j` part of the syntax.

```
# Avg house prices in OLDHAM
ans <- ukHousing[ District == "OLDHAM", .(Avg_Price = mean(Price))]
ans

# Output
    Avg_Price
1:  91258.15
```

2.**How many houses do we have in OLDHAM**

Here we need to count the total number of rows in the subject. To achieve this, we can use two different functions. One we can use `length()` and another we can use is `.N()` function. Please note, length() will require us to pass the argument. This could be any variable name. However, `.N` does not require a function to be passed.

```
# Total number of rows where Dristrict is OLDHAM
ans <- ukHousing[ District == "OLDHAM", length(District)]
ans

# Output
[1] 76576


# Total number of rows where Dristrict is OLDHAM
ans <- ukHousing[ District == "OLDHAM", .N]
ans

# Output
[1] 76576
```


The special symbol `.N` is a built-in variable that saves the total number of observations in the current group. In the next section, we will see how we can combine `.N` with the `by`. But before we move ahead, let us see how to refer to columns by name in `j`.

# Referring columns by name in `j` (just like data.frame)

If you are explicitly calling out the names, then you can follow the `data.frame` way of calling the column names. However, if you have stored the column names in a vector than there are two options -

A. **Using `..` prefix** - The `..` prefix requests the `data.table` to look for the selected colNames by going "up-one-level," I.E., in the global environment. If you are familiar with the Unix terminal, you should be able to connect to the `..` command, which also means "up-on-level".

```
# Calling multimple column names using `..` prefix
colNames = c("Duration", "County", "Price")
head(ukHousing[ , ..colNames])

# Output

   Duration             County Price
1:        F GREATER MANCHESTER 25000
2:        F           THURROCK 42500
3:        F           SOMERSET 45000
4:        F       BEDFORDSHIRE 43150
5:        F     WEST YORKSHIRE 18899
6:        F          WILTSHIRE 81750
```

B. **Using `with` argument ** - By default the `with` argument is set as **TRUE**. That allows  `data.table` to refer to the columns as variables. Setting the argument  `with = FALSE` actually disables this property thereby restoring the `data.frame` mode.


```
# Calling multimple column names using `with` argumnet
colNames = c("Duration", "County", "Price")
head(ukHousing[ , colNames, with = FALSE])

# Output

   Duration             County Price
1:        F GREATER MANCHESTER 25000
2:        F           THURROCK 42500
3:        F           SOMERSET 45000
4:        F       BEDFORDSHIRE 43150
5:        F     WEST YORKSHIRE 18899
6:        F          WILTSHIRE 81750
```

TO DESELECT THE COLUMNS YOU CAN USE **!** or **-**.

## How to summarize a larger list of variables

To summarise a large list of variables in `data.table` you can use **.SD** and **.SDCols** operators. Here SD stands for *subset of data*.

In the below code snippet, we will see how to get the following:

1.**Mean of multiple variables**

```
ukHousing[, lapply(.SD, mean), .SDcols = c("Price", "var2")]
```

2.**Getting summary statistics of all the numeric variable**

```
ukHousing[, lapply(.SD, mean)]
```

3.**using UDF for calculating different statistics**

```
ukHousing[, lapply(.SD, function(x) c(mean = median(x), mode(x)))]
```


# Aggregations using `by` in `data.table`

We have explored how to use`i` as `WHERE` or `ORDER BY`. We also learned how to use `SELECT` or `UPDATE` as part of `j`.  Next, we will learn how to combine these two with `by` to perform data operations *by groups*.

## How to get number of rows by *District*

The below code will return the top 5 and below 5 results. Also, note if you do not pass the name of the column in `j` it will be named as **N**.

```
byExample1 <- ukHousing[, .(Count = .N), by = .(District)]
byExample1

# Output
              District  Count
  1:             OLDHAM  76576
  2:           THURROCK  69498
  3:          SEDGEMOOR  52762
  4: NORTH BEDFORDSHIRE   2801
  5:              LEEDS 299133
 ---                          
450:      CITY OF DERBY  87152
451:             RADNOR     13
452:             BRYHER      1
453:    ISLES OF SCILLY    397
454:        ST MARTIN'S      2
```

## Compare average prices of old and new houses in LEEDS District

To calculate this, we need to do the following things:

1. Take the subset of data **WHERE** District == "LEEDS" - This will go in `i`
2. Take Average of Price variable and rename it - This will go in `j`
3. Finally, a group by on the *Old/New* Variable - This will go in `by`

```
byExample2 <- ukHousing[District == "LEEDS",
                        .(Average_House_Price = mean(Price)),
                        by = .(`Old/New`)]
byExample2

# Output
   Old/New Average_House_Price
1:       N            131776.4
2:       Y            152065.4
```

YOU CAN ALSO PASS MULTIPLE COLUMNS SEPARATED BY (,) IN `by`.

```
byExample3 <- ukHousing[District == "LEEDS",
                        .(Average_House_Price = mean(Price)),
                        by = .(`Old/New`, Duration)]

# Outlook

   Old/New Duration Average_House_Price
1:       N        F            133247.2
2:       N        L            120133.6
3:       Y        F            164630.3
4:       Y        L            136957.9
5:       Y        U             98157.0
6:       N        U             56837.5
```


## How to get ordered results by grouping variable in `data.table`

Although `data.table` retains the original order of groups by design, at times, you may be required to sort the values by each group. One may be interested in doing so to understand top performers in each group. You can achieve this by changing `by` to `keyby`. It will automatically update the values within each group in the ascending order. Below code results can be compared with the previous one, and you will notice that it is not ordered in ascending order.

`keyby` also **sets a key** after ordering the values by setting an attribute called `sorted`.


```
byExample3 <- ukHousing[District == "LEEDS",
                        .(Average_House_Price = mean(Price)),
                        keyby = .(`Old/New`, Duration)]
byExample3

# Output

   Old/New Duration Average_House_Price
1:       N        F            133247.2
2:       N        L            120133.6
3:       N        U             56837.5
4:       Y        F            164630.3
5:       Y        L            136957.9
6:       Y        U             98157.0
```

## How to write Sub Queries like SQL using **Chaining** operation

Chaining is a process using which we can avoid intermediate assignments of temporary variables. Here the results of the previous operation are passed directly to the next one.

In the below code, I will order the output of the previous code by *Average_House_Price* variable but in descending order.

```
byExample4 <- ukHousing[District == "LEEDS",
                        .(Average_House_Price = mean(Price)),
                        keyby = .(`Old/New`, Duration)][order(-Average_House_Price)]

byExample4

# Output

   Old/New Duration Average_House_Price
1:       Y        F            164630.3
2:       Y        L            136957.9
3:       N        F            133247.2
4:       N        L            120133.6
5:       Y        U             98157.0
6:       N        U             56837.5
```

# Using `data.table` for joining large tables

Merging tables in `data.table` is very similar to `data.frame`. You can merge tables using `merge()` function. However, in `data.table` The merging is done based on the common key variable as a primary key. On the other hand, `data.frame` takes a common variable as a primary key to merge.

For this exercise, we will first define two tables, as given below.

```
# Defining the tables
data.table1 <- data.table(primaryKey = letters[rep(1:10)], X= 1:10, key = "primaryKey")
data.table2 <- data.table(primaryKey = letters[rep(2:8)], Y= 10:1, key = "primaryKey")

# Printing the output
> data.table1
    A  X
 1: a  1
 2: b  2
 3: c  3
 4: d  4
 5: e  5
 6: f  6
 7: g  7
 8: h  8
 9: i  9
10: j 10

> data.table2
    A  Y
 1: b 10
 2: b  3
 3: c  9
 4: c  2
 5: d  8
 6: d  1
 7: e  7
 8: f  6
 9: g  5
10: h  4
```
We will now look at how to get the common elements between the two by doing inner join.

```
merge(data.table1, data.table2, by = "primaryKey")

# Output
    primaryKey X  Y
 1:          b 2 10
 2:          b 2  3
 3:          c 3  9
 4:          c 3  2
 5:          d 4  8
 6:          d 4  1
 7:          e 5  7
 8:          f 6  6
 9:          g 7  5
10:          h 8  4
```

To perform **Left Join**, we need to pass **all.x = TRUE** argument. Below are the code snippet and its output.

```
merge(data.table1, data.table2, by = "primaryKey", all.x = TRUE)
    primaryKey  X  Y
 1:          a  1 NA
 2:          b  2 10
 3:          b  2  3
 4:          c  3  9
 5:          c  3  2
 6:          d  4  8
 7:          d  4  1
 8:          e  5  7
 9:          f  6  6
10:          g  7  5
11:          h  8  4
12:          i  9 NA
13:          j 10 NA
```


I am sure with this example you will be able to figure out how to perform other joins like **Right Join**, **Full Join**, and other joins. If not please refer the documentation of `data.table` package.

# Must-know functions from `data.table` package

Below is the list of some must-know functions from data.table package. For this section, we will not be sharing the output.

## How to determine duplicated rows in data.table
There are a couple of functions that we can use from data.table to deal with duplicated rows. The use of a particular function depends on the task you want to achieve.

1.**unique()** - the function returns a data table with all the duplicated rows removed. The function also can be used to specify a particular column by which you wish to check for duplicated values.

```
# Removing duplicates considering all variables
unique(df)

# Removing duplicates considering a particular variable(s)
unique(df, by = "var1")
```

2.**duplicated()** - The output of this function is a logical vector indicating which rows are duplicates.

```
# getting logical vectors indicating duplicate rows
duplicated(df)
```

3.**anyDuplicated()** - The function is similar to the `duplicated()` function. The only difference is that this function returns inter values as output. It returns index `i` of the first duplicated entry if there is one, and 0 otherwise.

```
anyDuplicated(df, by=c("Var1"))
```

## How to define range in data.table
`between()` function can be used to define a range. The range generated includes values of both start and end values. You can use this function to check if the values of a variable lie between a certain range of values.

```
# Getting houses prices where prices range between 100k and 200k
ukHousing(Price %between% c(100000, 200000))
```

## How to reshape large datasets faster in data.table
`dcast.data.table` and `melt.data.table` are two functions which provides a very fast version of `reshape2:dcast()` and `reshape2:melt`. These functions can handle very large datasets quite efficiently and are also able to manage memory quite efficiently in comparison to functions from `reshape2` package.

```
dcast(df, time ~ variable, fun=mean)
melt(df, id=1:2, measure="f_1")
```

## How to rank large datasets using data.table

`data.table` provides a function named `frank()` to achieve faster ranking over the large datasets. The function is similar to base `rank()` function, but performance-wise, it is much faster. You can think of this function as a translation of RANK OVER PARTITION windows function in SQL.

The function is capable of accepting vectors, lists, data.frames, or data.tables as input.

```
x = c(4, 1, 4, NA, 1, NA, 4)
df = data.table(x, y=c(1, 1, 1, 0, NA, 0, 2))
frank(df, cols="x")
```

# Some essential set of functions
The set functions consists of `union`, `intersect`, `setdiff` and `setequal`. These functions can be very crucial when working with multiple datasets. The `data.table` package in R provides a set of these functions which perform these tasks at a super-fast speed when it comes to large datasets.

1. **fintersect** will return copies of common rows.
2. **fsetdiff** will return copies of rows that are not common.
3. **funion** will return copies of all the rows.
4. **fsetequal** will return FALSE unless all the rows are similar.

# How to write large datasets to the local
You can use `fwrite()` function to quickly write the larger file back to your local system.

# How to generate lead/lag values for time series with data.table
To generate lead/lag values, the `data.table` provides `shift()` function.

```
# Shifting data by 1 lag
df <- data.table(x = seq(1, 10, 2))
df[, x1:= shift(x, 1, type = "lag")]

# Output
> df
     x x1
 1:  1 NA
 2:  3  1
 3:  5  3
 4:  7  5
 5:  9  7
```
To get the leading values in the next column, all you need to do is mention **type = "lead"**. I encourage you to try it on your local machine.

In this chapter, we learned how to use `data.table` to deal with very large datasets with efficient memory utilization. This package provides an excellent solution for data wrangling tasks in R. In the next tutorial, we will talk about **dplyr** package. We understand that `data.table` can become a bit complicated, and for simplicity, some prefer using `dplyr` package.
