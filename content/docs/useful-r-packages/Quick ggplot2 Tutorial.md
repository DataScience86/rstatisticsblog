---
title: "Tutorial ggplot2 Package"
date: 2020-04-15
weight: 1
tags: ["ggplot2", "R Code"]
category: ["R", "Tutorial", "Visualization"]
---

# Overview
The `ggplot2` package in R provides a reliable system for describing and building graphs. The package is capable of creating elegant and aesthetically pleasing graphics. The framework of ggplot2 is quite different (in comparison to graphics package) and is based on the grammar of graphics(introduced initially by Leland Wilkinson). At first, you may not find it intuitive, but don't worry, we are here to help. **Together, we will master it to the core**.

# Basic plotting framework for ggplot
```
ggplot(data = dataset name) +
  <GEOM_FUNCTION>(mapping = aes(variable name))
```

# Mapping the aesthetics(using aes)
The aesthetic represents the object which you wish to plot in your graph. In other words, aesthetics represent different ways in which you can plot your data points. To showcase the data points, you can change things like size, shape, or color of the points. Thus by using aesthetics (represented by `aes()`) you can convey the information which is hidden in your dataset.

For example, you can map color to **cylinder** variable to reveal the relationship between **mileage** and **weight**.  So let us take our framework and add aesthetics to it. Here we have three variables, and that means we have to pass three arguments to the `aes()` function.

```
# Loading the library
library(ggplot2)

# loading data and converting cyl variable to factor
data(mtcars)
mtcars$cyl <- as.factor(mtcars$cyl)
# Adding aesthetics
ggplot(data = mtcars) +
  geom_point(mapping = aes(x = mpg, y = wt))
```

<figure>
  <img src="/images/ggplot/aes.jpeg" alt="An example of asthetics">
</figure>

# Mapping Geometric shapes(using geom)
The geometric shapes in ggplot are visual objects which you can use to describe your data. For example, one can plot **histogram** or **boxplot** to describe the distribution of a variable.

Below mentioned two plots provide the same information but through different visual objects. These objects are defined in ggplot using **geom**. That means you can use geom to define your plot. For example, the histogram uses histogram geom, barplot uses bar geom, line plot uses line geom, and so on. There is one exception. We use point geom to plot the scatter plots.

Let's see how we can draw the charts, which we mentioned in the above example using **geoms** for the total sleep hours of animals.

{{< hint warning>}}
Every geom function requires you to map an aesthetic to it. However, not every aesthetic requires a geom. For example, one can **set the shape of a point**, but you **cannot** set the shape of a **line**.
{{< /hint >}}

## Building histogram

```
# Building a histogram
ggplot(data = msleep) +
  geom_histogram(mapping = aes(x = sleep_total, col = "orange"))
```

<figure>
  <img src="/images/ggplot/geom1.jpeg" alt="An example of histogram geom">
</figure>

## Building boxplot

```
# Building a histogram
ggplot(data = msleep) +
  geom_boxplot(mapping = aes(y = sleep_total))
```

<figure>
  <img src="/images/ggplot/geom2.jpeg" alt="An example of boxplot using geom">
</figure>


# Using Facets in ggplot2
Facet is a way in which you can add additional categorical variables to your plot. The facet helps in building the chart by dividing the data into two or more groups. The data from these groups are used for plotting the data.

Now there are two ways in which you can use facets:

<strong>A.</strong> If you want to split the data by only one variable, then use `facet_wrap()` function. In the following syntax, you will notice **tilder(~)**. By default, this is the first argument. After this, you should mention the variable name by which you want to do the split.

Let's check the distribution of total sleep by kind of animal.

```
# Working example of facet_wrap
ggplot(data = msleep) +
  geom_histogram(mapping = aes(x = sleep_total)) +
  facet_wrap(~ vore)
```

<figure>
  <img src="/images/ggplot/facet_wrap.jpeg" alt="An example of facet_wrap from ggplot2">
</figure>


<strong>B.</strong> If you want to split the data by a combination of two variables, then you can use `facet_grid()`. Here the two variables should be separated by the **tilder(~)**.

Building the scatter plot between mpg and disp variable by cyl and am type.

```
# loading data
data(mtcars)
# Converting cylinder(cyl) and automatic(am) variable to factor variables.
mtcars$cyl <- as.factor(mtcars$cyl)
mtcars$am <- as.factor(mtcars$am)

# Working example of facet_grid
ggplot(data = mtcars) +
  geom_point(mapping = aes(x = mpg, y = disp)) +
  facet_grid(cyl ~ am)
```

