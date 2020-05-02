---
title: "Hypothesis Testing"
date: 2019-12-12
weight: 4
tags: ["T-test", "Two-sample", "Paired Test", "R Code"]
category: ["R", "Statistics"]
---

> Hypothesis testing uses concepts from statistics to determine the probability that a given assumption is valid. In this chapter, you will learn about several types of statistical tests, their practical applications, and how to interpret the results of hypothesis testing.

# Overview
Through hypothesis testing, one can make inferences about the population parameters by analysing the sample statistics.

Typically hypothesis testing starts with an assumption or an assertion about a population parameter. For example, you may be interested in validating the claim of Philips that the average life of there bulb 10 years.

# Applications of hypothesis testing

1. When you want to compare the sample mean with the population mean. For example - You would like to determine if the average life of a bulb from brand X is 10 years or not.
In this case, when you want to check if the sample mean represents the population mean, then you should run **One Sample t-test**.

2. When you want to compare the means of two independent variables. One of which can be a categorical variable. In this case, we run **Two sample t-test**.

3. When you want to compare the before and after-effects of an experiment or a treatment. Then, in that case, we run **Paired t-test**.

4. When you want to compare more than two independent variables; in that case, we run **ANOVA test**

5. In all the above applications, we assumed that variables are numeric. However, When you want to compare two categorical variables, we run **Chi-square test**.


# Perform One-Sample t-test
One sample t-test is a parametric test. It is used when you wish to check if the sample mean represents the population mean or not. It assumes that the data follows a normal distribution.

```
set.seed(100)
# Generating random data with normal distibution
x <- round(rnorm(30, mean = 10, sd = 1),0)
# Checking if mean is really 10 years
t.test(x, mu=10)
```
```
# Output
    One Sample t-test

data:  x
t = 0.17668, df = 29, p-value = 0.861
alternative hypothesis: true mean is not equal to 10
95 percent confidence interval:
  9.647473 10.419193
sample estimates:
mean of x
 10.03333
```

## How to interpret One-Sample t-test results?
In the above case,

**p-value = 0.861**, this value is greater than alpha value, and thus we have to accept the null hypothesis. Here the null hypothesis was that the average life of the bulb is 10. And the alternative hypothesis was that it is not equal to 10.


**95 percent confidence interval**:
  **9.647473 10.419193** - The 95% CI also includes the ten, and thus it is fine to state that the mean value is 10.

# Perform welch two-Sample t-test
Two sample t-tests are used to compare the means of two independent quantitative variables. By default, the `t.test()` function runs a welch test, which is a parametric test. It assumes that the two populations have normal distributions and equal variances.

In the below example, we assumed that the x and y are samples taken from populations that follow a normal distribution.

```
# Generating two variable x and y
x <- c(3.80, 5.83, 11.89, 21.04, 12.45, 7.38, 6.97, 6.60)
y <- c(10.54, 8.88, 9.89, 23.74, 14.65)
# Checking if mean is really 10 years
t.test(x,y)
```
```
# Output
    Welch Two Sample t-test

data:  x and y
t = -1.2051, df = 7.9346, p-value = 0.2629
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -11.796332   3.706332
sample estimates:
mean of x mean of y
    9.495    13.540
```
## How to interpret two sample t-test results?
Here, the null hypothesis is that the mean of x - mean of y = 0<br/>
and the alternative hypothesis is that the mean of x - mean of y != 0

**As p-value(0.2629) is greater than the alpha value(0.05)**, we accept the null hypothesis and conclude that the mean of x is indeed equal to the mean of y.

**95 percent confidence interval:**
 **-11.796332   3.706332** - Also, it is evident that zero did appear in at least 95% of the experiments, and thus we conclude that our decision to accept the null hypothesis is correct.


# Perform a Paired t-test

