Homework1
================
Christine Hou
2022-09-26

### Input Preparation

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(dbplyr)
```

    ## 
    ## Attaching package: 'dbplyr'
    ## 
    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     ident, sql

``` r
library(nycflights13)
```

### 1. How many flights have a missing dep_time? What other variables are missing? What might these rows represent?

``` r
sum(is.na(flights$dep_time))
```

    [1] 8255

There are 8255 flights are missing.

``` r
flights %>% filter(is.na(dep_time))
```

    # A tibble: 8,255 × 19
        year month   day dep_time sched_de…¹ dep_d…² arr_t…³ sched…⁴ arr_d…⁵ carrier
       <int> <int> <int>    <int>      <int>   <dbl>   <int>   <int>   <dbl> <chr>  
     1  2013     1     1       NA       1630      NA      NA    1815      NA EV     
     2  2013     1     1       NA       1935      NA      NA    2240      NA AA     
     3  2013     1     1       NA       1500      NA      NA    1825      NA AA     
     4  2013     1     1       NA        600      NA      NA     901      NA B6     
     5  2013     1     2       NA       1540      NA      NA    1747      NA EV     
     6  2013     1     2       NA       1620      NA      NA    1746      NA EV     
     7  2013     1     2       NA       1355      NA      NA    1459      NA EV     
     8  2013     1     2       NA       1420      NA      NA    1644      NA EV     
     9  2013     1     2       NA       1321      NA      NA    1536      NA EV     
    10  2013     1     2       NA       1545      NA      NA    1910      NA AA     
    # … with 8,245 more rows, 9 more variables: flight <int>, tailnum <chr>,
    #   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
    #   minute <dbl>, time_hour <dttm>, and abbreviated variable names
    #   ¹​sched_dep_time, ²​dep_delay, ³​arr_time, ⁴​sched_arr_time, ⁵​arr_delay

The flights with missing `dep_time`, the variables including
`dep_delay`,`arr_time`,`arr_delay`,`air_time` are also missing. This
actually means that these flights are cancelled by airlines.

### 2. Currently dep_time and sched_dep_time are convenient to look at, but hard to compute with because they’re not really continuous numbers. Convert them to a more convenient representation of number of minutes since midnight.

``` r
flights = mutate(flights,
                   dep_time_minutes = dep_time %/% 100 * 60 + dep_time %% 100,
                   sched_dep_time_minutes = sched_dep_time %/% 100 * 60 + sched_dep_time %% 100)
select(flights, starts_with('dep_time'), starts_with('sched'))
```

    ## # A tibble: 336,776 × 5
    ##    dep_time dep_time_minutes sched_dep_time sched_arr_time sched_dep_time_minu…¹
    ##       <int>            <dbl>          <int>          <int>                 <dbl>
    ##  1      517              317            515            819                   315
    ##  2      533              333            529            830                   329
    ##  3      542              342            540            850                   340
    ##  4      544              344            545           1022                   345
    ##  5      554              354            600            837                   360
    ##  6      554              354            558            728                   358
    ##  7      555              355            600            854                   360
    ##  8      557              357            600            723                   360
    ##  9      557              357            600            846                   360
    ## 10      558              358            600            745                   360
    ## # … with 336,766 more rows, and abbreviated variable name
    ## #   ¹​sched_dep_time_minutes

### 3. Look at the number of canceled flights per day. Is there a pattern? Is the proportion of canceled flights related to the average delay? Use multiple dyplr operations, all on one line, concluding with `ggplot(aes(x= ,y=)) + geom_point()`
