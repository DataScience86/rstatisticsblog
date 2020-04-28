---
title: "Binary Logistic Regression"
weight: 4
---

> **Logistics Regression** is used to explain the relationship between the categorical dependent variable and one or more independent variables. When the dependent variable is dichotomous, we use **binary logistic regression**. However, by default, a binary logistic regression is almost always called logistics regression.

# Overview - Logistic Regression
The logistic regression model is used to model the relationship between a binary target variable and a set of independent variables. These independent variables can be either qualitative or quantitative. In logistic regression, the model predicts the logit transformation of the probability of the event. The following mathematical formula is used to generate the final output.

<figure>
  <img src="/images/regression/logitMathEquation.JPG", alt="mathematical equation of logistic regression">
</figure>

In the above equation, p represents the odds ratio, and the formula for the odds ratio is as given below:

<figure>
  <img src="/images/regression/oddsMathEquation.JPG", alt="mathematical equation of odds ratio">
</figure>

# Case Study - What is UCI Adult Income ? <a name="Case Study"></a>
In this tutorial, we will be using Adult Income data from the UCI machine learning repository to predict the income class of an individual based upon the information provided in the data. You can download this [Adult Income](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/) data from the UCI repository.

**Beta coefficient in logistics regression are chosen based upon maximum likelihood estimates.**

The idea here is to give you a fair idea about how a data scientist or a statistician builds a predictive model. So, we will try to demonstrate all the essential tasks which are part of model building exercise. However, for the demo purpose, we will be using only three variables from the whole dataset.

# Getting the data
The adult dataset is fairly large, and to read it faster, I will be using `read_csv()` from `readr` package to load the data from my local machine.
```
library(readr)
adult <- read_csv("./static/data/adult.csv")
# Checking the structure of adult data
str(adult)
```
```
# Output
Classes ‘tbl_df’, ‘tbl’ and 'data.frame':    48842 obs. of  15 variables:
 $ Age           : int  25 38 28 44 18 34 29 63 24 55 ...
 $ Workclass     : chr  "Private" "Private" "Local-gov" "Private" ...
 $ Fnlwgt        : int  226802 89814 336951 160323 103497 198693 227026 104626 369667 104996 ...
 $ Education     : chr  "11th" "HS-grad" "Assoc-acdm" "Some-college" ...
 $ Education-num : int  7 9 12 10 10 6 9 15 10 4 ...
 $ Marital-status: chr  "Never-married" "Married-civ-spouse" "Married-civ-spouse" "Married-civ-spouse" ...
 $ Occupation    : chr  "Machine-op-inspct" "Farming-fishing" "Protective-serv" "Machine-op-inspct" ...
 $ Relationship  : chr  "Own-child" "Husband" "Husband" "Husband" ...
 $ Race          : chr  "Black" "White" "White" "Black" ...
 $ Sex           : chr  "Male" "Male" "Male" "Male" ...
 $ Capital-gain  : int  0 0 0 7688 0 0 0 3103 0 0 ...
 $ Capital-loss  : int  0 0 0 0 0 0 0 0 0 0 ...
 $ Hours-per-week: int  40 50 40 40 30 30 40 32 40 10 ...
 $ Native-country: chr  "United-States" "United-States" "United-States" "United-States" ...
 $ Class         : chr  "<=50K" "<=50K" ">50K" ">50K" ...
```

As mentioned earlier, we will be using three variables; **WorkClass**, **Marital-status** and **Age** to build the model. Out of these three variables - **WorkClass** and **Marital-status** are **categorical variables** where as **Age** is a continuous variable.

```
# Subsetting the data and keeping the required variables
adult <- adult[ ,c("Workclass", "Marital-status", "Age", "Class")]
# Checking the dim
dim(adult)
```
```
# Output
[1] 48842     4
```
*The new dataset has 48842 observations and only 4 variables*

{{< hint warning >}}
We cannot use categorical variables directly in the model. So for these variables, we need to create dummy variables. A **dummy variable** takes the value of 0 or 1 to indicate the absence or presence of a particular level. In our example, the function will automatically create dummy variables.
{{< /hint >}}


