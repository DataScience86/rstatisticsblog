+++
title = "7 Useful Interactive Charts in R (with sample code)"
description = ""
tags = [
    "network graph",
    "scatter plot",
    "bubble plot",
    "area plot",
    "collapsible tree",
    "dendrogram",
    "timeseries plot",
    "interactive charts"
]
date = "2020-05-02"
categories = [
    "R",
    "Visualization",
]
menu = "main"
+++


# Introduction

An interactive chart gives a lot of freedom to both presenter and the audience. Interactive graphs allow you to share complex ideas in a more engaging way. Using interactive charts allows users to perform actions like choosing a variable, zoom-in, zoom-out, hover to get tooltip, take a snapshot, and more. In this section, I will walk you through some of the packages and functions which we can use to plot different types of interactive plots.

# Interactive scatter plot
The best way to build an interactive scatter plot from `plotly` in R is through the use of `plot_ly` function. Here we are using `iris` data for creating a scatter plot between `Sepal.Length` and `Petal.width` variables.

```
library(plotly)
fig <- plot_ly(data = iris, x = ~Sepal.Length, y = ~Petal.Length)
fig
 ```
<iframe  src="/images/post/interactive-charts/scatter_plot.html" height="400" width="600" style="border:2px solid grey;">
</iframe>
<div w3-include-html="/images/post/interactive-charts/scatter_plot.html"></div>


# Interactive area plot
An area chart is very close to a line plot. We can build an interactive area plot in `plotly` using two different functions, `plot_ly()` and `ggplotyly()`. Here we will build an area chart showing the density of `AirPassengers` data using `plot_ly()` function.

```
density <- density(AirPassengers)
fig <- plot_ly(x = ~density$x, y = ~density$y ,
               type = 'scatter', mode = 'lines', fill = "tozeroy)
fig
```

<iframe src="/images/post/interactive-charts/area_plotly.html" height="400" width="600" style="border:2px solid grey;">
</iframe>

Once again, we will draw an interactive area chart using the `ggplotly()` function from the `plotly` package. The `ggplotly()` function is a special one as it turns a `ggplot2` design version interactive. So if you have a `ggplot2` graph already created, the `ggplotly()` can be very handy. Now, we will first create a static area chart using `ggplot` function and then will add interactive features to it using `ggplotly()`.

```
library(plotly)
library(ggplot2)
fig <- ggplot(diamonds, aes(x=carat)) +
  geom_area(stat = "bin") +
  theme_minimal()

# Turn figure interactive with ggplotly
fig <- ggplotly(fig)
fig
```
<iframe src="/images/post/interactive-charts/area_ggplotly.html" height="400" width="600" style="border:2px solid grey;">
</iframe>

# Interactive bubble plot
Again, the easiest way to draw an interactive bubble plot would be first to use `geom_point()` function from `ggplot2` package to draw the chart and then render the graph to get the interactive version of it.

```
library(ggplot2)
library(plotly)

mtcars$cyl <- as.factor(mtcars$cyl)
fig  <- ggplot(mtcars ,aes(mpg, wt, size = disp, color=cyl)) +
  geom_point() +
  theme_bw()

fig <- ggplotly(fig)
fig
```
<iframe src="/images/post/interactive-charts/bubble.html" height="400" width="600" style="border:2px solid grey;">
</iframe>


# Interactive heatmaps
Heatmaps are a great visualization tool to show the magnitude of information. The graph uses colors to depict the information stored in a matrix. Here we will see how we can use `plotly` and `d3heatmap` package to generate heatmaps. The `d3heatmap` package creates a **D3.js-based heatmap widget.**

```
library(d3heatmap)
fig <- d3heatmap(mtcars, scale = "column", colors = "Greens")
fig
```
<iframe src="/images/post/interactive-charts/d3heatmap.html" height="400" width="600" style="border:2px solid grey;">
</iframe>

To generate heatmap with `plotly` is simple. Generate heatmap using `ggplot2` and render that to the interactive version using `ggplotly` function. Here we first generate the correlation matrix using `cor` function. We then use the `melt()` function from the `reshape2` package to bring the correlation matrix into the desired format. It's a little tricky, but worth the shot.

```
library(ggplot)
library(reshape2)
matrix <- cor(mtcars)
melted_cor_mat <- melt(matrix)


fig <- ggplot(melted_cor_mat,
              aes(x=Var1, y=Var2, fill=value))+
  geom_tile()+
  theme_minimal()

fig <- ggplotly(fig + theme(legend.position = "none"))

fig
```

<iframe src="/images/post/interactive-charts/ggplot_heatmap.html" height="400" width="600" style="border:2px solid grey;">
</iframe>

# Interactive dendrogram with collapsible tree
A collapsible dendrogram is an interactive tree-like chart where when you can click on a node to either reveal the following branch or collapse the current node. Click the button to see an example.  

{{< button href="https://bl.ocks.org/d3noob/43a860bc0024792f8803bba8ca0d5ecd" >}}Interactive Dendrogram Example{{< /button >}}

You can use the `collapsibleTree` package to generate hierarchical visualization from a data frame object without worrying about the nested lists or JSON objects. Here we will use geography data from data.world.

{{< button href="https://data.world/glx/geography-table" >}}Download{{< /button >}}

```
library("collapsibleTree")

geo <- read.csv("M:/DSB/datasets/Machine-Learning/Geography.csv")
dendo  <- collapsibleTree(
    geo,
    hierarchy = c("region", "sub_region"),
    width = 700,
    zoomable = TRUE
  )
dendo
```

<iframe src="/images/post/interactive-charts/dendo_collapsible.html" height="500" width="700" style="border:2px solid grey;">
</iframe>

# Interactive time series charts
I find interactive time series charts of particular interest. The Time series graphs provide information about the evolution of one or multiple variables through time. Here we will use the `dygraphs` package to generate impressive time series charts.

```
library(dygraphs)
chart <- dygraph(AirPassengers)
chart
```

**Encourage you to select a section of the chart and zoom in**

<iframe src="/images/post/interactive-charts/timeseries.html" height="500" width="700" style="border:2px solid grey;">
</iframe>

# Interactive network
Building a network graph is kind of complicated. The primary reason for complexity comes from the fact that there can be many different input formats.  There are also other parameters that you should be aware of, like whether the network you are trying to build is weighted or unweighted, and if it is directed or undirected. We will have a dedicated article on the input formats and talk more about it. Here we will use one of the rather simple forms of data where we have just two columns - Source and Target. Now to build the network graph, we will use the `simpleNetwork()` function from the `networkD3` package in R.

```
library(networkD3)
network <- read.csv("M:/DSB/datasets/Machine-Learning/network_dummy.csv")

# create the network object
# Plot
p <- simpleNetwork(network, height="200px", width="200px",        
                   Source = 1,
                   Target = 2,
                   fontSize = 18,                    
                   linkColour = "#777",   
                   nodeColour = "#F47E5E",    
                   opacity = 0.7,             
                   zoom = T)
```

<p align="center">
<iframe src="/images/post/interactive-charts/networkGraph.html" height="300" width="400" style="border:2px solid grey;">
</iframe>
</p>

We hope you learned something new with this article. If so, please leave comment, like or share. You can also let us know what topics you would like us to write and share.
