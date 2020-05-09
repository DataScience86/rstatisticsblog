---
title: "Word and Document Analysis"
date: 2020-05-09
bookFlatSection: true
weight: 2
---

# Overview
In the real world, the use of text analytics is growing at a swift pace. Organizations across the globe have started to realize that textual the analysis of textual data can reveal significant insights that can help with decision making. As of today, the text analytics field is seeing exponential growth and will continue to see this high growth rate, even for the coming years. The increase in text analysis use cases can be attributed to the continuous growth of unstructured data both within and outside of the organization.

In this section, we will learn how to figure out and quantify what a document is all about through the use of term-frequency, inverse document frequency, and a technique called tf-idf(combination of tf and idf). We will generate all these statistics using `tidytext` package in R.

For example, we are using text from Jane Austen's published novels. The package `janeaustenr` in R provides a collection of 6 different novels by Jane formatted in a convenient form for text analysis. Let's print the total number of lines by each book to get an idea about the length of each document.

For data manipulations, we are uing `dplyr` package.

```
library(tidytext)
library(dplyr)
austen_books() %>% group_by(book) %>%
  summarise(total_lines = n())
```

```
# A tibble: 6 x 2
  book                total_lines
  <fct>                     <int>
1 Sense & Sensibility       12624
2 Pride & Prejudice         13030
3 Mansfield Park            15349
4 Emma                      16235
5 Northanger Abbey           7856
6 Persuasion                 8328
```


# Term frequency(tf)
If you think about a text document, it is nothing but a collection of sentences, and sentences are eventually a collection of words. So for a document, its features are the words. That means a similar set of documents will have related terms. Thus, the frequency of a term is a good measure of how important the word is to a given text. This method is called as term frequency.

It is no surprise that there are going to be words that occur many times but are not relevant; these are words like "is", "the", "that", "this", "of",  and so forth. It is thus vital to clean the text before getting the term-frequency of a document. **To learn more about the text cleaning process and steps read the [Text Pre-Processing](/docs/text-mining/text-pre-processing/) chater**.

Let us check the frequency of most common words used by Jane Austeneach throughout the novels. Here we `unnest_tokens()` function converts the text into `tokens` using tokenizers package. It also coverts the text to lower by default.  We also remove `stopwords` using `anti_join()` from `dplyr` package. The object `stop_words` is present in `tidytext` and contains about 1400+ stopwords. The `count` function counts the occurrence of each word by a novel.

In the next chunk of code, we are also looking at a total number of words by each book. This is achieved by using `group_by()` and `summarise()` functions. Finally, we join the two tables using `left_join()`.


```
library(tidytext)
library(dplyr)

word_frequency <- austen_books() %>%
  unnest_tokens(word, text) %>%
  anti_join(stop_words)%>%
  count(book, word, sort = TRUE)

total_words_per_book <- word_frequency %>%
  group_by(book) %>%
  summarise(total_words = sum(n))

word_frequency <- left_join(word_frequency, total_words_per_book)
word_frequency
```

```
# A tibble: 37,225 x 4
   book                word          n total_words
   <fct>               <chr>     <int>       <int>
 1 Mansfield Park      fanny       816       47968
 2 Emma                emma        786       46775
 3 Sense & Sensibility elinor      623       36330
 4 Emma                miss        599       46775
 5 Pride & Prejudice   elizabeth   597       37246
 6 Mansfield Park      crawford    493       47968
 7 Sense & Sensibility marianne    492       36330
 8 Persuasion          anne        447       25488
 9 Mansfield Park      miss        432       47968
10 Northanger Abbey    catherine   428       23803
# ... with 37,215 more rows
```

Now, let's calculate **term frequency** by dividing `n/total_words` for each novel.

```
freq_by_rank <- word_frequency %>%
  group_by(book) %>%
  mutate(rank = row_number(),
         `term_frequency` = n/total_words)
```