# Summarizing categorical variable
The best way to summarize the categorical variable is to create the frequency table, and that is what we will do using `table` function.

```
# Generating the frequency table
table(adult$Workclass)
```
```
# Output
        ?      Federal-gov        Local-gov     Never-worked
            2799             1432             3136               10
         Private     Self-emp-inc Self-emp-not-inc        State-gov
           33906             1695             3862             1981
     Without-pay
              21
```
*The table suggests that there are some 2799 missing values in this variable, which are represented by the (?) symbol. Also, the data is not uniformly distributed. Some of the levels have very few observations and looks like we have an opportunity to combine similar looking levels*.

```
# Combining levels
adult$Workclass[adult$Workclass == "Without-pay" | adult$Workclass == "Never-worked"] <- "Unemployed"
adult$Workclass[adult$Workclass == "State-gov" | adult$Workclass == "Local-gov"] <- "SL-gov"
adult$Workclass[adult$Workclass == "Self-emp-inc" | adult$Workclass == "Self-emp-not-inc"] <- "Self-employed"

# Checking the table again
table(adult$Workclass)
```
```
# Output

            ?   Federal-gov       Private Self-employed
         2799          1432         33906          5557
       SL-gov    Unemployed
         5117            31
```

**Let us do a similar treatment for our other categorical variable**

```
# Generating the frequency table
table(adult$Marital-status)
```
```
# Output
    Divorced     Married-AF-spouse
                 6633                    37
   Married-civ-spouse Married-spouse-absent
                22379                   628
        Never-married             Separated
                16117                  1530
              Widowed
                 1518
```
*We can reduce the above levels to never married, married and never married*.

```
# Combining levels
adult$Marital-status[adult$Marital-status == "Married-AF-spouse" | adult$Marital-status == "Married-civ-spouse" | adult$Marital-status == "Married-spouse-absent"] <- "Married"

adult$Marital-status[adult$Marital-status == "Divorced" |
                       adult$Marital-status == "Separated" |
                       adult$Marital-status == "Widowed"] <- "Not-Married"

# Checking the table again
table(adult$Marital-status)
```
```
# Output
      Married       Never-married   Not-Married
        23044         16117          9681
```

*This variable looks well-distributed then Workclass. Now, we must convert them to factor variables using as.factor() function.*

```
# Converting to factor variables
adult$Workclass <- as.factor(adult$Workclass)
adult$Marital-status <- as.factor(adult$Marital-status)
adult$Class <- as.factor(adult$Class)
```

# Deleting the missing values
We will first convert all ? to NA and then use `na.omit()` to keep the complete observation.
```
# Converting ? to NA
adult[adult == "?"] <- NA

# Keeping only the na.omit() function
adult <- na.omit(adult)
```

# Finally taking a look into the target variable
To save time, I will directly be going forward with the bivariate analysis. Let us see how the distribution of age looks for the two income groups.

```
library(ggplot2)
ggplot(adult, aes(Age)) +
  geom_histogram(aes(fill = Class), color = "black", binwidth = 2)
```

<figure>
  <img src="/images/regression/classAge.jpeg" alt= "Distribution of age by class variable using histogram">
<figure>

*Data looks much more skewed for the lower-income people as compared to the high-income group*.

# Building the Model
We will be splitting the data into the test and train using the `createDataPartition()` function from the `caret` package in R. We will train the model using the training dataset and predict the values on the test dataset. To train the logistic model, we will be using `glm()` function.

