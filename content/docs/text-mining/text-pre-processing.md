---
title: "Text Processing"
date: 2020-05-05
weight: 1
tags: ["stopwords", "text pre-processing" ,"text cleaning", "corpus", "stemming", "lemmatization", "text mining", "R Code"]
category: ["R", "Text Mining"]
---

It is not surprising to know that most of the information out in the world is available in an unstructured form. An unstructured data refers to the one that does not reside in a traditional relational database. Some of the examples include images, webpages, word documents, pdf documents, and reports. In this section, we will talk about some basic concepts and functions which you can use for text processing in R.

Although R provides several packages to perform tasks related to text processing, we will be using the `tm`, `SnowballC`, `textstem`, and `stringr` to perform several tasks related to text processing.

# How to obtain the text from different sources

As you can imagine, the primary source of text data is webpages, pdfs, and other forms of documents. So it becomes vital for us to learn how to extract data from different sources and prepare it for feature extraction.

## Reading text file in R
Reading a text file is relatively simple in R. You can use `read.delim()` file to read any text file in R.

```
#reading text file
my_data <- read.delim("rstatisticsblog.txt")
```

## Reading pdf file in R
To read a pdf files we will be using `pdf_text()` function from `pdftools` package. A sample code is provided below:

```
install.packages("pdftools")
library(pdftools)

#setting the working directory
setwd("C:/Users/EAOHSHM/Documents/Personal")

# Reading pdf file
file <- pdf_text("Paradis-rdebuts_en.pdf")

# Number of pages
length(file)

# Viewing 7th page
file[7]
```

## Extracting text from the website

The process of extracting data from the website is also called as web scraping. The method of web scraping consists of a couple of steps and can become complicated, depending on the type of site and variety of components you wish to extract from the site. Some of the packages which you can use to extract data from different websites are `rvest`, `RCurl` and `XML`.  For now, we will skip this part as it is beyond the scope of this article. But I encourage you to go and read more about these packages in R.

# General text processing steps
The different sets of actions we need to do as part of text cleaning or preprocess are as given below.

1. Convert to lower case
2. Remove numbers
3. Remove punctuations
4. Remove whitespace
5. Remove stop words
6. Stemming
7. Lemmatization

# Preparation
Here We will go through all the basic text cleaning steps using some of the most popular text processing packages in R. List of packages used in this article is as given below:

```
library(stringr)
library(tm)
library(SnowballC)
library(textstem)


```

## Convert text to lower
It is always a good idea to convert all of your text to the same case. R is a case sensitive language, and having the same word in two different cases can result in a program reading it as two separate entities.

```
text <- "This is a TEXT which Has different Cases"
text_lower <- tolower(text)
```

```
[1] "this is a text which has different cases"
```

## Remove numbers
In most cases, numbers present no real information, and thus it is a standard process to remove numerical values from the text. However, in some cases, you may be interested in extracting or collecting this information. We will be using a regular expression to remove the number. You can learn more about regular expressions here - https://regexone.com.

```
text <- "I contain number like 223, 12, and 24"
gsub('[[:digit:]]+', '', text)
```

```
[1] "I contain number like , , and "
```

## Remove punctuations
To remove punctuation marks from the string, we will again fall back on regular expressions.

```
text <- "This string contains punctuation ?, % $ @ and #"
gsub('[[:punct:]]', '', text)
```

```
[1] "This string contains punctuation     and "
```

## Removing whitespaces
You must have noticed that when we removed things like numbers or punctuations, than whitespace is created, and it is essential to remove those spaces else, they will be counted as empty strings.

```
text <- "This string contains punctuation     and "
gsub(' +',' ',text)
```

```
[1] "This string contains punctuation and "
```

But with this approach, you can see that single whitespace is still left at the end. We can use the `str_trim()` function to get rid of the trailing whitespace.

```
text <- "This string contains punctuation and "
str_trim(text, side = "both")
```

```
[1] "This string contains punctuation and"
```

## Remove stop words
Stop words are a collection of common words that do not provide any information about the content of the text. Here `removeWords()` function is being used to get rid of predefined stop words under the tm package. Based on one's requirement, additional terms can be added to this list.

```
library(tm)

text <- "The goal of the present document (created in 2012) is to give a starting point for people newly interested in R. I chose to emphasize 30 times on the understanding of how R works, with the aim of a beginner, rather than expert, use."

text <- tolower(text)
removeWords(text, stopwords())

```

```
[1] "goal   present document (created  2012)   give  starting point  people newly interested  r.  chose  emphasize 30 times   understanding   r works,   aim   beginner, rather  expert, use."
```

It is good practice to remove whitespaces at the end. For now,  go ahead and clean the whitespaces from the above output string.

## Stemming
The stemming algorithm is a collection of rules that work by cutting the end or beginning of the word. This chopping of the word can be helpful in some cases, but not always. There are different algorithms, but the most common one is **Porter stemmer**. However, the purpose of all the algorithms is to reduce the word to its root level. Here we will use `wordStem()` function for stemming from `SnowballC` package in R.

```
library(SnowballC)

text <- c('companies', 'authorized', 'learning', "cooking")

text <- tolower(text)
wordStem(text)

```

```
[1] "compani" "author"  "learn"   "cook"  
```
You can see from the above output, stemming does not always return a word which makes sense, or we can agree that it is the word representing the root of the actual word.

## Lemmatization
Just like stemming, lemmatization is also a process of finding a word's lemma meaning root. Lemmatization is generally more accurate as compared to stemming because it considers a morphological analysis of the word by going through the dictionaries to link back the word to its lemma. Here we use `lemmatize_words()` function from `textstem` package in R.

```
library(textstem)

text <- c('companies', 'authorized', 'learning', "cooking")

text <- tolower(text)
lemmatize_words(text)

```

```
[1] "company"   "authorize" "learn"     "cook"
```

It is quite evident that lemmatization returns much better results when compared to that of stemming.

# Bonus - Create a corpus
A corpus is a collection of text that has been put together with a certain set of predetermined criteria. The `tm` package provides two different implementations I.E. **"VCorpus"** and **"PCorpus"**. The default implementation is `VCorpus`(short for Volatile Corpus). It is called volatile since once the R object is destroyed, the whole corpus is gone, as it is stored in RAM. Another implementation is "PCorpus",  here P is short for **permanent corpus**. You will not lose the documents even if the R object is destroyed as 'PCorpus` stores the documents in a physical form outside of R.

Below we will create a VCorpus corpus.

```
library(tm)
Corpus_text = Corpus(VectorSource(text))

```
