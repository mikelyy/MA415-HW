MA 415/615 Homework 3
================
Yuyang Li

Question 1
----------

My work is to perform a preliminary EDA of the **real state properties** in Boston in 2018. To this end, I downloaded the "Property Assessment FY2018" data file from the [Analyze Boston](https://data.boston.gov/dataset/property-assessment) web page, and loaded it into a new dataset called `property`.

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
property <- read_csv("/Users/yuyangli/Desktop/2019 spring/ma415/MA415 HW3 Yuyang Li/data/ast2018full.csv",col_types=cols_only(PTYPE=col_integer(),LU=col_character(),GROSS_TAX=col_integer(),NUM_FLOORS=col_double()))
problems(property)
```

    ## [1] row      col      expected actual  
    ## <0 rows> (or 0-length row.names)

Question 2
----------

There are many "unusual" values, including tax exempt properties. Next, I'm filtering out these cases to get a new dataset, `property_rep`.

``` r
property_rep <- property %>% group_by(PTYPE) %>% filter(median(GROSS_TAX)<100000000 & n()>=5) %>% filter(GROSS_TAX!=0) %>% ungroup()
property_rep
```

    ## # A tibble: 153,652 x 4
    ##    PTYPE LU    GROSS_TAX NUM_FLOORS
    ##    <int> <chr>     <int>      <dbl>
    ##  1   105 R3       506289          3
    ##  2   105 R3       557641          3
    ##  3   105 R3       507546          3
    ##  4   105 R3       484910          3
    ##  5   104 R2       494552          3
    ##  6   105 R3       595474          3
    ##  7   105 R3       640852          3
    ##  8   105 R3       658354          3
    ##  9   105 R3       549362          3
    ## 10   105 R3       316496          3
    ## # … with 153,642 more rows

Question 3
----------

To summarize what I have in `property_rep`, here is a table with the proportions of properties by land use (`LU`), ordered by proportion, and the cumulative proportions.

``` r
property_rep %>% group_by(LU) %>% summarize(landuse=n()) %>% mutate(proportion=landuse/sum(landuse), cumulativeproportions=cumsum(proportion)) %>% arrange(proportion,cumulativeproportions)
```

    ## # A tibble: 13 x 4
    ##    LU    landuse proportion cumulativeproportions
    ##    <chr>   <int>      <dbl>                 <dbl>
    ##  1 I         430    0.00280                0.517 
    ##  2 CC       1794    0.0117                 0.0583
    ##  3 CL       1982    0.0129                 0.479 
    ##  4 R4       2576    0.0168                 0.937 
    ##  5 A        2858    0.0186                 0.0186
    ##  6 RC       2949    0.0192                 0.956 
    ##  7 C        4305    0.0280                 0.0466
    ##  8 CP       5479    0.0357                 0.515 
    ##  9 RL       6713    0.0437                 1     
    ## 10 R3      14050    0.0914                 0.920 
    ## 11 R2      17302    0.113                  0.829 
    ## 12 R1      30567    0.199                  0.716 
    ## 13 CD      62647    0.408                  0.466

'LU' codes with the highest counts is 'CD', which is residential condominium unit. I think they are expected because in Boston, there are lots of college students. Students would choose residential condominium unit as it is convenient and not too expensive for students.

Question 4
----------

Finally, I summarize my findings with log gross tax boxplots for each land use.

``` r
property_rep %>% group_by(LU) %>% mutate(landuse=n()) %>% ggplot(aes(x=reorder(LU, GROSS_TAX, FUN=median), y=log(GROSS_TAX)))+geom_boxplot(aes(fill=landuse))
```

![](hw3_files/figure-markdown_github/q4-1.png)