```
# Loading caret library
require(caret)
# Splitting the data into train and test
index <- createDataPartition(adult$Class, p = .70, list = FALSE)
train <- adult[index, ]
test <- adult[-index, ]

# Training the model
logistic_model <- glm(Class ~ ., family = binomial(), train)

# Checking the model
summary(logistic_model)
```
```
# Output
Call:
glm(formula = Class ~ ., family = binomial(), data = train)

Deviance Residuals:
    Min       1Q   Median       3Q      Max  
-1.6509  -0.8889  -0.3380  -0.2629   2.5834  

Coefficients:
                               Estimate Std. Error z value             Pr(>|z|)    
(Intercept)                   -0.591532   0.094875  -6.235     0.00000000045227 ***
WorkclassPrivate              -0.717277   0.077598  -9.244 < 0.0000000000000002 ***
WorkclassSelf-employed        -0.575340   0.084055  -6.845     0.00000000000766 ***
WorkclassSL-gov               -0.445104   0.086089  -5.170     0.00000023374732 ***
WorkclassUnemployed           -2.494210   0.766488  -3.254              0.00114 **
`Marital-status`Never-married -2.435902   0.051187 -47.589 < 0.0000000000000002 ***
`Marital-status`Not-Married   -2.079032   0.045996 -45.200 < 0.0000000000000002 ***
Age                            0.023362   0.001263  18.503 < 0.0000000000000002 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 36113  on 32230  degrees of freedom
Residual deviance: 28842  on 32223  degrees of freedom
AIC: 28858

Number of Fisher Scoring iterations: 5
```

## Interpreting Logistic Regression Output
All the variables in the above output have turned out to be significant(p values are less than 0.05 for all the variables). If you look at the categorical variables, you will notice that n - 1 dummy variables are created for these variables. Here, n represents the total number of levels. The one level which is left is considered as the reference variable, and all other variable levels are interpreted in reference to this level.

**1. Null and Residual deviance** - Null deviance suggests the response by the model if we only consider the intercept; lower the value better is the model. The Residual deviance indicates the response by the model when all the variables are included; again, lower the value, better is the model.

**2. (Intercept)** - Intercept(β0) indicates the log of odds of the whole population of interest to be on higher-income class with no predictor variables in the model. We can convert the log of odds back to simple probabilities by using **sigmoid** function.

    Sigmoid function, p = exp(-0.591532)/(1+exp(-0.591532))

The other way is to convert this logit of odds to simple odds by taking exp(-0.591532) =  0.5534. The number indicates that the odds of an individual being in the high-income group decreases by 45% if we have no predictor variables.

**3. WorkclassPrivate** - The beta coefficient against this variable is -0.717277.  Let us convert this value into odds by taking the exp(-0.717277) = 0.4880795. The value indicates that the odds of an individual with Private work-class being in the high-income group decreases by 52% than the one in a Federal-gov job.

> Out of 5 levels, the Federal-gov level became the reference, and thus all other levels of workclass variables are inferred in comparison to the referenced variable. That is how we interpret the categorical variables.

**4. Age** - The beta coefficient of the age variable is 0.023362, which is in the logit of odds terms. When we convert this to odds by taking exp(0.023362) we get 1.023. The value indicates that as age increase by one more unit, then the odds of an individual being in the high-income group will increase by 2%.

{{< hint info >}}
**Note**  
Odds value is never negative, and the value of 1 indicates that this variable has no impact on the target variables. If the value is less than one then the value is read as (1 - value) as a decrease in odds and a value greater than one indicates an increase in the odds.{{< /hint >}}

# Predicting Dependent Variable(Y) in Test Dataset
To predict the target variable in the unseen data, we use `predict` function. The output of the predict function is the probability.
```
# Predicting in the test dataset
pred_prob <- predict(logistic_model, test, type = "response")
```

# Evaluating Logistic Regression Model
There are number of ways in which we can validate our logistic regression model. We have picked all the popular once which you can use to evaluate the model. Let's discuss and see how to run those in R.

**1. Classification Table** - I would say this one is the most popular validation technique among all the known validation methods of the logistic model. It's basically a contingency table that we draw between the actual values and the predicted values. The table is then used to dig in many other estimates like **Accuracy**, **Misclassification Rate**, **True Positive Rate**, also known as recall, **True Negative Rate**, and **Precision**.  

Here is the representation of the contingency table marking essential terms.

<figure>
  <img src="/images/regression/confusionMatrix.jpg" alt= "Important terms in classification">
<figure>

Before we create a contingency table, we need to convert the probability into the two levels IE class <=50K and >50K. To get these values, we will be using a simple `ifelse()` function and will create a new variable in the train data by the name pred_class.

**We have to repeat the below steps for both the test and train dataset**.
# Converting probability to class values in the training dataset

