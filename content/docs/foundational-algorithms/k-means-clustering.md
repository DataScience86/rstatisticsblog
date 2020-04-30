---
title: "k-Mean Clustering"
date: 2020-03-14
weight: 6
---

> **Clustering** is one of the most popular and widespread unsupervised machine learning method used for data analysis and mining patterns. At its core, clustering is the grouping of similar observations based upon the characteristics. There are multiple approaches for generating clusters of similar objects. However, in this section, you will learn how to build groups based on the **k-Means** algorithm.

# What is k-means clustering?
In simple words, k-means clustering is a technique that aims to divide the data into **k** number of clusters. The method is relatively simple. The principal idea is to define k centers, one representing each cluster. Below is the explanation of the working of the algorithm:

1. Randomly place k points (also known as centroids) in data space. The placement of these points can severely impact the final results. Considering that a better choice is to place these points as far as possible.
2. The next step is to calculate the distance between each point and the k centers.
3. Now calculate the distance between each point and centroid. Based on the shortest distance, each point is associated with a particular centroid. Now that we have points assigned to a centroid, you can think of this as early groups.
4. At this point, we re-calculate new centroid as recalibrated centers based on the points within the group.
5. Now the steps from 2 to 4 are repeated in a loop. At each step, the centroid changes its position step by step, and the algorithm stops when centers do not move any further.

# What is the objective function
The object of the k-means clustering is to minimize the total variance within the clusters and have high variance between the clusters. The total intra-cluster variance objective function (which is also known as the squared error function) is calculated using the following formula:

# Animated illustration of how the clustering algorithm works
<video controls="controls" src="/images/kmeans/Cluster Animation.mp4" width="500px" height="300px">
Illustrating k-means algorithm
</video>

# Advantages of k-means clustering

1. K- means clustering is simple to implement.
2. It is relatively fast when compared to hierarchal methods.
3. Algorithm scales to large datasets.
4. The algorithm adapts to new examples reasonably easily.

# Disadvantages of k-means Clustering

1. The final results of K-means are dependent on the initial values of K. Although this dependency is lower for small values of K, however, as the K increases, one may be required to figure out a better way of initializing centroids.
2. K-means algorithm is influenced by outliers. The centroids in k-means can be dragged towards outliers, or you may get a complete cluster consisting of outliers instead of being ignored. To avoid that, you should remove or clip outliers before running the clustering.
3. The effectiveness of the k-means algorithm to distinguish between examples decreases as the number of dimensions increases. It happens because of the decrease in the ratio of the standard deviation to the mean distance between examples.
4. K-means is not good when it comes to cluster data with varying sizes and density. A better choice would be to use a gaussian mixture model.

# k-means clustering in R
You can use `kmeans()` function to compute the clusters in R. The function returns a list containing different components. Here we are creating 3 clusters on the wine dataset. The data set is readily available in `rattle.data` package in R. For the illustration purpose, we are using only a few columns. The columns are on different scales, so we are also using `scale()` function to bring them on the same scales.

The `kmeans()` function can take multiple arguments. For example, `centers = 3` or `k = 3` will group the data into 3 different groups. You can mention the maximum number of iterations for which algorithm should run before generating the final solution using `iter.max =`. One of the recommended argument is `nstart=`. In our case, we have mentioned `nstart = 25`, which means the algorithm will attempt 25 initial configurations before reporting the best one.

```
library(rattle.data)
data(wine, package="rattle.data")
wine_subset <- scale(wine[ , c(2:4)])

wine_cluster <- kmeans(wine_subset, centers = 3,
                       iter.max = 10,
                       nstart = 25)
```

# Understanding k-means clustering output

