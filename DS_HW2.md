Homework\_2
================
Apoorva Srinivasan
10/2/2018

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.0.0     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.6
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ──────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(ggplot2)
library(readxl)
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

**Further questions:**

How many distinct stations are there?

``` r
distinct(nyc_transit, line, station_name, .keep_all = TRUE) %>%
  nrow()
```

    ## [1] 465

How many stations are ADA compliant?

``` r
 nyc_transit %>%
  distinct(station_name, line, .keep_all = TRUE) %>% 
  filter(ada == TRUE) %>% 
  nrow()
```

    ## [1] 84

No. of ADA stations are 84

What proportion of station entrances / exits without vending allow entrance?

``` r
no_vending = nyc_transit %>%
  filter(vending == "NO")
  proportion_no_vending = mean(no_vending$entry == TRUE)
  knitr::kable(proportion_no_vending)
```

|          x|
|----------:|
|  0.3770492|

Therefore, 37.7% of stations with entrance don't have vending machines.

Reformating the data

``` r
nyc_transit = gather(nyc_transit, key = route_number, value = route_name, route1:route11) %>%
  separate(route_number, into = c("route_str", "route_number"), sep = 5) %>%
  select(-route_str)
```

How many distinct stations serve the A train?

``` r
  nyc_transit %>%
  distinct(station_name, line, .keep_all = TRUE) %>%
  filter(route_name == "A") %>%
  nrow()
```

    ## [1] 60

Of the stations that serve the A train, how many are ADA compliant?

``` r
nyc_transit %>%
  distinct(station_name, line, .keep_all = TRUE) %>%
  filter(route_name == "A", ada == "TRUE") %>%
  nrow()
```

    ## [1] 17

**PROBLEM 2**

**PROBLEM 3**

``` r
library(p8105.datasets)

#View(p8105.datasets::brfss_smart2010)
```

``` r
brfss_data = p8105.datasets::brfss_smart2010 %>%
  janitor::clean_names() %>%
  rename(resp_id = respid, location_abbreviation = locationabbr, location_desc = locationdesc) %>% 
  filter(topic == "Overall Health") %>%
  select(-class, -topic, -question, -sample_size, -c(confidence_limit_low:geo_location)) %>%
  spread(key = response, value = data_value) %>%
  janitor::clean_names() %>%
  mutate(excellent_or_verygood = excellent + very_good)
```
