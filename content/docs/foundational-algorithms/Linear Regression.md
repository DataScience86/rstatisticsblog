---
title: "Linear Regression"
weight: 1
---

> Linear regression is a supervised machine learning algorithm that is used to predict the continuous variable. The algorithm assumes that the relation between the dependent variable(Y) and independent variables(X), is linear and is represented by a line of best fit. In this chapter, we will learn how to execute linear regression in R using some select functions and test its assumptions before we use it for a final prediction on test data.

# Overview - Linear Regression
In statistics,  linear regression is used to model a relationship between a continuous dependent variable and one or more independent variables. The independent variable can be either categorical or numerical. The case when we have only **one independent variable** then it is called as **simple linear regression**. If we have **more than one independent variable**, then it is called as **multivariate regression**.

A mathematical representation of a linear regression model  is as give below:

{{< katex >}}
Y = β_0 + β_1*X_1 + β_2*X_2 + β_3*X_3 + ….. + β_n*X_n + error
{{< /katex >}}

In the above equation, {{< katex >}}β_0{{< /katex >}} coefficient represents **intercept** and {{< katex >}}β_i{{< /katex >}} coefficient represents **slope**. To help you understand how to implement linear regression, we will be directly diving into a case study.

In the below case study, we will be using **USA housing data** to predict the price. Let us look at the top six observations of USA housing data.

```
# Reading data
housing <- read.csv("./static/data/USA_Housing.csv",
                    header = TRUE, sep = ",")
# Print top 6 observations
head(housing)
```
```
# Output
 AreaIncome AreaHouse AreaNumberofRooms AreaNumberofBedrooms AreaPopulation     Price
1   79545.46  5.682861          7.009188                 4.09       23086.80 1059033.6
2   79248.64  6.002900          6.730821                 3.09       40173.07 1505890.9
3   61287.07  5.865890          8.512727                 5.13       36882.16 1058988.0
4   63345.24  7.188236          5.586729                 3.26       34310.24 1260616.8
5   59982.20  5.040555          7.839388                 4.23       26354.11  630943.5
6   80175.75  4.988408          6.104512                 4.04       26748.43 1068138.1
```

# Glimpse of Exploratory Data Analysis
**1. Checking distribution of target variable** - First, you should always try to understand the nature of your target variable. To achieve this, we will be drawing a histogram with a density plot.

```
library(ggplot2)
# Building histogram
ggplot(data=housing, aes(housing$Price)) +
  geom_histogram(aes(y =..density..), fill = "orange") +
  geom_density()
```

<figure>
  <img src = "/images/regression/priceHist.jpeg" alt ="Building histogram for checking the distribution of price variable">
</figure>

**It is good that the price variable follows a normal distribution.**

**2. Analyzing Summary Statistics** - Here, we will simply create summary statistics for all the variables to understand the behavior of all the independent variables. It will also provide information about missing values or outliers, if any.

```
# loading psych package
library(psych)
psych::describe(housing)
```
```
# Output
                     vars    n       mean        sd     median    trimmed       mad      min        max      range
AreaIncome              1 5000   68583.11  10657.99   68804.29   68611.84  10598.27 17796.63  107701.75   89905.12
AreaHouse               2 5000       5.98      0.99       5.97       5.98      0.99     2.64       9.52       6.87
AreaNumberofRooms       3 5000       6.99      1.01       7.00       6.99      1.01     3.24      10.76       7.52
AreaNumberofBedrooms    4 5000       3.98      1.23       4.05       3.92      1.33     2.00       6.50       4.50
AreaPopulation          5 5000   36163.52   9925.65   36199.41   36112.49   9997.21   172.61   69621.71   69449.10
Price                   6 5000 1232072.65 353117.63 1232669.38 1232159.69 350330.42 15938.66 2469065.59 2453126.94
                      skew kurtosis      se
AreaIncome           -0.03     0.04  150.73
AreaHouse            -0.01    -0.09    0.01
AreaNumberofRooms    -0.04    -0.08    0.01
AreaNumberofBedrooms  0.38    -0.70    0.02
AreaPopulation        0.05    -0.01  140.37
Price                 0.00    -0.06 4993.84
```

**3. Checking Outliers Using Boxplots** - As we have mentioned in the chapter [Analysing Outliers](../Identifying Outliers) that any point which lies beyond whispers is called as outliers.

