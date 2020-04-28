---
title: "Standardization And Scaling"
weight: 7
---

> While building a Machine Learning model, do not throw away all of your information! Normalize your features.

# Overview
It is a general requirement for many machine learning algorithms to have features with similar scales. It is important because if we pass features with different scales to algorithms like SVMs, perceptron’s, neural networks, optimization algorithms in logistic regressions, and more prominently, you can think of algorithms which use gradient descent will end up having a faster update for some feature values as compared to others. This is because the feature values play an important role in weight updates.

<figure>
  <img src="/images/Processing/formula.jpg" alt="Formula Gradient Descent">
</figure>

Below is the list of algorithms which get highly impacted by the differing scale of features:

1. K-Nearest Neighbours and K-Means algorithm which uses Euclidean distance as a measure

2. If you are using gradient descent-based optimization in algorithms like logistics regression, neural network, SVM, perceptron etc. we should bring the features on the same scale else some weights will get updated faster when compared to others.

3. In the case of principal component analysis, kernel principal component analysis and linear discriminant analysis as these algorithms intent on finding the direction of maximum variance; you must pass features with similar scales otherwise the algorithm will put much more emphasis on features with the higher measurement as compared to ones with smaller scales.

When we wish to bring the features on the same scale, we can broadly do the following:

1. **Standardization also sometimes called Z-score normalization** - When you apply this technique, the features are scaled in such a way that they end up having properties of a standard normal distribution with mean equal to zero and standard deviation of one. We simply calculate the Z-score of each observation in the dataset for the feature.

*A Z-Score is calculated using the following formula*:

<p style="text-align:center">
{{< katex >}}
\Zeta = (x- \mu)/\sigma
{{< /katex >}}
</p>

2. **Min-Max scaling also sometimes refers to Normalization** - Often, people confuse the Min-Max scaling with the Z-Score Normalization. In this approach, the data is scaled in such a way that the values usually range between 0 – 1. In contrast to the standardization, the min-max scaling results into smaller standard deviations. What this essentially means is that we will be suppressing the effects of outliers.  

*A min-max scaling is typically done using the following formula*:

<figure>
  <img src="/images/Processing/MinMaxScaling.jpg" alt="Formula for min-max scaling">
</figure>

# Which is better Z-score standardization or Min-Max scaling?
Both Standardization and Min-Max scaling have there pros and cons. I am listing these below, and hopefully, this will help you make a call on when to use what.

**Min-max Scaling** - All features end up having the same scale, but it does not handle outliers well.

**Z-score Standardization** - Handles outliers, but the data produced does not have the exact same scale.

# How it can be done using R programming

The below code we are creating a UDF for **min-max scaling**. We have created a sored list of numeric values that are randomly generated from the exponential distribution. We then apply our UDF, which is named as `MinMaxScaling`. You will notice that the first value is zero, and the last value is one as we squeeze the values between 1 and 0.

```
# generating random numbers
num = sort(rexp(10))

# Min-Max Scaling function
MinMaxScaling <- function(x){
  return((x-min(x))/(max(x)-min(x)))
  }

# Calling the function
MinMaxScaling(num)
```

```
# Output
 [1] 0.00000000 0.07356108 0.08615292 0.12786590
 [5] 0.22440422 0.31220196 0.41876790 0.52747519
 [9] 0.55234810 1.00000000
```

For the **Z-score standardization**, you can use the `scale()` from the base R package. The function is very easy to use. And let's just apply the same on the above numerical values. For your reference, we are sharing the minimum, maximum, mean, and standard deviation for the scaled values.

```
# Usinf scale function
scaledValues <- scale(num)
```

```
# Output
> scaledValues
             [,1]
 [1,] -1.09310641
 [2,] -0.85110980
 [3,] -0.80968594
 [4,] -0.67246119
 [5,] -0.35487550
 [6,] -0.06604399
 [7,]  0.28452994
 [8,]  0.64214835
 [9,]  0.72397370
[10,]  2.19663083
attr(,"scaled:center")
[1] 0.6872931
attr(,"scaled:scale")
[1] 0.5836444

> min(scaledValues)
[1] -1.093106

> max(scaledValues)
[1] 2.196631

> round(mean(scaledValues),0)
[1] 0

> round(sd(scaledValues),0)
[1] 1
```

In this chapter, you learned about different techniques of normalizing your data. When using machine learning algorithms, it is imperative that you bring your variables on the same scale. You might have an amazing dataset with many significant features, but if you do not normalize your features, one of those features might completely dominate the others.