```
# Converting from probability to actual output
train$pred_class <- ifelse(logistic_model$fitted.values >= 0.5, ">50K", "<=50K")

# Generating the classification table
ctab_train <- table(train$Class, train$pred_class)
ctab_train
```
```
# Output
        <=50K  >50K
  <=50K  1844 22391
  >50K   1697  6299
```

# Training dataset converting from probability to class values
```
# Converting from probability to actual output
test$pred_class <- ifelse(pred_prob >= 0.5, ">50K", "<=50K")

# Generating the classification table
ctab_test <- table(test$Class, test$pred_class)
ctab_test
```
```
# Output
        <=50K  >50K
  <=50K  9602   784
  >50K   2676   750
```
# Accuracy
Accuracy is calculated by adding the diagonal elements and dividing it by the sum of all the elements of the contingency table. We will also compare the accuracy of the training dataset with the test dataset to see if our results are holding in the unseen data or not.

    Accuracy = (TP + TN)/(TN + FP + FN + TP)

```
# Accuracy in Training dataset
accuracy_train <- sum(diag(ctab_train))/sum(ctab_train)*100
accuracy_train
```
```
#Output
[1] 74.7355
```
*Our logistics model is able to classify 74.7% of all the observations correctly in training dataset.*

```
# Accuracy in Test dataset
accuracy_test <- sum(diag(ctab_test))/sum(ctab_test)*100
accuracy_test
```
```
#Output
[1] 74.94932
```
*The over all correct classification accuracy in test dataset is 74.9% which is comparable to train dataset. This shows that our model is perfomring good.*

> A model is considered fairly good if the model accuracy is greater than 70%.

## Misclassification Rate
**Misclassification Rate** indicates how often is our predicted values are False.

    Misclassification Rate = (FP+FN)/(TN + FP + FN + TP)

## True Positive Rate - Recall or Sensitivity
**Recall or TPR** indicates how often does our model predicts actual TRUE from the overall TRUE events.

    Recall Or TPR = TP/(FN + TP)

```
# Recall in Train dataset
Recall <- (ctab_train[2, 2]/sum(ctab_train[2, ]))*100
Recall
```
```
# Output
[1] 21.22311
```
## True Negative Rate
**TNR** indicates how often does our model predicts actual non events from the overall non events.

    TNR = TN/(TN + FP)

```
# TNR in Train dataset
TNR <- (ctab_train[1, 1]/sum(ctab_train[1, ]))*100
TNR
```
```
#Output
92.39117
```

### Precision
**Precision** indicates how often does your predicted TRUE values are actually TRUE.

    Precision = TP/FP + TP

```
# Precision in Train dataset
Precision <- (ctab_train[2, 2]/sum(ctab_train[, 2]))*100
Precision
```
```
#Output
[1] 47.92432
```

# Calculating F-Score
**F-Score** is a harmonic mean of recall and precision. The score value lies between 0 and 1. The value of 1 represents perfect precision & recall. The value 0 represents the worst case.

```
F_Score <- (2 * Precision * Recall / (Precision + Recall))/100
F_Score
```
```
#Output
[1] 0.2941839
```

# ROC Curve
The area under the curve(AUC) is the measure that represents ROC(Receiver Operating Characteristic) curve. This ROC curve is a line plot that is drawn between the Sensitivity and (1 - Specificity) Or between TPR and TNR. This graph is then used to generate the AUC value. An AUC value of greater than .70 indicates a good model.

```
library(pROC)
roc <- roc(train$Class, logistic_model$fitted.values)
auc(roc)
```
```
# Output
Area under the curve: 0.7965
```

# Concordance
**Concordance** In how many pairs does the probability of ones is higher than the probability of zeros divided by the total number of possible pairs. The higher the values better is the model. The value of concordance lies between 0 and 1.

Similar to concordance, we have **disconcordance** which states in how many pairs the probability of ones was less than zeros. If the probability of ones is equal to 1 we say it is a **tied pair**.

```
library(InformationValue)
Concordance(logistic_model$y,logistic_model$fitted.values)
```
```
# Output
$`Concordance`
[1] 0.7943923

$Discordance
[1] 0.2056077

$Tied
[1] 0

$Pairs
[1] 193783060
```
