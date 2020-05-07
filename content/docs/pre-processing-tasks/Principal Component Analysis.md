---
title: "Principal Component Analysis"
date: 2019-12-28
weight: 8
---


> Principal component analysis(PCA) is an unsupervised machine learning technique that is used to reduce the dimensions of a large multi-dimensional dataset without losing much of the information. It is often also used to visualize and explore these high dimensional datasets.

# Overview
One of the challenges among others that large datasets present is the time to model or learn the relationship between independent variables and the target variables. Thus it becomes essential for us to reduce the number of variables that we want to pass into the model to predict the target variable. There are many different statistical or machine learning algorithms like SVD, ICA, tSNE, feature selection etc. that we can use to reduce the dimension in order to speed up the model training process. Among these methods, one of the most talked and widely used algorithms is principal component analysis.  In this article, we will learn how to use principal component analysis and identify the latent features of any dataset to reduce the dimensions of the dataset without losing much of the variance/information.

At a high level, the independent X variables are passed through a function which returns **n** number of principal components. Here n is the total number of variables in the dataset. Each principal component has hidden information of all the independent variables. However, the first principal component explains maximum variability in the data and each following component for the remaining variance as possible. Thus, the Explainability of the variance is maximum with first and lowest with the last principal component.

# When to use Principal Component Analysis
In the following scenario, we can use principal components.

1. When you want to **reduce the dimensionality** of the large dataset.

2. **When you want to transform several correlated variables into some uncorrelated variables.** Sometimes you may find correlated variables within your independent variables. However, you believe that all variables are important and should not be deleted. In such scenarios you can make use of principal components as **principal components are uncorrelated latent variables.**

{{< hint danger >}}
The principal components are less interpretable in comparison to actual variables.
{{< /hint >}}


# Intuitive understanding of Principal Components

In layman terms, through principal components, we are actually trying to figure out the magnitude and direction of data. To get these two components, we can only use straight lines that are orthogonal to each other.  To understand this, let's consider the below example.

1. Imagine that your data points are distributed in the shape of an oval, as shown below. Each dark blue point represents the location of an individual data point in terms of variable x1 and variable x2.

<figure>
  <img src= "/images/pca/fig4.png" alt = "Changing from default theme to other in ggplot2">
</figure>

2. The next step is to draw straight lines, which kind of capture the maximum variance in the data. So, let's draw the straight lines and remember these lines need to be orthogonal to each other. The solid green and red lines are the ones that are orthogonal. You can see that the green line clearly captures the maximum information and thus becomes your principal component 1, and the red line becomes your principal component 2.

<figure>
  <img src="/images/pca/fig2.png" alt = "Principal component analysis example 2">
</figure>

3. Now on each line, imagine the projection of all the data points(represented by dotted lines). These projections on the solid lines are represented by eigenvectors(Intuitively, an eigenvector is a vector whose direction remains unchanged when a linear transformation is applied). So you can see that on both the lines, we actually have some representation of all the data points. The only difference is that the green line captures much more variance of data as compared to the red line.

<figure>
  <img src="/images/pca/fig3.png" alt = "Principal component analysis example 3">
</figure>

4. We now transform our data use PC1 and PC2 as are new axis in place of X1 and X2 variables. Further information can be represented linearly on one axis as given below.

<figure>
  <img src="/images/pca/fig5.png" alt = "Principal component analysis example 5">
</figure>

{{< hint info >}}
You now understand that principal component analysis tries to figure out a new coordinate system such that every data point has a new (x,y) value. Thus, each principal component is nothing but a combination of x1 and x2 variables. These are finally arranged in such a way that the first PC gets to explain lots of variation.
{{< /hint >}}

# 2D Example Visualizing Principal Component coordinates

<figure>
  <img src="/images/pca/fig1.PNG">
</figure>

# Mathematical explanation of principal components

In this section, we will look into the mathematical steps involved in calculating principal components. We will not do manual calculations for each and every step. I feel that it is not required because we have many prebuilt packages which can do these calculations in the possible know efficient ways.

1. Take all the independent variables, ignoring the target variable.
2. Calculate the mean for every variable.
3. Calculate the covariance matrix of the complete dataset.
4. Calculate the eigenvectors and generate the corresponding eigenvalues.
5. Finally, Sort these eigenvectors in decreasing order.
6. Based upon your variance explained by these components, select top k eigenvectors with the most significant values.
6. Use this new matrix consisting of eigenvalues to transform the data into a new space.


