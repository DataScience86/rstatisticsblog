---
title: "Conditions & Loops"
weight: 3
---

# Conditions and Loops In R
Conditional statements in R programming language enables the user to execute a statement based upon a particular condition or criteria. On the other hand, the loops ensure that the same task is executed again and again.

Having knowledge and understanding of how the two work is extremely critical for any programmer. As a data analyst or a data science practitioner, you will be using them quite often.

# if statement in R Programming
If statements are used to check a condition and if the condition is met, it executes a block of code. It returns TRUE if the condition is met and returns FALSE if the condition is not met.

## if statement syntax
```
# Syntax of if statement
if(condition){
  # block of code to be executed
}
```
## Working example
Let us look at an example - Print the summary statistics of a variable only if the variable is numeric.

```
# Getting the iris data
data(iris)
# Executing the if statement
if(is.numeric(iris$Petal.Length)){
  print(summary(iris$Petal.Length))
}
```

{{< hint info>}}
In the above example, you saw that the summary got printed because the variable type was numeric. However, if the class of variable would have been different, no output would have been written. In fact, nothing would have happened, and leaving it like that is not a good idea, especially if a user is involved. You may want to inform the user that the variable entered is not a numeric one, and thus nothing is printed.
{{< /hint >}}

# if-else statement syntax
```
# Syntax of if statement
if(condition){
  # block of code to be executed
} else {
  # block of code to be executed in case if returns FALSE
}
```
## Working example
Let us take the above example and add an else statement that will print a statement notifying the user that the input variable is not numeric.

```
# Executing the if statement
if(is.numeric(iris$Species)){
  print(summary(iris$Species))
} else {
  print("Tested variable is not a numeric variable.")
}
```
## if-else statement syntax
The nested if else statement is used when you want to test two or more conditions.

```
# Syntax of nested if-else statement
if(first condition){
  # block of code to be executed
} else if(second condition) {
  # block of code to be executed if the second condition is met
} else {
  # block of code to be executed if none of the conditions is met.
}
```
## Working example
Let us take the above example and say we should print the summary statistics if the variable is numeric or integer and table if the variable is a factor or character type. If none of the conditions are met; print - we are sorry we do not accept this variable type.

```
# Resetting the data
data(iris)
# Getting the variable name
varName <- "Species"
# Executing the if statement
if(is.numeric(iris[, varName]) | is.integer(iris[, varName])){
  print(summary(iris$Species))
} else if(is.character(iris[, varName]) | is.factor(iris[, varName])) {
  print(table(iris[ , varName]))
} else {
  print("Variable type unknown")
}
```

{{< hint danger>}}You can have as many conditions in a nested if-else as you wish. However, remember that the if-else statement works from top to bottom. That means it may lead to unnecessary computation at times based upon your analysis requirement. {{< /hint >}}


# switch statement in R Programming
If you are sure about all the output cases based upon the condition, then it is recommended to use switch function over the if-else statement.

## Working example
Print the mean if the user passes "1" and print median if the user passes "2". We will use a switch statement to achieve this task.

```
# Refreshing the mtcars dataset
data(mtcars)

# printing mean
switch("1",
         "1" = mean(mtcars$mpg),
         "2" = median(mtcars$mpg))
}

```

# for loop in R Programming
**for loops** are used to execute the same block of code over a range of values. That means as the range of values gets over, the for loop ends.

## for loop syntax
The syntax is pretty simple and straightforward. There are three components of the syntax: one, the vector in which we have the values. Second, the index identifier that will call one value at a time from the vector and final part is the block of code, which will be executed over and over.

```
# For loop syntax
for(i in vector){
  # Block of code
}
```
## Working example
Print the class of all the variable using for loop.

```
varNamesList <- colnames(iris)
for(i in varNamesList){
  print(sprintf("The class of %s is %s.", i, class(iris[, i])))
}
```

To print the value while running a **for loop**, you must use print() function. In the above example, I used the sprintf() function to ensure that values get printed on the console.

# While loop in R Programming
A while loop is designed to execute when a condition is met. The execution of the code is stopped if the condition is not met.

## Working Example
Print a sequence of numbers starting from 1 till 10.

```
num <- 1
while(num <= 10){
  print(num)
  num = num + 1
}
```

## How to break or skip a value in a loop
To skip a value or break out of a loop in R, we have two control statements called `next` and `break` statement.

## Working example
Print only those names from a character vector that have less than five alphabets and skip all the names which are spelled using more than five characters. To achieve this we will be using **nchar()** function and **if statement** inside a **for loop**.

```
# Defining a list of vector containg names
allNames <- c("John", "Michell", "Danny", "Silver", "Olive")

for (nam in allNames) {
  if (nchar(nam) > 5){
    next
  }
  print(nam)
}
```

In this chapter, we learned how to use conditional statements, loops, and control statements in R. We used some simple and some little difficult examples to showcase the use of the same. But this is just the beginning. With time you will be able to create some very complicated programs using these simple things. In the next chapter, we will look into apply family functions, and we will also learn how to define custom functions.