I am taking this example from [datasciencebeginners](https://datasciencebeginners.com/2018/10/04/06-ultimate-practical-guide-to-hypothesis-testing/).

An educational institute wants to check if their course helps in improving the scores of the students. So what they do is they give a test to a bunch of students before the class started and recorded the scores. After which all these students were trained on the subject and at the end of the course another test was given to the students, and the scores were noted. They now need to understand if the course or training has resulted in better scores.

```
# Score of students before and after the course
before <- c(73, 38, 46, 99, 5, 15, 58, 78, 99, 43)
after <- c(90, 78, 21, 70, 72, 59, 55, 39, 40, 99)

# Running the paired ttest
t.test(before, after, paired = TRUE)
```
```
# Output
    Paired t-test

data:  before and after
t = -0.49552, df = 9, p-value = 0.6321
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -38.39999  24.59999
sample estimates:
mean of the differences
                   -6.9
```

## How to interpret paired t-test results?
The **p-value** of  0.63 is higher than the alpha value. That means we need to accept the null hypothesis and thus conclude that there is no significant change in test scores.


# Perform ANOVA test
ANOVA stands for analysis of variance, and to test this, we run Fishers F-test. We run this test when we want to compare the means of more than two independent variables.

For example - we may want to know if the average sepal length across three different flower species is similar or not.

Here, Null Hypothesis :: μ1 = μ2 = μ3<br/>
and, Alternative :: μ1 ≠ μ2  ≠ μ3 or μ1 = μ2  ≠ μ3 or μ1 ≠ μ2  = μ3

> You need to run the post adHoc test in case you reject the null hypothesis. It is done to check if all groups are different, or only one of them is different.

```
# Running anova
result <- aov(Sepal.Length ~ Species, data = iris)
# Checking the result
summary(result)
```
```
# Output
            Df Sum Sq Mean Sq F value              Pr(>F)    
Species       2  63.21  31.606   119.3 <0.0000000000000002 ***
Residuals   147  38.96   0.265                                
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

## How to interpret ANOVA test results?
**The Pr(>F)  = <0.0000000000000002** is less than the alpha value. That means we reject the null hypothesis stating that the average sepal length of three different flower species is not the same.


{{< hint info >}}
As we have rejected the null hypothesis, we now run a post-AdHoc test
{{< /hint >}}

## Running post-Adhoc test
As part of the post-Adhoc test, We are running the Tukey test.

```
TukeyHSD(result) # pass the model output
```
```
# Output
  Tukey multiple comparisons of means
    95% family-wise confidence level

Fit: aov(formula = Sepal.Length ~ Species, data = iris)

$`Species`
                      diff       lwr       upr    p adj
versicolor-setosa    0.930 0.6862273 1.1737727     0
virginica-setosa     1.582 1.3382273 1.8257727     0
virginica-versicolor 0.652 0.4082273 0.8957727     0
```
Each line of output in the above table can be thought of as an individual independent test run for each pair. The p-value for which is represented by **p adj**. Comparing the padj value against the alpha value, we conclude that mean of all the three flowers is different.

# Perform Chi-Square test
the Chi-sqaure test uses a contingency table to test if the two categorical variables are dependent on each other or not. To run the test, you first need to create a contingency table between the two categorical variables. This table is then passed to the `chisq.test()` function.

For example - Let us check if the treatment and type are dependent on each other in the CO2 dataset.

Here, the null hypothesis is that they are not dependent<br/>
And, the alternative is that they are dependent on each other

```
# Creating contigency table
tab <- table(CO2$Type, CO2$Treatment)
# Running chi-square test
chisq.test(tab)
```
```
# Output
    Pearson's Chi-squared test

data:  tab
X-squared = 0, df = 1, p-value = 1
```

### How to interpret chi-square test results?
We again look for the p-value and compare that with the present alpha value of 0.05. In this case, the p-value is greater than alpha, and thus we accept the null hypothesis. So the conclusion is that the plant and treatment are not dependent on each other.

# Wilcoxon Signed Rank Test
The Wilcoxon Signed Rank test is a nonparametric test. It is an alternative of one sample t-test when the data is not assumed to follow a normal distribution.

```
data <- c(30, 29, 29, 28, 26, 23, 28, 25, 24, 19)
wilcox.test(data, mu=35, conf.int = TRUE)
```
```
# Output
    Wilcoxon signed rank test with continuity
    correction

data:  data
V = 0, p-value = 0.005857
alternative hypothesis: true location is not equal to 35
95 percent confidence interval:
 23.50001 28.99999
sample estimates:
(pseudo)median
      26.49994
```

#  Shapiro Test
We use the Shapiro test to check if the data follows normal distribution or not. In the Shapiro test, the null hypothesis is that the data has a normal distribution, and the alternative hypothesis is that data does not follow a normal distribution.

```
set.seed(123)
data <- rnorm(50, mean = 30, sd = 2)
shapiro.test(data)
```
```
# Output
Shapiro-Wilk normality test

data:  data
W = 0.98928, p-value = 0.9279
```

As p-value > 0.05, we accept the null hypothesis, which states that the data is normally distributed. We can confirm that result are correct as we used `rnorm` function to generate random numbers that follow a normal distribution.

# Kolmogorov And Smirnov Test
The test is also very famous by the name k-s test. The test is done to check whether two data sets follow the same distribution or not. Here, the null hypothesis is that the distribution of the two samples is the same, and the alternative hypothesis is that the distributions are different.

```
# Generating random numbers
x <- rnorm(100)
y <- runif(100)
# Performaing k-s test
ks.test(x, y)
```
```
# Output
Two-sample Kolmogorov-Smirnov test

data:  x and y
D = 0.54, p-value = 0.0000000000004335
alternative hypothesis: two-sided
```

The output above suggests that the distribution of x and y is different as p-value < 0.05, and thus we reject the null hypothesis.

In this chapter, we looked into different types of statistical tests. We learned when to use them, how to use them, how to interpret results, which R functions to use to run a particular test. In the next chapter, we will learn how to identify and treat missing values using R programming.
