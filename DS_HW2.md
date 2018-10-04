Homework\_2
================
Apoorva Srinivasan
10/2/2018

``` r
library(tidyverse)
```

    ## ── Attaching packages ──────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.0.0     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.6
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ─────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(ggplot2)
library(readxl)
library(p8105.datasets)
```

``` r
nyc_transit = read_csv(file = "./hw2_data/NYC_Transit_Subway_Entrance_And_Exit_Data.csv") 
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   `Station Latitude` = col_double(),
    ##   `Station Longitude` = col_double(),
    ##   Route8 = col_integer(),
    ##   Route9 = col_integer(),
    ##   Route10 = col_integer(),
    ##   Route11 = col_integer(),
    ##   ADA = col_logical(),
    ##   `Free Crossover` = col_logical(),
    ##   `Entrance Latitude` = col_double(),
    ##   `Entrance Longitude` = col_double()
    ## )

    ## See spec(...) for full column specifications.

``` r
  nyc_transit = janitor::clean_names(nyc_transit) %>%
    select(line:entry, vending, ada) %>%
    mutate(entry = recode(entry, "YES" = TRUE, "NO" = FALSE ))
```

We're given a dataset containing information about nyc transit subway entrance and exit details. After editing out what is not required for this homework, we now have a new dataset with variable that are line, station name, station lattitude, longitude, route 1 to 11, entrance type, entry, vending and compliance with ADA.

Data cleaning steps

-   Imported data using read\_csv through relative path to ensure reproducability
-   Cleaned the variable names using the janitor function which converted all column names into lower snake case.
-   Retained the required variables using select to declutter our data.
-   Converted the entry variable from character to logical variable using recode.

The dimensions of the data set are 1868 x 19.

Are these data tidy?

No, the data doesn't look tidy because the data for the route variable are spread across eleven columns which makes it difficult to read.

Further questions:

How many distinct stations are there?

``` r
distinct(nyc_transit, line, station_name, .keep_all = TRUE) %>%
  nrow()
```

    ## [1] 465

PROBLEM 2
