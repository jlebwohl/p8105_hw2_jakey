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
characters. The data set is 1868 rows long and 19 columns wide. These
data are not tidy: routes served should not be broken up into 11
variables (route1, route2, etc.): it should be broken up into route
number and route name.

Keeping only distinct values of the dataset:

``` r
nyc_subways_distinct = 
  nyc_subways %>% 
  distinct(line, station_name, .keep_all = TRUE)
```

Using this new dataframe, we can find that there are 465 stations.

The number of stations which are ADA compliant is 84, which can be found
using the code `sum(nyc_subways_distinct$ada == TRUE)`.

Filtering only the stations without vending:

``` r
nyc_subways_novending = 
  nyc_subways %>% 
  filter(vending == "NO")
```

Using `sum(nyc_subways_novending$entry == TRUE)` /
`nrow(nyc_subways_novending)`, we find that the proportion of station
entrances / exits without vending which allow entrance is 69 / 183 =
0.3770492.

Reformating + tidying data for routes. First we need to convert some
routes from integer values to strings:

``` r
nyc_subways_tidy = 
  nyc_subways_distinct %>% 
  transform(route8 = as.character(route8)) %>% 
  transform(route9 = as.character(route9)) %>% 
  transform(route10 = as.character(route10)) %>% 
  transform(route11 = as.character(route11)) %>% 
  pivot_longer(
    route1:route11,
    names_to = "route_number",
    names_prefix = "route",
    values_to = "route_name"
  )
```

Filtering distinct stations which serve the A train.

``` r
nyc_subways_atrain = 
  nyc_subways_tidy %>% 
  filter(route_name == "A") %>% 
  distinct(line, station_name, .keep_all = TRUE)
```

There are 60 distinct stations which serve the A train. Of these 17 are
ADA compliant.

## Problem 2

I got bored

## Problem 3

Import and prep data

``` r
pols_month = 
  read_csv("./data/fivethirtyeight_datasets/pols-month.csv") %>% 
  janitor::clean_names() %>% 
  separate(mon, into = c("year", "month", "day")) %>% 
  mutate(president = ifelse(prez_gop == 1, "gop", "dem")) %>% 
  mutate(month = snakecase::to_snake_case(month.abb[as.numeric(month)])) %>% 
  mutate(year = as.numeric(year)) %>% 
  select(!c(day, prez_gop, prez_dem)) %>% 
  janitor::clean_names()
```

    ## Rows: 822 Columns: 9
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (8): prez_gop, gov_gop, sen_gop, rep_gop, prez_dem, gov_dem, sen_dem, r...
    ## date (1): mon
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
snp_month =
  read_csv("./data/fivethirtyeight_datasets/snp.csv") %>% 
  janitor::clean_names() %>% 
  separate(date, into = c("month", "day", "year")) %>% 
  mutate(year = as.numeric(year)) %>% 
  mutate(year = ifelse(year <= 15, year + 2000, year + 1900)) %>% 
  arrange(year, month) %>% 
  mutate(month = snakecase::to_snake_case(month.abb[as.numeric(month)])) %>% 
  select(!day) %>% 
  relocate(year, month) %>% 
  janitor::clean_names()
```

    ## Rows: 787 Columns: 2
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): date
    ## dbl (1): close
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
unemployment_month =
  read_csv("./data/fivethirtyeight_datasets/unemployment.csv") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    jan:dec,
    names_to = "month",
    values_to = "unemployment"
  )
```

    ## Rows: 68 Columns: 13
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (13): Year, Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
full_df = 
  left_join(pols_month, snp_month, by = c("year", "month")) %>% 
  left_join(unemployment_month, by = c("year", "month"))
```