<figure>
  <img src="/images/ggplot/facet_grid.jpeg" alt="An example of using facet_grid function from ggplot2">
</figure>

# Mapping colors to variables in ggplot2

Colors can play a game-changer role in any data visualization, and thus it becomes important for us to learn about it. The default color in ggplot is on the greyscale. But if you want, you can change the color.

In ggplot, there are a couple of ways in which you can use color.

<strong>A.</strong> You can assign the colors to the objects, lines, and points. To color the objects, you can use `fill()` argument.  To set colors to the lines and points, you can use the `color` argument. Below is a quick example of both cases.

## Using `color` argument
```
# Making the points blue color in the scatter plot
ggplot(data = mtcars) +
  geom_point(mapping = aes(x = mpg, y = wt), color = "blue")
```

<figure>
  <img src="/images/ggplot/color1.jpeg" alt="An example of using color argument to assign colors to points in ggplot2">
</figure>

## Using `fill` argument
```
# Making the bars of histogram blue
ggplot(data = iris) +
  geom_histogram(mapping = aes(x = Sepal.Width), fill = "blue")
```

<figure>
  <img src="/images/ggplot/fill.jpeg" alt="An example of using color argument to assign colors to the objects in ggplot2">
</figure>

<strong>B.</strong> We can use color to map the values of the third variable, which we have already learned in the very first example under mapping aesthetics.

{{< hint warning >}} By default the ggplot2 uses **scale_fill_hue()** and **scale_colour_hue()** for color selection. However you can choose to change the luminance of these colors. Also there are other color scales available in R from **RColorBrew** package. {{< /hint >}}

### Example 1 - Showcasing Default RColorBrew setup

```
ggplot(data = mtcars) +
  geom_boxplot(mapping = aes(x = cyl, y = mpg, fill=cyl)) +
  scale_fill_brewer()
```

<figure>
  <img src="/images/ggplot/RBrewColor1.jpeg" alt="An example of using default color palette from RBrewColor Package with ggplot2">
</figure>

### Example 2 - Showcasing Set1 pallette colors
```
ggplot(data = mtcars) +
  geom_boxplot(mapping = aes(x = cyl, y = mpg, fill=cyl)) +
  scale_fill_brewer(palette="Set1")
```

<figure>
  <img src="/images/ggplot/RBrewColor2.jpeg" alt="An example of using color pallette set1 from RBrewColor Package with ggplot2">
</figure>

### Example 3 - Showcasing Spectral palette colors
```
ggplot(data = mtcars) +
  geom_boxplot(mapping = aes(x = cyl, y = mpg, fill=cyl)) +
  scale_fill_brewer(palette="Spectral")
```

<figure>
  <img src="/images/ggplot/RBrewColor3.jpeg" alt="An example of using color pallette Spectral from RBrewColor Package with ggplot2">
</figure>

{{< hint info >}} For Your reference sharing the RBrewColor Pallet chart. {{< /hint >}}

<figure>
  <img src="/images/ggplot/RBrew.png" alt="RBrewColor pallets chart">
</figure>

# Understanding the Coordinate System of ggplot2
The coordinates system of ggplot is a little complicated. But don't worry, we will not dig too much. As of now, we will provide you with some examples of coordinate systems. If you pay attention to these, I think most of the job is done, and you are on your way to creating awesome charts using ggplot2. With time, I am sure you will be able to take deeper plunges into ggplot coordinate system. To start with, I have shortlisted some five functions as given below:

<strong>1.</strong> `coord_cartesian()` - This is the default coordinate system in ggplot2. According to this system the X and Y positions of each point act independently to determine its location on the graph.

<strong>2.</strong> `coord_flip()` - This is helpful in cases when you want to build horizontal graphs. This function switches the X and Y-axis. For example, you can use `coord_flip` to draw horizontal boxplots.

```
ggplot(data = mtcars) +
  geom_boxplot(mapping = aes(x = cyl, y = mpg, fill=cyl)) +
  scale_fill_brewer(palette="Set1") +
  coord_flip()
```

<figure>
  <img src="/images/ggplot/coord_flip.jpeg" alt="A working example of coord_flip function in ggplot2">
</figure>

<strong>3.</strong> `coord_polar()` - This creates a nice combination charts of bar and coxcomb or pie graphs by using polar coordinates.

```
# Loading girbExtra Library
library(gridExtra)
# Generating a barplot
bar <- ggplot(data = mtcars) +
  geom_bar(
    mapping = aes(x = cyl, fill = cyl),
    width = 1
  )

# Saving the two plots
plot1 <- bar + coord_flip()
plot2 <- bar + coord_polar()
# Plotting the graphs by cloumn
grid.arrange(plot1, plot2, ncol = 2)
```

