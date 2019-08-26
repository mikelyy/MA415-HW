MA 415/615 Homework 4
================
Yuyang Li

Question 1
----------

My work is to perform a preliminary analysis of consumer behavior using sales data on **Black Friday**. I downloaded the [BlackFriday](https://www.kaggle.com/mehdidag/black-friday) dataset from Kaggle, and loaded it into a new dataset called `blackfriday`.

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.0     ✔ purrr   0.2.5
    ## ✔ tibble  2.0.1     ✔ dplyr   0.7.8
    ## ✔ tidyr   0.8.2     ✔ stringr 1.3.1
    ## ✔ readr   1.3.1     ✔ forcats 0.3.0

    ## ── Conflicts ──────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
blackfriday <- read_csv("/Users/yuyangli/Desktop/2019 spring/ma415/MA415 HW4 Yuyang Li/data/BlackFriday.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   User_ID = col_double(),
    ##   Product_ID = col_character(),
    ##   Gender = col_character(),
    ##   Age = col_character(),
    ##   Occupation = col_double(),
    ##   City_Category = col_character(),
    ##   Stay_In_Current_City_Years = col_character(),
    ##   Marital_Status = col_double(),
    ##   Product_Category_1 = col_double(),
    ##   Product_Category_2 = col_double(),
    ##   Product_Category_3 = col_double(),
    ##   Purchase = col_double()
    ## )

``` r
problems(blackfriday)
```

    ## [1] row      col      expected actual  
    ## <0 rows> (or 0-length row.names)

``` r
ggplot(blackfriday)+stat_count(aes(x=Age))
```

![](hw4_files/figure-markdown_github/q1%20part2-1.png)

``` r
ggplot(blackfriday)+stat_count(aes(x=Marital_Status))
```

![](hw4_files/figure-markdown_github/q1%20part3-1.png)

``` r
ggplot(blackfriday)+geom_freqpoly(aes(x=Purchase))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](hw4_files/figure-markdown_github/q1%20part4-1.png)

Question 2
----------

There is no apparent difference of purchase prices among different age groups. Marital status has an effect on purchase prices. For example, for 55+ group, the median value for unmarried is higher than married.

``` r
blackfriday %>% unite(agems, Age, Marital_Status) %>% ggplot(aes(x=agems, y=Purchase)) + geom_boxplot()
```

![](hw4_files/figure-markdown_github/q2-1.png)

Question 3
----------

``` r
blackfriday %>% filter(is.na(Product_Category_1))
```

    ## # A tibble: 0 x 12
    ## # … with 12 variables: User_ID <dbl>, Product_ID <chr>, Gender <chr>,
    ## #   Age <chr>, Occupation <dbl>, City_Category <chr>,
    ## #   Stay_In_Current_City_Years <chr>, Marital_Status <dbl>,
    ## #   Product_Category_1 <dbl>, Product_Category_2 <dbl>,
    ## #   Product_Category_3 <dbl>, Purchase <dbl>

``` r
blackfriday %>% filter(is.na(Product_Category_2)) %>% filter(!is.na(Product_Category_3))
```

    ## # A tibble: 0 x 12
    ## # … with 12 variables: User_ID <dbl>, Product_ID <chr>, Gender <chr>,
    ## #   Age <chr>, Occupation <dbl>, City_Category <chr>,
    ## #   Stay_In_Current_City_Years <chr>, Marital_Status <dbl>,
    ## #   Product_Category_1 <dbl>, Product_Category_2 <dbl>,
    ## #   Product_Category_3 <dbl>, Purchase <dbl>

``` r
blackfriday_cat <- blackfriday %>% 
  gather(Product_Category_1, Product_Category_2, Product_Category_3, key = "Prod_Category", value = "Prod_Category_ID") %>%  
  filter(!is.na(Prod_Category_ID))
blackfriday
```

    ## # A tibble: 537,577 x 12
    ##    User_ID Product_ID Gender Age   Occupation City_Category
    ##      <dbl> <chr>      <chr>  <chr>      <dbl> <chr>        
    ##  1 1000001 P00069042  F      0-17          10 A            
    ##  2 1000001 P00248942  F      0-17          10 A            
    ##  3 1000001 P00087842  F      0-17          10 A            
    ##  4 1000001 P00085442  F      0-17          10 A            
    ##  5 1000002 P00285442  M      55+           16 C            
    ##  6 1000003 P00193542  M      26-35         15 A            
    ##  7 1000004 P00184942  M      46-50          7 B            
    ##  8 1000004 P00346142  M      46-50          7 B            
    ##  9 1000004 P0097242   M      46-50          7 B            
    ## 10 1000005 P00274942  M      26-35         20 A            
    ## # … with 537,567 more rows, and 6 more variables:
    ## #   Stay_In_Current_City_Years <chr>, Marital_Status <dbl>,
    ## #   Product_Category_1 <dbl>, Product_Category_2 <dbl>,
    ## #   Product_Category_3 <dbl>, Purchase <dbl>

Question 4
----------

The pattern is not coherent. For example, for 8 and 1, they all have higher purchase and higher purchasenumber. And the case is reversed for 11 and 12 with higher purchasenumber and higher purchase as well. Product like 8 and 5 have larger outliers than others. This operation is easier after processing because it is more straightforward and much easier to plot the graph and show the result by putting them into one column.

``` r
blackfriday_cat %>% 
  group_by(Prod_Category_ID) %>% 
  mutate(purchasenumber=n()) %>% 
  ggplot(aes(x=reorder(factor(Prod_Category_ID), Purchase), y=Purchase)) +
  geom_boxplot(aes(fill=purchasenumber))
```

![](hw4_files/figure-markdown_github/q4-1.png)