```
# A tibble: 37,225 x 6
# Groups:   book [6]
   book      word       n total_words  rank term_frequency
   <fct>     <chr>  <int>       <int> <int>          <dbl>
 1 Mansfiel~ fanny    816       47968     1        0.0170
 2 Emma      emma     786       46775     1        0.0168
 3 Sense & ~ elinor   623       36330     1        0.0171
 4 Emma      miss     599       46775     2        0.0128
 5 Pride & ~ eliza~   597       37246     1        0.0160
 6 Mansfiel~ crawf~   493       47968     2        0.0103
 7 Sense & ~ maria~   492       36330     2        0.0135
 8 Persuasi~ anne     447       25488     1        0.0175
 9 Mansfiel~ miss     432       47968     3        0.00901
10 Northang~ cathe~   428       23803     1        0.0180
# ... with 37,215 more rows
```

# Inverse document frequency(idf)
**Inverse document frequency** decreases the weight of words that are common and increases the weight of words, which are more important to the document. Here the frequency of each term in a document is divided by the document frequency of that term. In other words, `idf` is a measure of whether a term is common or rare to the given document.

{{< katex display >}}
{idf_{term}} = \ln(\frac {N_{documnets}} {N_{documents\space{containing}\space{terms}}} )
{{< /katex >}}

# Term frequency-inverse document frequecny(tf-idf)
The intention behind `tf-idf` statistic is to measure the importance of a word in a collection of documents.
tf-idf is calculated using the following formula.

{{< katex display >}}
tfidf(t,d,D) = tf(t, d) * idf(t, D)
{{< /katex >}}

Here,
{{< katex display>}}`t` \dashrightarrow term{{< /katex >}}
{{< katex display>}}`d` \dashrightarrow document{{< /katex >}}
{{< katex display>}}`D` \dashrightarrow total\space{number}\space{documents}\space{in}\space{collection}{{< /katex >}}

{{< hint info >}}
**Note**  
In `tidytext` **idf** and **tf-idf** can be calculated using `bind_tf_idf()` function.
{{< /hint >}}

```
word_frequency <- austen_books() %>%
  unnest_tokens(word, text) %>%
  anti_join(stop_words)%>%
  count(book, word, sort = TRUE)

word_tf_idf <- word_frequency %>%
  bind_tf_idf(word, book, n)

word_tf_idf
```
```
# A tibble: 37,225 x 6
   book                word          n      tf   idf  tf_idf
   <fct>               <chr>     <int>   <dbl> <dbl>   <dbl>
 1 Mansfield Park      fanny       816 0.0170  0.693 0.0118
 2 Emma                emma        786 0.0168  1.10  0.0185
 3 Sense & Sensibility elinor      623 0.0171  1.79  0.0307
 4 Emma                miss        599 0.0128  0     0      
 5 Pride & Prejudice   elizabeth   597 0.0160  0.693 0.0111
 6 Mansfield Park      crawford    493 0.0103  1.79  0.0184
 7 Sense & Sensibility marianne    492 0.0135  1.79  0.0243
 8 Persuasion          anne        447 0.0175  0.182 0.00320
 9 Mansfield Park      miss        432 0.00901 0     0      
10 Northanger Abbey    catherine   428 0.0180  0.693 0.0125
```

# Visualizing high tf-idf words
To visualize the plot, we will use `ggplot2` package in R.

```
library(ggplot2)

tf_idf_plot <- word_tf_idf %>%
    arrange(desc(tf_idf)) %>%
    mutate(word = factor(word, levels = rev(unique(word)))) %>%
    group_by(book) %>%
    top_n(10) %>%
    ungroup() %>%
    ggplot(aes(word, tf_idf, fill = book)) +
    geom_col(show.legend = FALSE) +
    labs(x = NULL, y = "tf-idf") +
    facet_wrap(~book, ncol = 3, scales = "free") +
    coord_flip()
```

<figure>
  <img src="/images/wordprocessing/tf_idf_chart.PNG" alt= "Visualizing clusters by original variables">
</figure>

# Bonus - Convert above static chart to interactive chart
You can use `ggplotly()` function to convert any `ggplot2` chart to an interactive chart as given blow:
```
library(plotly)
ggplotly(tf_idf_plot)
```
{{< hint info >}}
**Important Notice**  
Stay updated by subscribing to our newsletter.  
We hate spam just like you.  
{{< /hint>}}

{{< button href="https://www.rstatisticsblog.com/docs/subscribe/" >}}Subscribe{{< /button >}}
