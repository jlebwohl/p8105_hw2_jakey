Homework 2
================
Jakey Lebwohl
2023-07-08

Always load tidyverse.

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## Problem 1

Import the NYC transit data and retain relevant variables. Also clean it
a little.

``` r
nyc_subways =
  read.csv("./data/NYC_Transit_Subway_Entrance_And_Exit_Data (1).csv") %>% 
  janitor::clean_names() %>% 
  select(line, station_name, station_latitude, station_longitude, (route1:route11), entry, vending, entrance_type, ada) %>% 
  mutate(entry = recode(entry, "YES" = TRUE, "NO" = FALSE))
```

This dataset contains data for line, station, name, station latitude /
longitude, routes served, entry, vending, entrance type, and ADA
compliance for subway stations in NYC. So far I have cleaned the names
of the variables, retained only certain variables, and changed the
values of the variable ‘entry’ to be logical variables instead of
characters. The data set is 1868 rows long and 19 columns wide.
