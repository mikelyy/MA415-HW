MA 415/615 Homework 7
================
Yuyang Li

Question 1
----------

``` r
library(nycflights13) 
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
library(geosphere)
dist_geo <- function (lon_from, lat_from, lon_to, lat_to) {
  geosphere::distGeo(cbind(lon_from, lat_from), c(lon_to, lat_to))
}
JFK <- airports %>% filter(faa == 'JFK') %>% select(lon, lat)
airport_w_dist <- airports %>% mutate(dist = dist_geo(lon, lat, JFK$lon, JFK$lat))
flights_dist <- flights %>% inner_join(airport_w_dist, flights, by = c("dest"="faa"))
flights_dist %>% ggplot(aes(x=log(dist), y=log(distance)))+geom_line()
```

![](hw7_files/figure-markdown_github/q1-1.png)

Question 2
----------

``` r
flights_mt <- flights_dist %>% filter(!is.na(dep_delay) & !is.na(arr_delay)) %>% mutate(madeup_time = dep_delay - arr_delay)

linearmodel <- lm(madeup_time ~ log(dist), data = flights_mt)
beta <- coef(linearmodel)
flights_mt %>% ggplot(aes(x=log(dist), y=madeup_time))+geom_point()+geom_abline(intercept=beta[1],slope=beta[2],color="red",alpha=0.3)
```

![](hw7_files/figure-markdown_github/q2-1.png)

``` r
# based on the formula: 30-arr_delay = beta[1] + beta[2] * 15.19
# 30-beta[1]-(beta[2]*15.19)
(answer <- 30-beta[1]-(beta[2]*15.19))
```

    ## (Intercept) 
    ##    21.96295

Question 3
----------

``` r
rep_flights <- flights %>% group_by(carrier) %>% filter(n_distinct(flight) > 100) %>% group_by(carrier, flight) %>% filter(n()>50) %>% ungroup()
flights_load <- flights %>% group_by(month, day, origin) %>% summarize(trips=n())
flightdb2 <- inner_join(flights_load, rep_flights, by = c("month", "day","origin"))
flightdb2 <- flightdb2 %>% mutate(ontime = ifelse(!is.na(dep_delay) & !is.na(arr_delay) & arr_delay <= 15, 1,0))
flightdb2
```

    ## # A tibble: 271,632 x 21
    ## # Groups:   month, day [365]
    ##    month   day origin trips  year dep_time sched_dep_time dep_delay
    ##    <int> <int> <chr>  <int> <int>    <int>          <int>     <dbl>
    ##  1     1     1 EWR      305  2013      517            515         2
    ##  2     1     1 EWR      305  2013      555            600        -5
    ##  3     1     1 EWR      305  2013      606            610        -4
    ##  4     1     1 EWR      305  2013      607            607         0
    ##  5     1     1 EWR      305  2013      608            600         8
    ##  6     1     1 EWR      305  2013      615            615         0
    ##  7     1     1 EWR      305  2013      628            630        -2
    ##  8     1     1 EWR      305  2013      632            608        24
    ##  9     1     1 EWR      305  2013      644            636         8
    ## 10     1     1 EWR      305  2013      646            645         1
    ## # … with 271,622 more rows, and 13 more variables: arr_time <int>,
    ## #   sched_arr_time <int>, arr_delay <dbl>, carrier <chr>, flight <int>,
    ## #   tailnum <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
    ## #   minute <dbl>, time_hour <dttm>, ontime <dbl>

The linear model verifies the assumption. The larger the distance, the smaller the arr\_delay.

Question 4
----------

``` r
rep_flights <- flights %>%
  group_by(carrier) %>% filter(n_distinct(flight) > 100) %>%
  group_by(carrier, flight) %>% filter(n() > 50) %>% ungroup()
```

``` r
flights_load <- rep_flights %>% group_by(origin,month,day) %>% summarize(trips=n())
flights_ontime <- inner_join(rep_flights,flights_load,by=c("origin","month","day"))%>%mutate(ontime=ifelse(!is.na(arr_delay)&arr_delay<=15,1,0))
flights_load
```

    ## # A tibble: 1,095 x 4
    ## # Groups:   origin, month [?]
    ##    origin month   day trips
    ##    <chr>  <int> <int> <int>
    ##  1 EWR        1     1   181
    ##  2 EWR        1     2   216
    ##  3 EWR        1     3   216
    ##  4 EWR        1     4   224
    ##  5 EWR        1     5   130
    ##  6 EWR        1     6   191
    ##  7 EWR        1     7   230
    ##  8 EWR        1     8   225
    ##  9 EWR        1     9   233
    ## 10 EWR        1    10   235
    ## # … with 1,085 more rows

Question 5
----------

**`[==[`**

Finally, fit a logistic regression model of `ontime` vs `trips` and `carrier`, but without an intercept so we can see the direct effect of each carrier on the odds of a trip being on-time. List the coefficients; what's the effect of `trips`? Is it what you expected? If flying Delta, how many trips would be needed for your predicted probability of the flight being on-time to be 50%?

**`]==]`**

``` r
summary(glm(ontime~trips+carrier-1, data=flights_ontime, family=binomial))
```

    ## 
    ## Call:
    ## glm(formula = ontime ~ trips + carrier - 1, family = binomial, 
    ##     data = flights_ontime)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -2.0353  -1.3805   0.6982   0.8040   1.0179  
    ## 
    ## Coefficients:
    ##            Estimate Std. Error z value Pr(>|z|)    
    ## trips     -0.003731   0.000134  -27.84   <2e-16 ***
    ## carrier9E  1.860530   0.040702   45.71   <2e-16 ***
    ## carrierAA  2.290753   0.036979   61.95   <2e-16 ***
    ## carrierB6  1.978318   0.036673   53.95   <2e-16 ***
    ## carrierDL  2.399162   0.036557   65.63   <2e-16 ***
    ## carrierEV  1.525424   0.035119   43.44   <2e-16 ***
    ## carrierMQ  1.753978   0.036482   48.08   <2e-16 ***
    ## carrierUA  2.155223   0.035850   60.12   <2e-16 ***
    ## carrierUS  2.244955   0.038820   57.83   <2e-16 ***
    ## carrierWN  1.895400   0.051846   36.56   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 376562  on 271632  degrees of freedom
    ## Residual deviance: 305331  on 271622  degrees of freedom
    ## AIC: 305351
    ## 
    ## Number of Fisher Scoring iterations: 4

``` r
ontime_coef<-coef(glm(ontime~trips+carrier,data=flights_ontime,family=binomial))
(ontime_coef[5]-0.5/ontime_coef[2])
```

    ## carrierDL 
    ##   134.541

The coefficient of trips is -0.003731. Around 135 trips are needed for probability of the flight being on-time to be 50%.