```
library(reshape)
meltData <- melt(housing)
p <- ggplot(meltData, aes(factor(variable), value))
p + geom_boxplot() + facet_wrap(~variable, scale="free")
```

<figure>
  <img src = "/images/regression/boxplot1.jpeg" alt ="Building boxplot for checking the outliers">
</figure>

*Apart from Area of Number of Bedrooms all other variables seem to have outliers*

**4. Correlation Matrix Visualization** - We will use `corrgram` plot package to visualize and analyse the correlation matrix.
```
require(corrgram)
corrgram(housing, order=TRUE)
```

<figure>
  <img src = "/images/regression/corrplot1.jpeg" alt ="Correlation plot for visualizing degree of association between variables">
</figure>

# Training Regression Model
To build a linear regression, we will be using `lm()` function. The function takes two main arguments.
Formula stating the dependent and independent variables separated by **~**(tilder).
The dataset name.
There are other useful arguments and thus would request you to use `help(lm)` to read more from the documentation.

## Diving data into train and test subsets
The housing data is divided into 70:30 split of train and test. The 70:30 split is the most common and is mostly used during the training phase. 70% of the data is used for training, and the rest 30% is for testing how good we were able to learn the data behavior.

```
library(caret)
# Split data into train and test
index <- createDataPartition(housing$Price, p = .70, list = FALSE)
train <- housing[index, ]
test <- housing[-index, ]
```
```
# Checking the dim of train
dim(train)
```
```
# Output
[1] 3500    6
```
You can see we have 70% of the random observations in the training dataset.

## Building Model
```
# Taining model
lmModel <- lm(Price ~ . , data = train)
# Printing the model object
print(lmModel)
```
```
# Output
Call:
lm(formula = Price ~ ., data = housing)

Coefficients:
         (Intercept)            AreaIncome             AreaHouse  
         -2637299.03                 21.58             165637.03  
   AreaNumberofRooms  AreaNumberofBedrooms        AreaPopulation  
           120659.95               1651.14                 15.20
```
# Interpreting Regression Coefficients
In the above output, **Intercept** represents that the minimum value of Price that will be received, if all the variables are constant or absent.

{{<hint danger>}}
Please note - intercept may not always make sense in business terms.
{{< /hint >}}

**Slope(represented by independent variables)** tells us about the rate of change that the Price variable will witness, with every one unit change in the independent variable. For example – if **AreaHouse** of house increases by one more unit, the **Price** of the house will increase by 165,637.

# Validating Regression Coefficients and Models
We must ensure that the value of each beta coefficient is significant and has not come by chance. In R, the `lm` function runs a **one-sample t-test** against each beta coefficient to ensure that they are significant and have not come by chance. Similarly, we need to validate the overall model. Just like a one-sample t-test, `lm` function also generates three statistics, which help data scientists to validate the model. These statistics include **R-Square**, **Adjusted R-Square**, and **F-test**, also known as global testing.

To view these statistics, we need to pass the `lmModel` object to the `summary()` function.

```
# Checking model statistics
summary(lmModel)
```
```
# Output
Call:
lm(formula = Price ~ ., data = housing)

Residuals:
    Min      1Q  Median      3Q     Max
-337020  -70236     320   69175  361870

Coefficients:
                          Estimate    Std. Error  t value            Pr(>|t|)    
(Intercept)          -2637299.0333    17157.8092 -153.708 <0.0000000000000002 ***
AreaIncome                 21.5780        0.1343  160.656 <0.0000000000000002 ***
AreaHouse              165637.0269     1443.4130  114.754 <0.0000000000000002 ***
AreaNumberofRooms      120659.9488     1605.1604   75.170 <0.0000000000000002 ***
AreaNumberofBedrooms     1651.1391     1308.6712    1.262               0.207    
AreaPopulation             15.2007        0.1442  105.393 <0.0000000000000002 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 101200 on 4994 degrees of freedom
Multiple R-squared:  0.918,    Adjusted R-squared:  0.9179
F-statistic: 1.119e+04 on 5 and 4994 DF,  p-value: < 0.00000000000000022
```

In the above output, **Pr(>|t|)** represents the p-value, which can be compared against the alpha value of 0.05 to ensure if the corresponding beta coefficient is significant or not. The `lm` function here lends a helping hand. All values in the output that have (.) period or (\*) astric against the variable names indicates that these values are significant. Based upon this, we now know that all variables are statistically significant except <strong>AreaNumberofBedrooms</strong>.

