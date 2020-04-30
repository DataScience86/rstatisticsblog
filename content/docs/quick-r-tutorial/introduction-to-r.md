---
weight: 1
#bookFlatSection: true
date: 2019-10-12
title: "Getting Started"
---

# Introduction

In this quick tutorial, we will cover **essential concepts of R** programming language.  It is **not a zero to hero** tutorial. However, this course does proffer you with all the necessary information to achieve data analysis related tasks in R.

# Installation

The installation of R software is pretty straight forward and is like any other software. First, you need to download the R software IDE (Interactive Development Environments).  

{{< button href="https://cran.r-project.org/" >}}Download R{{< /button >}}  

If you have installed the R software as guided. You can move forward and download the most popular and famous IDE called RStudio.

{{< hint info >}}
**Why R Studio**  
RStudio is a collection of integrated tools that will help you to be more productive. The IDE includes a console, syntax-highlighting editor, plotting tools, and supports direct code execution. It also allows one to view history, debug, and manage workspace.
{{< /hint >}}


{{< button href="https://rstudio.com/products/rstudio/download/" >}}Download RStudio{{< /button >}}


{{< hint danger >}}
**Note**  
RStudio requires R 3.0.1+.
{{< /hint >}}


# Frequently asked questions?

* **Question 1** - What is CRAN ?<br />
CRAN stands for **C**omprehensive **R** **A**rchive **N**etwork. This network is a collection of (File Transfer Protocol)FTP, and web servers present around the world.

* **Question 2** - What does this collection of servers comprise of?<br />
On these web servers, which are placed all over the world, you can find R source code, documentation, contributed packages, R manuals, and datasets.  

* **Question 3** - What is a CRAN mirror?<br />
Each web server of the network is a mirror.

* **Question 4** - Why do we have so many CRAN mirrors?<br />
The idea was to reduce long-distance or international traffic. You can find a list of CRAN mirrors at [CRAN mirrors](https://cran.r-project.org/mirrors.html).

* **Question 5** - How can I permanently save information about my preferred CRAN mirror?<br />
The information about the CRAN mirror gets saved in a file named `.RProfile`. This file is present in the user's home directory. For example, one can use the below line of code to add Sweden as your permanent mirror in `.RProfile` file.

```
options("repos" = c(CRAN = "https://ftp.acc.umu.se/mirror/CRAN/"))
```

* **Question 6** - Can I download previous RGui version?<br />
Yes, you can download the source code of older versions of R. Here is the link to the page from where you can download the [older versions](https://cran.r-project.org/src/base/).


* **Question 7** - How do I update my R software?<br />
You can use `installr` package to update your R software on windows.
Use the below code to install the package and update the software.

```
# Downloading the package from CRAN
install.packages("installr")

# Loading the package
library(installr)

# Use update function to update.
updateR()
```
