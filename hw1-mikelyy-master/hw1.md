MA 415/615 Homework 1
================
Yuyang Li

In this homework I'm analyzing the `msleep` dataset from package `ggplot2`. I start by loading the packages:

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.0     ✔ purrr   0.2.5
    ## ✔ tibble  2.0.1     ✔ dplyr   0.7.8
    ## ✔ tidyr   0.8.2     ✔ stringr 1.3.1
    ## ✔ readr   1.3.1     ✔ forcats 0.3.0

    ## ── Conflicts ──────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
msleep
```

    ## # A tibble: 83 x 11
    ##    name  genus vore  order conservation sleep_total sleep_rem sleep_cycle
    ##    <chr> <chr> <chr> <chr> <chr>              <dbl>     <dbl>       <dbl>
    ##  1 Chee… Acin… carni Carn… lc                  12.1      NA        NA    
    ##  2 Owl … Aotus omni  Prim… <NA>                17         1.8      NA    
    ##  3 Moun… Aplo… herbi Rode… nt                  14.4       2.4      NA    
    ##  4 Grea… Blar… omni  Sori… lc                  14.9       2.3       0.133
    ##  5 Cow   Bos   herbi Arti… domesticated         4         0.7       0.667
    ##  6 Thre… Brad… herbi Pilo… <NA>                14.4       2.2       0.767
    ##  7 Nort… Call… carni Carn… vu                   8.7       1.4       0.383
    ##  8 Vesp… Calo… <NA>  Rode… <NA>                 7        NA        NA    
    ##  9 Dog   Canis carni Carn… domesticated        10.1       2.9       0.333
    ## 10 Roe … Capr… herbi Arti… lc                   3        NA        NA    
    ## # … with 73 more rows, and 3 more variables: awake <dbl>, brainwt <dbl>,
    ## #   bodywt <dbl>

**`[==[`** Note: instructions are framed with bold `[==[` and `]==]` and should be either removed (like this one!) or substituted by answers in the final version of your document. **`]==]`**

Question 1
----------

**`[==[`** I think the two variables have a relatively positive relationship. And it does seem to be linear after th elog transformations. **`]==]`**

``` r
ggplot(msleep) + geom_point(aes(x = log(bodywt), y = log(brainwt), color= vore))
```

    ## Warning: Removed 27 rows containing missing values (geom_point).

![](hw1_files/figure-markdown_github/q1-1.png)

Question 2
----------

**`[==[`** The larger portions of meat in the diet, the larger body weights of the animal and larger size of mammal as well. **`]==]`**

``` r
ggplot(msleep) + geom_boxplot(aes(x = vore, y = log(bodywt))) + coord_flip()
```

![](hw1_files/figure-markdown_github/q2-1.png)

Question 3
----------

**`[==[`** It seems like there does not exist any noticeable relationship between sleep and conservation status. **`]==]`**

``` r
ggplot(msleep, aes(x= conservation, y= sleep_total/24)) + geom_point(position = "jitter", aes(color=vore))+stat_summary(fun.y=mean, fun.ymin=min, fun.ymax=max, geom="crossbar", color="blue")
```

![](hw1_files/figure-markdown_github/q3-1.png)

Question 4
----------

**`[==[`** Substitute the command in chunk `q4` by a `ggplot` R command that would actually generate the figure. **`]==]`**

``` r
ggplot(msleep) + geom_bar(aes(x=conservation, fill=vore))+coord_polar()
```

![](hw1_files/figure-markdown_github/q4-1.png)
