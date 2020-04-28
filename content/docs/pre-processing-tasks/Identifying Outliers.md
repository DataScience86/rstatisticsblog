---
title: "Analysing Outliers"
weight: 1
---

> **Outlier** is a value that does not follow the usual norms of the data. For almost all the statistical methods, outliers present a particular challenge, and so it becomes crucial to identify and treat them. Let's see which all packages and functions can be used in R to deal with outliers.

# Overview
The presence of outliers in the dataset can be a result of an error, or it can be a real value present in the data as a result of the actual distribution of the data. In either case, it is the responsibility of the analyst to identify and treat outlier values.

# Using tukey formula to identify outlier
The tukey formula uses quantiles to produce upper and lower range values beyond which all values are considered as outliers.

Outlier on the upper side = 3rd Quartile + 1.5 * IQR<br/>
Outlier on the lower side = 1st Quartile – 1.5 * IQR

The same formula is also used in a boxplot. The whiskers on either side represent the upper and lower threshold values.

# Using histograms to identify outliers
In a histogram, values beyond ± 2 standard deviations can be tossed as an outlier. However, this is not a rigid rule, and one can modify this on a need basis.

```
# Building a histogram
hist(iris$Sepal.Width,
     col = "blue",
     main = "Histogram",
     xlab = "Sepal width of flowers")
```

<figure>
  <img src="/images/outlier/hist1.jpeg" alt="Histogram to identify outliers">
</figure>

# Using boxplot to identify outliers

```
# Building a box plot
library(ggplot2)
ggplot(iris, aes(x = "", y=Sepal.Width)) +
  geom_boxplot(outlier.colour="red",
             outlier.shape=16,
             outlier.size=2, notch=FALSE)
```

<figure>
  <img src="/images/outlier/boxplot1.jpeg" alt="Boxplot to identify outliers">
</figure>

In the above plot, red dots represent the outlier values. We can also take a deeper plunge by looking at the boxplot by flower type. This should help us explore and understand if missing values are coming from a particular group or not.

```
# Building a box plot
library(ggplot2)
ggplot(iris, aes(x = Species, y=Sepal.Width)) +
  geom_boxplot(outlier.colour="red",
             outlier.shape=16,
             outlier.size=2, notch=FALSE)
```

<figure>
  <img src="/images/outlier/boxplot2.jpeg" alt="Boxplot by group variable">
</figure>

There are flowers in Setosa and Virginia categories that have an unusual sepal width. However, Versicolor species of flowers have no outlier.

# Using cook's distance to identify outliers
**Cooks Distance** is a multivariate method that is used to identify outliers while running a regression analysis. The algorithm tries to capture information about the predictor variables through a distance measure, which is a combination of leverage and each value in the dataset.

A higher value of cooks distance indicates that the values are outliers. Following rules are used to make a decision using cooks distance.

* A value is considered an outlier if it is three times the mean value.
* Another rule states that all values which are higher than 4/n (n is the total number of observations) must be investigated.

```
# Detecting outliers in cars dataset
mod <- glm(mpg ~ ., data = mtcars)
mtcars$cooksd <- cooks.distance(mod)

# Defining outliers based on 4/n criteria
mtcars$outlier <- ifelse(mtcars$cooksd < 4/nrow(mtcars), "keep","delete")

# Inspecting the dataset
mtcars[5:12, ]
```
```
#Output
                  mpg cyl  disp  hp drat   wt  qsec vs am gear carb        cooksd outlier
Hornet Sportabout 18.7   8 360.0 175 3.15 3.44 17.02  0  0    3    2 0.00408313839    keep
Valiant           18.1   6 225.0 105 2.76 3.46 20.22  1  0    3    1 0.03697080496    keep
Duster 360        14.3   8 360.0 245 3.21 3.57 15.84  0  0    3    4 0.00006907413    keep
Merc 240D         24.4   4 146.7  62 3.69 3.19 20.00  1  0    4    2 0.03454201049    keep
Merc 230          22.8   4 140.8  95 3.92 3.15 22.90  1  0    4    2 0.37922064047  delete
Merc 280          19.2   6 167.6 123 3.92 3.44 18.30  1  0    4    4 0.00428238516    keep
Merc 280C         17.8   6 167.6 123 3.92 3.44 18.90  1  0    4    4 0.02404481831    keep
Merc 450SE        16.4   8 275.8 180 3.07 4.07 17.40  0  0    3    3 0.04013728968    keep
```
**Merc 230 is detected as an outlier by cooks distance**

# Using DBSCAN - A machine learning approach
**DBSCAN** is a method that uses a clustering algorithm to separate dense areas with sparse areas. DBSCAN identifies collective outliers, and thus one should ensure that not more than 5% of values are chosen to be recognized as outliers while running the algorithm.

```
library(fpc)
# Compute DBSCAN using fpc package
set.seed(86)
db <- fpc::dbscan(iris[, -c(5,4,3)], eps = 0.20, MinPts = 3)
# Plot DBSCAN results
plot(db, iris[, -c(5,4,3)], main = "DBSCAN", frame = FALSE)
```

<figure>
  <img src="/images/outlier/dbscan1.jpeg" alt="Scatter plot showcasing output of DBSCAN">
</figure>

## Optimal value of **eps**
**eps** is the maximum distance between two points, and based upon this distance; the algorithm decides whether a point is an outlier or not. So it becomes essential to identify the optimal eps value in order to make the correct decision. The below code dictated how we can identify optimal value for eps.

```
library(dbscan)
dbscan::kNNdistplot(iris[, -c(5,4,3)], k =  4)
abline(h = 0.15, lty = 2)
```
**The point where you see an elbow like bend corresponds to the optimal eps value or where the dotted line crosses the solid line is considered as optimal.**


<figure>
  <img src="/images/outlier/dbscan2.jpeg" alt="line plot showcasing optimal eps value for DBSCAN">
</figure>


# Possible actions towards outliers
The possible actions which we can take outliers are mentioned below:

**1. Capping And flooring** - We cap every value that is greater or lesser than the tukey formula by the value returned by the tukey method. When you seal the value on the higher side, we call it as capping. The same action for the lower side values is called as flooring.<br/>
**2. Delete Outliers** - Another solution is to delete all the values which are unusual and do not represent the major chunk of the data.<br/>
**3. Model Outliers** - In cases where outliers are a significant percentage of total data, you are advised to separate all the outliers and build a different model for these values.

In this chapter, we learned different statistical algorithms and methods which can be used to identify the outliers. These methods included univariate and multivariate techniques. We also learned what possible actions could a data scientist take in case data has outliers. In the next chapter, we will learn how to train linear regression models and validate the same before using it for scoring in R.
