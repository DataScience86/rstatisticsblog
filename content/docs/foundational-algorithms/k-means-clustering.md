---
title: "k-Mean Clustering"
weight: 6
---

> **Clustering** is one of the most popular and widespread unsupervised machine learning method used for data analysis and mining patterns. At its core clustering is grouping similar observations based upon the characteristics of each observations. There are multiple approaches for generating clusters of similar objects. However, in this section, you will learn how to build clusters based on **k-Means** algorithm.

## What is k-means clustering?
In simple words, k-means clustering is a technique which aims to divide the data into **k** number of clusters. The technique is fairly simple and principal idea is to define k centers, one representing each cluster. Below is the pseudo code explaining working of algorithm:

1. Randomly place k points (also known as centroids) in data space. Placement of these points can severely impact the final results. Taking this into consideration the better choice is to place these points as far as possible.
2. The next step is to calculate distance between each point and the k centers.
3. Calculate the distance between each point and centroid. Based on shortest distance each point is assoicated to a particular centroid. Now that we have points assigned to a centroid, you can think of this as early groups.
4. At this point, we re-calculate new centroid as recalibrated centers based on the points with in the group.
5. Now the steps from 2 to 4 are repeated in a loop. At each step, the centroid changes its position step by step and the algorithm stops when centers do not move any further

## What is the objective function
The object of the k-means clustering is to minimize the total variance within the clusters and have high variance between the clusters. The total intra cluster variance objective function which is also know as squared error function is calculated using the following formula:

<center>![Squared error function](/images/k-means/fig1.png "k-means squared function error formula")</center>


## Limitations of k-means clustering