For overall model accuracy, let's discuss statistics generated by `lm` function one by one.

**1. Multiple R-squared:  0.918** - The R-squared value is formally called a **coefficient of determination**. Here, 0.918 indicates that the intercept, AreaIncome, AreaHouse, AreaNumberofRooms, and AreaPopulation variables, when put together, are able to explain 91.8% of the variance in the Price variable. The value of R-squared lies between 0 to 1. In practical applications, if the R2 value is higher than 0.70, we consider it a good model.

**2. Adjusted R-squared:  0.9179** - The Adjusted R-squared value tells if the addition of new information ( variable )  brings significant improvement to the model or not. So as of now, this value does not provide much information. However, the increase in the adjusted R-squared value with the addition of a new variable will indicate that the variable is useful and brings significant improvement to the model.

{{< hint warning>}}
A large difference between the R-Squared and Adjusted R-squared is not appreciated and generally indicates that multicollinearity exists within the data.
{{< /hint >}}

**3. F-statistic: 1.119e+04 on 5 and 4994 DF,  p-value: < 0.00000000000000022** - This line talks about the global testing of the model. The `lm` function runs an ANOVA test to check the significance of the overall model. Here the null hypothesis is that the model is not significant, and the alternative is that the model is significant. According to the p-values < 0.05, our model is significant.

Albeit, looking at these statistics is enough to take a call on the model significance. But there are other validation methods for linear regression that can be of help while deciding how good or bad the model is. Some of them are mentioned below:

**4. AIC and BIC values** - The AIC(Akaike’s information criterion, 1974) and BIC(Bayesian information criterion, 1978) are penalized-likelihood criteria. Both these measures use a "measure of fit + complexity penalty"  to get the final values.

AIC = - 2 \* ln(likelihood) + 2 \* p <br/>
BIC = - 2 \* ln(likelihood) + ln(N) \* p

Here p = number of estimated parameters and N = sample size.

The AIC and BIC values can be used for choosing the best predictor subsets in regression and for comparing different models. When comparing different models, the model with minimum AIC and BIC values is considered the best model.

{{< hint warning>}}
**Note**  
AIC is likely to overfit the data, whereas BIC is susceptible to underfit the data.
{{< /hint >}}

