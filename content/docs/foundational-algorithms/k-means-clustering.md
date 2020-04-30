---
title: "k-Mean Clustering"
weight: 6
---

# What is k-means clustering
**Clustering** is one of the most popular and widespread unsupervised machine learning method used for data analysis and mining patterns. At its core clustering is grouping similar observations based upon the characteristics of each observations. There are multiple approaches for generating clusters of similar objects. However, in this section, you will learn how to build clusters based on **k-Means** algorithm.

# How does k means algorithm works
In simple words, k-means clustering is a technique which aims to divide the data into **k** number of clusters. The technique is fairly simple and principal idea is to define k centers, one representing each cluster. Below is the pseudo code explaining working of algorithm:

1. Randomly place k points (also known as centroids) in data space. Placement of these points can severely impact the final results. Taking this into consideration the better choice is to place these points as far as possible.
2. The next step is to calculate distance between each point and the k centers.
3. Calculate the distance between each point and centroid. Based on shortest distance each point is associated to a particular centroid. Now that we have points assigned to a centroid, you can think of this as early groups.
4. At this point, we re-calculate new centroid as recalibrated centers based on the points with in the group.
5. Now the steps from 2 to 4 are repeated in a loop. At each step, the centroid changes its position step by step and the algorithm stops when centers do not move any further

## Animated illustration of how the clustering algorithm works

# What is the objective function
The object of the k-means clustering is to minimize the total variance within the clusters and have high variance between the clusters. The total intra cluster variance objective function which is also know as squared error function is calculated using the following formula:

<figure>
  <img src="/images/kmeans/fig1.PNG" alt= "k-means squared function error formula">
</figure>

# How to select the initial value of K in k-Means
The initial value of k can be selected using the below mentioned rule of thumb. If you have N number of observation in your dataset then the initial value of K can be selected using:

&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{{< katex >}}
K =  \sqrt{2/N}
{{< /katex >}}

# How to select optimal number of clusters in k-Means
To choose the optimal number of clusters we can use **elbow method**. The method comprises of plotting a graph explaining percentage of variance by each cluster. The expectation is that with-in group heterogeneity decreases as the number of clusters increases. The implementation of method involves fitting the k-means model for different values of K. The part of the chart which resembles an "elbow" is a good indication of best model.

<figure>
  <img src="/images/kmeans/elbow.PNG"alt= "An example of elbow plot">
</figure>

# Advantages of k-means clustering

1. K- means clustering is simple to implement.
2. It is relatively fast when compared to hierarchal methods.
3. Algorithm scales to large datasets.
4. Adopts to new examples fairly easily.

# Disadvantages of k-means Clustering

1. Final results of K-means are dependent on the initial values of K. Although this dependency is lower for small values K but as the K increases one may be required to figure-out better ways of initial centroids.
2. K-means algorithm is influenced by outliers. The centroids in k-means can get be dragged towards outlier, or you may get a complete cluster consisting of outliers instead of being ignored. You should remove or clip outliers before running the clustering.
3. The effectiveness of k-means algorithm to distinguish between examples decreases as the number of dimensions increases. It happens because of the decrease in the ratio of standard deviation to the mean of distance between examples.
4. K-means is not good when it comes to cluster data with varying sizes and density. A better choice would be to use gaussian mixture model.