# How to run PCA in R
For this example, we are using the USDA National Nutrient Database data set. The dataset has  8619 observations and around 48 variables, including both categorical and numeric variables.

## reading the raw dataset
We will read the dataset into R and keep only independent variables. For this example, we are only considering numeric variables. The dataset is then divided into train and test. As this is just a working example, we are not doing the random sampling.

```
rawdata <- read.csv("nndb_flat.csv")

#Diving the dataset into test and train
pca.train <- rawdata[1:7000, c(8:45)]
pca.test <- rawdata[7001: nrow(rawdata), c(8:45)]
```

## Running Principal component analysis

If the variables in the dataset have widely varying scales, then it is advised to bring all the data points on the same scale. The function I am using provides the functionality of bringing the data values on the same scale. In case you decide to use some other function to ensure that you follow this particular step.  

{{< hint warning >}}
It is important to bring variables on the same scale in case they are on different scales. This can be achieved through [scaling or standardization](../standardization-minmax-scaling) techniques.
{{< /hint >}}

We will be using the `prcomp()` function for generating principal components. This function comes along with the default `{stats}` package in R. For scaling the variables use `scale.` argument to do scaling computation.

```
# Generating principal components
prin_comp <- prcomp(pca.train, scale. = T)
```

## Plotting principal components to understand cumulative variance explained

We can use a simple cumulative chart to shortlist the adequate amount of principal components. Generally, a cumulative variance explained up to 95% is considered a good cut-off. However, this number is not a strict guideline. Based upon the requirement, one can select anywhere between 85% to 99%.

```
# Extrating and calculating the variance of each PC
prin_comp$rotation[1:5,1:4]
std_dev <- prin_comp$sdev
pr_var <- std_dev^2
prop_varex <- pr_var/sum(pr_var)

# Generating Cumulative point chart
plot(cumsum(prop_varex), xlab = "Principal Component",
     ylab = "Cumulative Proportion of Variance Explained",
     type = "b")
```

<figure>
  <img src="/images/pca/figure6.jpeg" alt = "Cumulative graph showing variance explained by principal components">
</figure>

From the above graph, it looks like around 96.4% of the variance is explained by just 18 variables.

```
# Getting the explained by 18 principal components
sum(prop_varex[1:18])

# Output
[1] 0.9639597
```

## Replacing actual variables with principal components

This step is relatively simple; all we need is to concatenate the principal components to the current dataset using the `cbind()` function. The actual variables are dropped, and only the target variable is kept in the final training dataset.

**Note:** *In the below example, we do not have a target variable*. However, I request you to include the target variable into the final pca.train dataset while working on an actual dataset.

```
# selecting principal components from model
pca.train <- cbind(pca.train, prin_comp$x)
pca.train <- as.data.frame(pca.train[, 39:56])

# Examining the first 4 variables and top 6 obs
head(pca.train[0:4])
# Output

         PC1        PC2        PC3        PC4
1  1.76851457 -0.3880891 -0.4975637  1.3199199
2  1.76438516 -0.3953145 -0.5022858  1.3365616
3  1.73022080 -0.3956281 -0.6435845  1.6293108
4 -0.37948652  0.1763231 -2.5346533 -0.5315933
5 -0.40721247  0.2832664 -3.3679989 -0.3626819
6  0.06090458 -0.4432112 -0.7140177 -0.6492139
```

We can now use this new dataset and run either regression or classification machine learning algorithm based upon the nature of the target variable.

# How to generate/predict PCA in the test dataset
You know that the train and test dataset needs to have the same set of variables. If this condition is not met, then your model will produce an error. You can use regular `predict()` function to generate principal components in the test dataset, as well.

```
# Predicting PC in test dataset
test.data <- predict(prin_comp, newdata = pca.test)

# PCA returns a matrix, so converting back to the data frame
test.data <- as.data.frame(test.data[1:18])
```

In this chapter, we learned how to reduce the dimension of a large dataset using principal component analysis. We did by understanding principal components intuitively as well as learning all the mathematical steps involved in generating principal components. Finally, we walked through step by step R programming code and created PC on a USDA National Nutrient Database.
