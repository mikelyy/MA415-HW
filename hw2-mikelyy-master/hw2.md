MA 415/615 Homework 2
================
Yuyang Li

In this homework, we're performing a more detailed analysis of the `nycflights13` dataset we saw in lecture. This data set contains flight information from all flghts that departed NYC in 2013. We start by loading the packages:

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.0     ✔ purrr   0.2.5
    ## ✔ tibble  2.0.1     ✔ dplyr   0.7.8
    ## ✔ tidyr   0.8.2     ✔ stringr 1.3.1
    ## ✔ readr   1.3.1     ✔ forcats 0.3.0

    ## ── Conflicts ───────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(nycflights13)
```

Question 1
----------

Here are the top 4 carriers in total number of flights departing NYC in 2013:

``` r
topfourcarriers <- flights %>% group_by(carrier) %>% summarize(count=n()) %>% filter(rank(desc(count))<=4)
print(topfourcarriers)
```

    ## # A tibble: 4 x 2
    ##   carrier count
    ##   <chr>   <int>
    ## 1 B6      54635
    ## 2 DL      48110
    ## 3 EV      54173
    ## 4 UA      58665

``` r
(big_carriers <- c("B6", "DL", "EV", "UA"))
```

    ## [1] "B6" "DL" "EV" "UA"

Question 2
----------

Here are the proportion of canceled flights for the top 4 carriers at each origin airport:

``` r
flights %>% filter(carrier %in% big_carriers) %>%
  group_by(origin, carrier) %>%
  summarize(prop_cancelled = mean(is.na(dep_delay) & is.na(arr_delay))) %>%
  arrange(origin,desc(prop_cancelled))
```

    ## # A tibble: 12 x 3
    ## # Groups:   origin [3]
    ##    origin carrier prop_cancelled
    ##    <chr>  <chr>            <dbl>
    ##  1 EWR    EV             0.0493 
    ##  2 EWR    B6             0.0113 
    ##  3 EWR    UA             0.00944
    ##  4 EWR    DL             0.00898
    ##  5 JFK    EV             0.0582 
    ##  6 JFK    UA             0.00970
    ##  7 JFK    B6             0.00749
    ##  8 JFK    DL             0.00483
    ##  9 LGA    EV             0.0647 
    ## 10 LGA    UA             0.0257 
    ## 11 LGA    B6             0.0128 
    ## 12 LGA    DL             0.00910

For carrier EV, it seems like to be cancelled more often than B6, UA AND DL. And carrier DL seems like to be cancelled the least often. And the origin LGA seems to have more prop\_cancelled in total than EWR and JFK.

Question 3
----------

To investigate if there is any pattern of flight cancellations depending on the time of year, I first create a `canceled_by_doy` dataset with the proportion of canceled flights for each day of the year (`doy`) and `origin`:

``` r
flights %>% transmute(doy = as.integer(strftime(time_hour, format = "%j")))
```

    ## # A tibble: 336,776 x 1
    ##      doy
    ##    <int>
    ##  1     1
    ##  2     1
    ##  3     1
    ##  4     1
    ##  5     1
    ##  6     1
    ##  7     1
    ##  8     1
    ##  9     1
    ## 10     1
    ## # … with 336,766 more rows

``` r
cancelled_by_doy <- flights %>% mutate(doy=as.integer(strftime(time_hour, format = "%j"))) %>%
  group_by(doy,origin) %>% summarize(prop_cancelled=mean(is.na(dep_delay) & is.na(arr_delay)))
print(cancelled_by_doy)
```

    ## # A tibble: 1,095 x 3
    ## # Groups:   doy [?]
    ##      doy origin prop_cancelled
    ##    <int> <chr>           <dbl>
    ##  1     1 EWR           0.00328
    ##  2     1 JFK           0.00337
    ##  3     1 LGA           0.00833
    ##  4     2 EWR           0.0171 
    ##  5     2 JFK           0.00312
    ##  6     2 LGA           0.00368
    ##  7     3 EWR           0.00893
    ##  8     3 JFK           0      
    ##  9     3 LGA           0.0269 
    ## 10     4 EWR           0.00590
    ## # … with 1,085 more rows

Question 4
----------

``` r
ggplot(cancelled_by_doy, aes(x=doy, y=prop_cancelled))+geom_point(aes(color=origin), alpha=0.3)+geom_smooth(aes(color=origin),se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](hw2_files/figure-markdown_github/q4-1.png)

The overall shape of curves shows linearity and coherence. And for unusual days, while doy is between 35 to 50, it shows a relatively high prop\_cancelled around 0.6. While doy is in other ranges, it shows a relatively flat and stable level of prop\_cancelled which is around 0.1. And the shape of curves is flat with relative increase during the middle of the year and the end of the year.