{{< hint info>}} In the above code, we have used a **gridExtra** package. I love this package it makes plotting multiple charts on the same canvas very easy.{{< /hint >}}

<figure>
  <img src="/images/ggplot/polar.jpeg" alt="A working example of coord_polar function in ggplot2">
</figure>

<strong>4.</strong> `coord_map()` - This functions creates a 2D map of the desired earth location. We use `coor_polygon` along with coord_map to a map with maintained aspect ratio. If you do not understand what this means then just run the code once without the coord_map part.
```
# An example showcasing the map of USA
italy <- map_data("italy")
ggplot(italy, aes(long, lat, group = group)) +
   geom_polygon(fill = "lightblue", colour = "black") +
   coord_map()
```

<figure>
  <img src="/images/ggplot/coord_map.jpeg" alt="A working example of coord_map function in ggplot2">
</figure>

<strong>5.</strong> `coord_fixed()` - This coordinate system ensures that the aspect ratio of axes is kept inside the specified range. Check out the below examples:
```
# Building a scatter plot
plot <- ggplot(mtcars, aes(mpg, wt)) + geom_point()

# Setting the ratio to 1
ratio1 <- plot + coord_fixed(ratio = 1)
# Setting ratio to 10
ratio10 <- plot + coord_fixed(ratio = 3)
# plotting then in grid
grid.arrange(ratio1, ratio10, ncol = 2)
```

<figure>
  <img src="/images/ggplot/coord_fixed.jpeg" alt="A working example of coord_fixed function in ggplot2">
</figure>

# Support for statistical transformation in ggplot

Among many useful features of ggplot2, the one which may become dear to you is the support for statistical transformations. These functions save a lot of time as you don't have to prepare the data for it, and the statistical calculations can be done on the go. Again there are multiple statistical functions, and we encourage you to explore them. However, below I have listed some of the most widely used statistical functions.

<strong>1.</strong> `stat_count` - Creates a bar plot showcasing the frequency count of each level of categorical variable.

```
# Plotting the bar chart of cylinder counts
ggplot(data = mtcars) +
  stat_count(mapping = aes(x = cyl))
```

<figure>
  <img src="/images/ggplot/stat_count.jpeg" alt="A working example of Stat_count function in ggplot2">
</figure>

<strong>2.</strong> `stat_density()` - Creates a kernel density plot. Kernel density estimate is a smoothed version of histogram. A very useful alternative for histogram to plot the histogram.

```
# Plotting the bar chart of cylinder counts
ggplot(data = iris) +
  stat_density(mapping = aes(x = Petal.Length))
```

<figure>
  <img src="/images/ggplot/stat_density.jpeg" alt="A working example of stat_density function in ggplot2">
</figure>

<strong>3.</strong> `stat_summary()` - The function summarises the Y Variable for each unique values of X Variable.

```
# Plotting the bar chart of cylinder counts
ggplot(data = iris) +
  stat_summary(mapping = aes(x = Species, y = Petal.Length),
    fun.ymin = min,
    fun.ymax = max,
    fun.y = mean)
```

<figure>
  <img src="/images/ggplot/stat_summary.jpeg" alt="A working example of stat_summary function in ggplot2">
</figure>

<strong>4.</strong> `stat_smooth()` - Adds a smooth line to a scatter plot.

```
# Adding smooth line to the scatter plot
ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth()
```

<figure>
  <img src="/images/ggplot/stat_smooth.jpeg" alt="A working example of geom_smooth function in ggplot2">
</figure>

# Themes Themes Themes
You must have noticed that the default theme for ggplot2 is pretty much greyish in color. If you are not a great fan of grey color, then don't worry. Ggplot2 has a couple of themes for you to choose from.

```
library(gridExtra)
p1 <- ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth() +
  ggtitle("theme_bw") +
  theme_bw()

p2 <- ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth() +
  ggtitle("theme_linedraw") +
  theme_linedraw()

p3 <- ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth() +
  ggtitle("theme_gray") +
  theme_gray()

p4 <- ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth() +
  ggtitle("theme_dark") +
  theme_dark()

p5 <- ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth() +
  ggtitle("theme_minimal") +
  theme_minimal()

p6 <- ggplot(mpg, aes(displ, hwy)) +
  geom_point() +
  geom_smooth() +
  ggtitle("theme_void") +
  theme_void()

grid.arrange(p1,p2,p3,p4,p5,p6, ncol = 3, nrow = 2)
```

<figure>
  <img src="/images/ggplot/themes.jpeg" alt="Changing from default theme to other in ggplot2">
</figure>
