MA 415/615 Homework 5
================
Yuyang Li

Question 1
----------

In this work I'll analyze the titles of papers presented at the NIPS conference from 1987 to 2017. The data in file `nips-titles.csv`, loaded into table `papers`, contains only two columns, `year` and `title` from the [original Kaggle dataset](https://www.kaggle.com/benhamner/nips-papers). As an initial check, I'm plotting the distribution of the number of words in a title for each year.

``` r
library(tibble) 
library(tidyr) 
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.0     ✔ dplyr   0.7.8
    ## ✔ readr   1.3.1     ✔ stringr 1.3.1
    ## ✔ purrr   0.2.5     ✔ forcats 0.3.0

    ## ── Conflicts ──────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
papers <- read.csv("/Users/yuyangli/Desktop/2019 spring/ma415/MA415 HW5 Yuyang Li/data/nips-titles.csv")
```

``` r
problems(papers)
```

    ## [1] row      col      expected actual  
    ## <0 rows> (or 0-length row.names)

``` r
papers <- papers %>% mutate(word_count=str_count(title,"\\w+\\b"))
```

``` r
papers %>% ggplot(aes(factor(year),word_count))+geom_boxplot()+coord_flip()
```

![](hw5_files/figure-markdown_github/q1%20part4-1.png)

There is no clear trend on the variability of title sizes over the years. Overall, there are some variations.

Question 2
----------

    ## # A tibble: 10 x 10
    ##     year bayesian  data  deep learning models networks neural optimization
    ##    <int>    <int> <int> <int>    <int>  <int>    <int>  <int>        <int>
    ##  1  2008       13    11     1       51     22       10      7            6
    ##  2  2009       20     9     4       61     24        6      7            7
    ##  3  2010       12    14     3       65     19       14      8            9
    ##  4  2011        9    15     3       79     25        9     11           10
    ##  5  2012       22    12    10       92     31       15     14           12
    ##  6  2013       20    18    13       77     33       18     17           17
    ##  7  2014       18    20    22       85     41       25     28           16
    ##  8  2015       16    16    21       78     36       33     22           31
    ##  9  2016       23    24    44      121     43       64     47           39
    ## 10  2017       20    35    64      180     38       69     64           29
    ## # … with 1 more variable: stochastic <int>

``` r
keywords <- tibble(keyword = c("bayesian", "data", "deep", "models",
              "networks", "neural", "learning", "optimization", "stochastic"))

papers_condensed <- papers %>% crossing(keywords) %>% mutate(keyword_in_title=str_detect(str_to_lower(title),keyword)) %>% count(year,keyword,wt=keyword_in_title)
papers_condensed %>% spread(keyword,n) %>% tail(n=10)
```

    ## # A tibble: 10 x 10
    ##     year bayesian  data  deep learning models networks neural optimization
    ##    <int>    <int> <int> <int>    <int>  <int>    <int>  <int>        <int>
    ##  1  2008       13    11     1       51     22       10      7            6
    ##  2  2009       20     9     4       61     24        6      7            7
    ##  3  2010       12    14     3       65     19       14      8            9
    ##  4  2011        9    15     3       79     25        9     11           10
    ##  5  2012       22    12    10       92     31       15     14           12
    ##  6  2013       20    18    13       77     33       18     17           17
    ##  7  2014       18    20    22       85     41       25     28           16
    ##  8  2015       16    16    21       78     36       33     22           31
    ##  9  2016       23    24    44      121     43       64     47           39
    ## 10  2017       20    35    64      180     38       69     64           29
    ## # … with 1 more variable: stochastic <int>

Question 3
----------

``` r
papers_condensed %>% ggplot(aes(year,n))+geom_line(aes(color=keyword))
```

![](hw5_files/figure-markdown_github/q3-1.png)

Learning seems to be the most popular keyword after 1994 and neural is the most popular keyword before 1994. Neural was declining first and then increasing later.

Question 4
----------

``` r
papers_year <- papers %>% group_by(year) %>% summarize(paper_count=n())

left_join(papers_year,papers_condensed,'year') %>% mutate(prop=n/paper_count) %>% ggplot(aes(year,prop))+geom_line(aes(color=keyword))
```

![](hw5_files/figure-markdown_github/q4-1.png)

The conclusion does not change a lot. Learning is still the most popular after 1994. And maybe while looking at proportion graph, some keywords show more variations.