```
# Using AIC function
AIC(lmModel)
# Using BIC function
BIC(lmModel)  
```
```
# Output AIC
[1] 129441.3

# Output BIC
[1] 129486.9
```
**5. Root Mean Square Error(RMSE)** - By comparing the RMSE statistics of different models, we can decide which is a better model. The model with the lower RMSE value is considered a better model. There are other similar functions like MAE, MAPE, MSE, and so on that can be used. These functions can be found in [`Metrics`](https://cran.r-project.org/web/packages/Metrics/Metrics.pdf) R Package. These functions take majorly two arguments: One is the actual value and second, predicted values. So let's see how we can get these values. The actuals can be 100% found from the original dataset or the training data in our case. However, to find the fitted values, we need to explore the model object.

```
# Checking model object for actual and predicted values
names(lmModel)
```
```
# Output
  [1] "coefficients"  "residuals"    
  [3] "effects"       "rank"         
  [5] "fitted.values" "assign"       
  [7] "qr"            "df.residual"  
  [9] "xlevels"       "call"         
  [11] "terms"         "model"
```

The above vector presents the names of the object that constitute the model object. Here, fitted values are the predicted values. Now, we will use these values to generate the **rmse** values.

```
library(Metrics)
rmse(actual = train$Price, predicted = lmModel$fitted.values)
```
```
# Output
[1] 493370.4
```

# Checking Assumptions of Linear Regression
Linear regression is parametric, which means the algorithm makes some assumptions about the data.  A linear regression model is only deemed fit is these assumptions are met. There are about four assumptions and are mentioned below. If the model fails to meet these assumptions, then we simply cannot use this model.

**1. Errors should follow normal distribution** - This can be checked by drawing a histogram of residuals or by using `plot()` function. The `plot` function creates 4 different charts. One of which is an NPP plot. The chart confirms if the errors follow a normal distribution or not.

## Generating histogram
```
# Histogram to check the distribution of errors
hist(lmModel$residuals, color = "grey")
```
<center>![Histogram to check linear regression assumption](/images/regression/normalerror1.jpeg "Histogram - errors are normally distributed")</center>

**The above histogram of errors clearly states that errors are normally distributed.**

## Generating NPP plot
We except the points to be very close to the dotted line in an NPP plot. Points being close to the line means that errors follow a normal distribution.
```
plot(lmModel)
```
<center>![npp plot to check linear regression assumptions](/images/regression/nppplot.jpeg "npp plot - errors are normally distributed")</center>

**2. There should be no heteroscedasticity** - This means that the variance of error terms should be constant. We shall not see any patterns when we draw a plot between residuals and fitted values. And the mean line should be close to Zero.

## Generating the scatterplot between residuals and fitted values

```
# Using plot function
plot(lmModel)
```

<figure>
  <img src="/images/regression/hetrosca.jpeg" alt="Checking for heteroscedasticity in R using plot function">
<figure>

*A straight red line closer to the zero value represents that we do not have heteroscedasticity problem in our data.*

**3. There should be no multicollinearity** - The linear model assumes that the predictor variables do not correlate with each other. If they exhibit high correlation, it is a problem and is called **multicollinearity**.  A variation inflation factor test can help check for the multicollinearity assumption.

VIF = 1/(1-R2)

The R implementation of the below function can be found [here](https://gist.github.com/fawda123/4717702).

{{< hint info >}}
VIF is an iterative process. The function will remove one variable at a time, which is cause for multicollinearity and repeats the process until all problem causing variables are removed. So, finally, we are left with the list of variables that have no or very weak correlation between them.
{{< /hint >}}

```
vif_func(housing[, 1:5])
```
```
var                  vif             
 AreaIncome           1.00115868968647
 AreaHouse            1.00057658981485
 AreaNumberofRooms    1.27353508823836
 AreaNumberofBedrooms 1.27441273719468
 AreaPopulation       1.00126579728799

All variables have VIF < 10, max VIF 1.27

[1] "AreaIncome"          
[2] "AreaHouse"           
[3] "AreaNumberofRooms"   
[4] "AreaNumberofBedrooms"
[5] "AreaPopulation"     
```
*There is no multicollinearity problem in the dataset. Generally, VIF values which are greater than 5 or 7 are the cause of multicollinearity.*

**3. There should be no auto serial correlation** - The autocorrelation means that error terms should not be correlated with each other. To check this, we can run the Durbin-Watson test(dw test). The test returns a value between 0 and 4. If the value is two, we say there is no auto serial correlation. However, a value higher than 2 represents (-) ve correlation and value lower than 2 represents (+) ve correlation.

```
library("lmtest")
dwtest(lmModel)
```
```
# Output
    Durbin-Watson test

data:  lmModel
DW = 1.9867, p-value = 0.3477
alternative hypothesis: true autocorrelation is greater than 0
```
*We got a value of 1.9867 which suggests that there is no auto serial correlation.*

{{< hint info >}}Our model met all the four assumptions of linear regression.{{< /hint >}}

# Predicting Dependent Variable(Y) in Test Dataset
We test the model performance on test data set to ensure that our model is stable, and we get the same or closer enough results to use this trained model to predict and forecast future values of dependent variables. To predict, we use `predict` function, and then we generate R-Squared value to see if we get the same result as we got in the training dataset or not.

```
# Predicting Price in test dataset
test$PreditedPrice <- predict(lmModel, test)
# Priting top 6 rows of actual and predited price
head(test[ , c("Price", "PreditedPrice")])
```
```
# Output
      Price   PreditedPrice
2  1505890.9     1494997.3
3  1058988.0     1253539.2
8  1573936.6     1572965.3
12  663732.4      629153.8
23  718887.2      727497.8
24  743999.8      991034.7
```

# Generating R-Squared Value for the test dataset
We are using a user-defined formula to generate the R-Squared value here.

```
actual <- test$Price
preds <- test$PreditedPrice
rss <- sum((preds - actual) ^ 2)
tss <- sum((actual - mean(actual)) ^ 2)
rsq <- 1 - rss/tss
rsq
```
```
# Output
[1] 0.9140436
```
*Our model is performing fantastic*. <br/>
*In test dataset, we got an accuracy of 0.9140436 and training data set, we got an accuracy of 0.918*.

In this chapter, We learned many things related to linear regression from a practical and theoretical point of view. We learned when to use linear regression, how to use it, how to check the assumptions of linear regression, how to predict the target variable in test dataset using trained model object, and we also learned how to validate the linear regression model using different statistical methods. In the next chapter, we will learn about an advanced linear regression model called ridge regression.
