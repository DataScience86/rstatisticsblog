---
title: "Correlation"
weight: 5
---

# Overview
Correlation coefficients are used to describe the degree of association between quantitative variables. The value of the correlation lies between +1 to -1. The signs only indicate the direction of the relationship. That means a +0.86 value is equal to -0.86. However, -ve sign indicates that if one variable increases, the other decreases, and +ve sign indicates that if one variable increases, the other also increases. A value in the range of +0.20 to -0.20 indicates very week or no correlation.

R Programming supports a variety of correlations. However, we will only be discussing Pearson, Spearman, and Kendall correlation, as these are used most of the time. You can briefly learn about the [correlation and types, HERE](https://datasciencebeginners.com/2018/09/30/05-statistics-and-branches-of-statistics-part-2/)

The `cor` function produces all the correlation, as mentioned above. Although the `cor` function finds the correlation for a matrix, it does not provide any information related to the statistical significance of the association. If you are interested in that, you can use `corr.test` function.

# Quick Look - Types of correlation
Let us quickly learn when to use which correlation.

**1. Pearson correlation** - Pearson correlation is used when we want to assess the degree of association between two quantitative variables.

```
cor(x, method = "pearson")
```

**2. Spearman correlation** -  Use spearman correlation when you want to assess the degree of association between rank-ordered variables.

```
cor(x, method = "spearman")
```

**3. Kendall’s correlation** - Kendall's correlation can also be used to assess the degree of association between rank-ordered variables. However, it is a non-parametric measure.

```
cor(x, method = "kendall")
```

# Generate a correlation matrix
One can generate a correlation matrix given any correlation type using `cor` function. However, ensure that you have carefully looked into the data type. That will ensure that you produce the correct results using appropriate correlation. Let us generate a correlation between the variables of `iris` data.

```
# Computing correlation
corMat <- cor(x= iris[, -5], method = "pearson")
# Rounding the values to two decimal
round(corMat, 2)
```
```
# Output
             Sepal.Length Sepal.Width Petal.Length Petal.Width
Sepal.Length         1.00       -0.12         0.87        0.82
Sepal.Width         -0.12        1.00        -0.43       -0.37
Petal.Length         0.87       -0.43         1.00        0.96
Petal.Width          0.82       -0.37         0.96        1.00
```

The above matrix suggests that `Petal.Width` and `Sepal.Length` have a high correlation. Similarly, you find other variables that show a high correlation between each other.

The same function can be used to print the correlation matrix between the two ranked variables. For example, we can calculate the correlation between the cylinder type and gear. Both of these variables are rank variables.

```
# Looking into the spearman correlation
cor(mtcars[, c("cyl", "gear")], method = "spearman")
```
```
# Output
           cyl       gear
cyl   1.0000000 -0.5643105
gear -0.5643105  1.0000000
```

# Testing correlation for significance
To check the statistical significance of the correlation, we can use `cor.test` function. The function generates the p-value which, when compared to alpha value, reveals if the correlation is statistically significant or not.

{{< hint info>}}
According to the decision rule, if *p-value* is less than *alpha(0.05)* we reject the null hypothesis. Here null hypothesis is - that correlation between the two variables is equal to zero.
{{</ hint>}}
```
# Checking significance of correlation
cor.test(mtcars$mpg, mtcars$disp)
```
```
# Output

    Pearson's product-moment
    correlation

data:  mtcars$mpg and mtcars$disp
t = -8.7472, df = 30,
p-value = 0.000000000938
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.9233594 -0.7081376
sample estimates:
       cor
-0.8475514
```

Based on the above test results, we conclude that the correlation between mileage and displacement variable is significant.

# Visualizing correlation Matrix
A visualization is a powerful tool. It speeds up the process of understanding and digesting the critical points. As your dataset grows, it gets more and more challenging to go through the numbers present in your correlation matrix. So the best way to represent your insights about the relationship between variables is through correlation charts.

We are sharing some of the examples below, and that means you can use whatever suits your needs.  For building these graphs, we are using a package called `corrgram` and `corrplot`. If you don't have these R Package, then use `install.packages()` to install it on your local system.

## Example 1 - Visualizing corrgram

```
# loading package
require(corrgram)
corrgram(mtcars[, c("mpg", "wt", "disp", "hp", "qsec")], order=TRUE)
```

<figure>
  <img src="/images/correlation/corrgram1.jpeg" alt="Example code for building correlation graph">
</figure>

In the above graph:

**The Red Shade** indicates a negative correlation between the variables, darker the shade stronger the association.

**The Blue Shade** indicates a positive correlation between the variables, darker the shade stronger the association.

## Example 2 - Visualizing correlation matrix using `corrplot`
There are seven different shapes, or you can say ways in which you can represent the information - “pie”, “circle”, “square”, “number”, “ellipse”, “shade”, “color”.

> The first argument to the function is a correlation matrix

```
# loading library
require(corrplot)

# Generating correlation matrix
corMat <- cor(mtcars[, c("mpg", "wt", "disp", "hp", "qsec")])
# Building the correlation plot
corrplot(corMat, method="pie")
```

<figure>
  <img src="/images/correlation/corrplot1.jpeg" alt="Example code for building correlation graph using corrplot">
</figure>

## Example 3 - Changing the shape to a square

```
# Generating correlation matrix
corMat <- cor(mtcars[, c("mpg", "wt", "disp", "hp", "qsec")])
# Building the correlation plot
corrplot(corMat, method="square")
```

<figure>
  <img src="/images/correlation/corrplot2.jpeg" alt="Using squares to represent corrplot">
</figure>

## Example 4 - Representing the correlation information using numbers

```
# Generating correlation matrix
corMat <- cor(mtcars[, c("mpg", "wt", "disp", "hp", "qsec")])
# Building the correlation plot
corrplot(corMat, method="number")
```

<figure>
  <img src="/images/correlation/corrplot3.jpeg" alt="Using numbers to represent corrplot">
</figure>

## Example 5 - Changing the layout of the correlation graph

So far, we have been drawing the full correlation matrix. However, as we know, that the upper triangle matrix and lower triangle matrix are similar. So you can choose to represent only one half of the table.

```
# Generating correlation matrix
corMat <- cor(mtcars[, c("mpg", "wt", "disp", "hp", "qsec")])
# Building the correlation plot
corrplot(corMat, method="circle", type = "upper")
```

<figure>
  <img src="/images/correlation/corrplot4.jpeg" alt="Presenting upper half of the corrplot">
</figure>

In this chapter, we learned about functions in R programming to generate the correlation coefficient. We also looked into how to check if the correlation is statistically significant. Finally, We learned about packages in R using which we can create beautiful visualizations to present the correlation matrix. In the next chapter, we will learn about the applications of statistical hypothesis testing.