The output of k-means is an output of class **kmeans**.
```
> wine_cluster
K-means clustering with 3 clusters of sizes 60, 48, 70

Cluster means:
     Alcohol      Malic        Ash
1  0.8914655 -0.4522073  0.5406223
2  0.1470536  1.3907328  0.2534220
3 -0.8649501 -0.5660390 -0.6371656

Clustering vector:
  [1] 1 3 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 1 2 1 1 1 1 1 3 1 1 1 1 1
 [34] 1 1 1 1 1 3 2 1 2 1 2 3 2 2 1 1 1 3 1 1 1 1 1 1 1 1 3 3 3 3 3 3 3
 [67] 3 3 1 3 3 1 1 1 3 3 3 3 3 2 3 3 3 2 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3
[100] 3 3 3 3 3 3 3 3 3 3 3 3 3 2 3 3 3 3 3 2 3 3 1 2 2 2 3 3 3 3 2 3 2
[133] 3 2 3 3 2 2 2 2 2 1 2 2 2 2 2 2 2 2 2 2 1 2 3 2 2 2 1 1 2 2 2 2 1
[166] 2 2 2 1 2 3 3 1 2 2 2 1 2

Within cluster sum of squares by cluster:
[1]  67.98619  73.71460 111.63512
 (between_SS / total_SS =  52.3 %)

Available components:

[1] "cluster"      "centers"      "totss"        "withinss"    
[5] "tot.withinss" "betweenss"    "size"         "iter"        
[9] "ifault"    
```

**Printing the cluster object** - We see that as expected, we have 3 clusters comprising of 60, 48, and 70 number of observations. You can also see that we have the average value of three groups by each variable. `Cluster vector` provides assignment information at the observation level. Here one observation is assigned to group 1, whereas 2nd observation is assigned to the 3rd group and so on. The next output is self-explanatory. It provides within cluster sum of squares value. In the end, we have a list consisting of multiple components. Some of them are explained below.

* `cluster`    - A vector of integers (from 1:k) indicating the cluster to which each point is allocated.
* `centers` - A matrix of cluster centres.
* `totss`    - The total sum of squares.
* `withinss` - Vector of within-cluster sum of squares, one component per cluster.
* `tot.withinss` - Total within-cluster sum of squares, i.e. sum(withinss).
* `betweenss`    - The between-cluster sum of squares, i.e. totss-tot.withinss.
* `size` - The number of points in each cluster.
* `iter` - The number of (outer) iterations.

# Visualizing the output of k-means clusters in R
To visualize the output of the three clusters, we will use `fviz_cluster()` from the `factoextra` package. The function not just provides a nice visualization but also converts the input information to PCA(principal components) if there are more than two variables. Once we have the principal components, a graph is generated between the first two components showing how much variance is explained by each component.

```
library(factoextra)
fviz_cluster(wine_cluster, data = wine_subset)
```

<figure>
  <img src="/images/kmeans/fig2.PNG" alt= "Visualizing clusters using fviz_cluster function">
</figure>

Another useful way of visualizing clusters is to illustrate groups by the original variable. Here we will use `with()` and `pair()` function to generate a pairwise plot showing each group by color.

<figure>
  <img src="/images/kmeans/fig3.PNG" alt= "Visualizing clusters by original variables">
</figure>

# How to select the initial value of K in k-Means
The initial value of k can be selected using the below mentioned rule of thumb. If you have N number of observation in your dataset then the initial value of K can be selected using:

&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{{< katex >}}
K =  \sqrt{2/N}
{{< /katex >}}

# How to select an optimal number of clusters in k-Means
To choose the optimal number of clusters, we can use **elbow method**. The method comprises of plotting a graph explaining the percentage of variance by each cluster. The expectation is that with-in group heterogeneity decreases as the number of clusters increases. The implementation of the method involves fitting k-means for different values of K. The part of the chart, which resembles an "elbow" is a good indication of the best model.

<figure>
  <img src="/images/kmeans/elbow.PNG" alt= "An example of elbow plot">
</figure>

To generate the elbow plot, we are utilizing a user-defined function. I took this function from the stack overflow along time back.

```
# Function to compute total within-cluster sum of square
wssplot <- function(data, nc=15, seed=1234){
  wss <- (nrow(data)-1)*sum(apply(data,2,var))
  for (i in 2:nc){
    set.seed(seed)
    wss[i] <- sum(kmeans(data, centers=i)$withinss)}
  plot(1:nc, wss, type="b", xlab="Number of Clusters",
       ylab="Within groups sum of squares")
}

# plotting values for each cluster starting from 1 to 9
wssplot(wine_subset, nc = 9)
```

<figure>
  <img src="/images/kmeans/fig4.PNG" alt= "An example of elbow plot for wine data">
</figure>
