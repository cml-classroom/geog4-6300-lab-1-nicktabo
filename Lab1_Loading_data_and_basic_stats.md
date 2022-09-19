Geog4/6300: Lab 1
================

## Loading data into R, data transformation, and summary statistics

Your name: Nick Taborsak

**Overview:**

This lab is intended to assess your ability to use R to load data and to
generate basic descriptive statistics. You’ll be using monthly weather
data from the Daymet climate database (<http://daymet.ornl.gov>) for all
counties in the United States over a 11 year period (2010-2021). These
data are available on the Github repo for our course. The following
variables are provided:

-   cty_txt: Code for joining to census data
-   year: Year of observation (with an initial “Y” to make it a
    character)
-   month: Month of observation (1=Jan, 2=Feb, etc.)
-   median_tmax: Median maximum recorded temperature (Celsius)
-   median_tmin: Median minimum recorded temperature (Celsius)
-   sum_prcp: Total recorded prcpitation for the month (mm)
-   cty_name: Name of the county
-   state: state of the county
-   region: Census region (map:
    <https://www2.census.gov/geo/pdfs/maps-data/maps/reference/us_regdiv.pdf>)
-   division: Census division
-   X: Longitude of the county centroid
-   Y: Latitude of the county centroid

These labs are meant to be done collaboratively, but your final
submission should demonstrate your own original thought (don’t just copy
your classmate’s work or turn in identical assignments). Your answers to
the lab questions should be typed in the provided RMarkdown template.
You’ll then “knit” this to an Github document and upload it to your
class Github repo.

**Procedure:**

Load the tidyverse package and import the data:

``` r
library(tidyverse)
daymet_data <- read_csv("data/daymet_monthly_median_2010-2021.csv")
```

We can look at the first few rows of the dataset using the *head*
function. We also use *kable* to format this nicely.

``` r
kable(head(daymet_data))
```

| cty_txt | year  | month | median_tmax | median_tmin | sum_prcp | cty_name          | state  | region      | division         |         x |        y |
|:--------|:------|------:|------------:|------------:|---------:|:------------------|:-------|:------------|:-----------------|----------:|---------:|
| G02060  | Y2010 |     1 |       -4.27 |      -10.83 |    10.04 | Bristol Bay       | Alaska | West Region | Pacific Division | -156.7011 | 58.74213 |
| G02185  | Y2010 |     1 |      -20.73 |      -28.20 |     0.00 | North Slope       | Alaska | West Region | Pacific Division | -153.4411 | 69.30696 |
| G02180  | Y2010 |     1 |      -16.50 |      -23.72 |     5.75 | Nome              | Alaska | West Region | Pacific Division | -163.9703 | 64.89492 |
| G02050  | Y2010 |     1 |      -11.20 |      -18.90 |    24.55 | Bethel            | Alaska | West Region | Pacific Division | -159.7678 | 60.92187 |
| G02261  | Y2010 |     1 |      -13.93 |      -20.03 |    15.84 | Valdez-Cordova    | Alaska | West Region | Pacific Division | -144.4573 | 61.57080 |
| G02170  | Y2010 |     1 |       -5.10 |      -12.42 |    35.84 | Matanuska-Susitna | Alaska | West Region | Pacific Division | -149.5702 | 62.31653 |

**Question 1:** After loading the file into R, pick TWO variables and
determine whether they are nominal, ordinal, interval, and ratio data
using the course readings and lectures as reference. Justify each
classification in a sentence or two.

{Variable 1: region - nominal data; no implied ranking/ordering,
qualitative in nature, classified into mutually exclusive categories.

Variable 2: median_tmax - interval data; measured along a scale with
equal intervals, 0 is arbitrary on this scale (degrees Celsius), values
can be compared by intervals.}

There are a lot of observations here, 452,448, to be exact. To get a
better grasp on it, we can use group_by and summarise in the tidyverse
package, which we covered in class. This will allow us to identify the
mean value for each year by county across the study period.

**Question 2:** Use group_by and summarise to calculate the mean minimum
temperature for each year *by county* across all months, also including
State and Region as grouping variables. Your resulting dataset should
show the value of tmin for each county in each year. Use the kable and
head functions as shown above to call the resulting table.

``` r
tmin_mean<-daymet_data %>%
  group_by(year, month, cty_name, state, region) %>%
  summarise(tmin_mean=mean(median_tmin))
```

    ## `summarise()` has grouped output by 'year', 'month', 'cty_name', 'state'. You
    ## can override using the `.groups` argument.

``` r
kable(head(tmin_mean))
```

| year  | month | cty_name  | state          | region         | tmin_mean |
|:------|------:|:----------|:---------------|:---------------|----------:|
| Y2010 |     1 | Abbeville | South Carolina | South Region   |     -2.40 |
| Y2010 |     1 | Acadia    | Louisiana      | South Region   |      3.28 |
| Y2010 |     1 | Accomack  | Virginia       | South Region   |     -2.22 |
| Y2010 |     1 | Ada       | Idaho          | West Region    |     -1.11 |
| Y2010 |     1 | Adair     | Iowa           | Midwest Region |    -14.99 |
| Y2010 |     1 | Adair     | Kentucky       | South Region   |     -7.72 |

**Question 3:** What if we were only interested in the South Region?
Filter the original data frame (daymet_data) to just include counties in
this region. Then calculate the mean minimum temperature by year *for
each state.* For an optional extra challenge, use the round function to
include only 1 decimal point. You can type ?round in the console to find
documentation on this function. Use kable and head to call the first few
lines of the resulting table

``` r
daymet_south<-daymet_data %>%
  filter(region=="South Region")

tmin_mean_south<-daymet_south %>%
  group_by(year, state) %>%
  summarise(tmin_mean=mean(median_tmin))
```

    ## `summarise()` has grouped output by 'year'. You can override using the
    ## `.groups` argument.

``` r
kable(head(tmin_mean_south))
```

| year  | state                | tmin_mean |
|:------|:---------------------|----------:|
| Y2010 | Alabama              |  10.24551 |
| Y2010 | Arkansas             |  10.07935 |
| Y2010 | Delaware             |   8.71000 |
| Y2010 | District of Columbia |   9.23125 |
| Y2010 | Florida              |  14.19376 |
| Y2010 | Georgia              |  10.34788 |

tmin_mean_south_rounded\<-tmin_mean_south %\>%
round(tmin_mean_south\$tmin_mean, 1)

**Question 4:** To visualize the trends, we could use ggplot to
visualize change in mean temperature over time. Create a line plot
(geom_line) showing the state means you calculated in question 3. Use
the color parameter to show separate colors for each state. You may also
need to define the state as a group in the aesthetic parameter.

``` r
ggplot(tmin_mean_south,aes(x=year, y=tmin_mean, group=state, color=state))+
  geom_line()
```

![](Lab1_Loading_data_and_basic_stats_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

**Question 5:** If you wanted to look at these data as a table, you’d
need to have it in wide format. Use the pivot_wider function to create a
wide format version of the data frame you created in question 3. In this
case, the rows should be states, the columns should be the years, and
the data in those columns should be mean minimum temperatures. Then call
the *whole table* using kable.

``` r
pivot_wider(tmin_mean_south, names_from = year, values_from = tmin_mean)
```

    ## # A tibble: 17 × 13
    ##    state Y2010 Y2011 Y2012 Y2013 Y2014 Y2015 Y2016 Y2017 Y2018 Y2019 Y2020 Y2021
    ##    <chr> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ##  1 Alab… 10.2  10.7  11.8  10.8  10.2  12.5  11.9  12.4  12.0  12.3  12.3  11.8 
    ##  2 Arka… 10.1  10.2  11.1   9.17  9.11 10.8  10.9  11.0  10.3  10.6  10.5  10.7 
    ##  3 Dela…  8.71  8.84  9.18  8.28  7.56  8.66  8.83  9.27  8.85  9.27  9.57  8.87
    ##  4 Dist…  9.23  9.30  9.47  8.52  7.98  8.90  9.10  9.96  9.13  9.79  9.97  9.59
    ##  5 Flor… 14.2  15.8  16.4  16.2  15.4  17.5  16.7  17.2  16.7  17.0  17.2  16.6 
    ##  6 Geor… 10.3  11.2  12.4  11.2  10.8  12.8  12.1  12.7  12.2  12.7  12.7  12.0 
    ##  7 Kent…  7.31  7.89  8.21  6.75  6.66  8.02  8.39  8.48  8.08  8.36  8.21  8.08
    ##  8 Loui… 13.2  13.9  14.7  13.2  12.7  14.8  15.0  15.4  14.5  14.4  14.8  14.7 
    ##  9 Mary…  8.33  8.62  8.73  7.79  7.01  8.11  8.39  8.86  8.40  8.91  9.22  8.68
    ## 10 Miss… 11.0  11.4  12.2  11.0  10.5  12.8  12.4  12.9  12.2  12.5  12.6  12.4 
    ## 11 Nort…  8.68  9.12  9.73  8.71  8.57 10.2   9.84 10.0   9.85 10.5  10.1   9.62
    ## 12 Okla…  9.57  9.52 10.4   8.53  8.81  9.78 10.2   9.96  9.16  9.29  9.41  9.88
    ## 13 Sout… 10.2  11.0  11.8  10.6  10.4  12.3  11.8  12.1  11.8  12.2  12.2  11.5 
    ## 14 Tenn…  8.28  8.57  9.36  7.86  7.58  9.36  9.15  9.33  9.18  9.57  9.26  9.06
    ## 15 Texas 11.5  12.2  12.8  11.5  11.5  12.4  12.9  12.9  12.1  12.0  12.4  12.5 
    ## 16 Virg…  7.41  7.88  8.22  7.33  6.84  8.21  8.17  8.38  8.16  8.69  8.60  8.05
    ## 17 West…  4.91  5.76  5.66  4.83  4.19  5.64  5.87  6.10  5.80  6.24  6.16  5.84

``` r
kable(tmin_mean_south)
```

| year  | state                | tmin_mean |
|:------|:---------------------|----------:|
| Y2010 | Alabama              | 10.245510 |
| Y2010 | Arkansas             | 10.079350 |
| Y2010 | Delaware             |  8.710000 |
| Y2010 | District of Columbia |  9.231250 |
| Y2010 | Florida              | 14.193762 |
| Y2010 | Georgia              | 10.347883 |
| Y2010 | Kentucky             |  7.313726 |
| Y2010 | Louisiana            | 13.248724 |
| Y2010 | Maryland             |  8.334948 |
| Y2010 | Mississippi          | 10.984035 |
| Y2010 | North Carolina       |  8.675038 |
| Y2010 | Oklahoma             |  9.568831 |
| Y2010 | South Carolina       | 10.163052 |
| Y2010 | Tennessee            |  8.279781 |
| Y2010 | Texas                | 11.478857 |
| Y2010 | Virginia             |  7.409715 |
| Y2010 | West Virginia        |  4.910909 |
| Y2011 | Alabama              | 10.734901 |
| Y2011 | Arkansas             | 10.169006 |
| Y2011 | Delaware             |  8.837778 |
| Y2011 | District of Columbia |  9.299584 |
| Y2011 | Florida              | 15.752463 |
| Y2011 | Georgia              | 11.170511 |
| Y2011 | Kentucky             |  7.892549 |
| Y2011 | Louisiana            | 13.880033 |
| Y2011 | Maryland             |  8.623281 |
| Y2011 | Mississippi          | 11.431438 |
| Y2011 | North Carolina       |  9.117442 |
| Y2011 | Oklahoma             |  9.515498 |
| Y2011 | South Carolina       | 10.959121 |
| Y2011 | Tennessee            |  8.572684 |
| Y2011 | Texas                | 12.157956 |
| Y2011 | Virginia             |  7.880649 |
| Y2011 | West Virginia        |  5.756598 |
| Y2012 | Alabama              | 11.833694 |
| Y2012 | Arkansas             | 11.119139 |
| Y2012 | Delaware             |  9.178194 |
| Y2012 | District of Columbia |  9.466667 |
| Y2012 | Florida              | 16.399621 |
| Y2012 | Georgia              | 12.355335 |
| Y2012 | Kentucky             |  8.214351 |
| Y2012 | Louisiana            | 14.680664 |
| Y2012 | Maryland             |  8.734462 |
| Y2012 | Mississippi          | 12.168389 |
| Y2012 | North Carolina       |  9.727054 |
| Y2012 | Oklahoma             | 10.350790 |
| Y2012 | South Carolina       | 11.818397 |
| Y2012 | Tennessee            |  9.359118 |
| Y2012 | Texas                | 12.815773 |
| Y2012 | Virginia             |  8.217077 |
| Y2012 | West Virginia        |  5.662424 |
| Y2013 | Alabama              | 10.790093 |
| Y2013 | Arkansas             |  9.174078 |
| Y2013 | Delaware             |  8.275417 |
| Y2013 | District of Columbia |  8.518750 |
| Y2013 | Florida              | 16.247929 |
| Y2013 | Georgia              | 11.202432 |
| Y2013 | Kentucky             |  6.749837 |
| Y2013 | Louisiana            | 13.202969 |
| Y2013 | Maryland             |  7.787986 |
| Y2013 | Mississippi          | 10.967403 |
| Y2013 | North Carolina       |  8.707479 |
| Y2013 | Oklahoma             |  8.527890 |
| Y2013 | South Carolina       | 10.616196 |
| Y2013 | Tennessee            |  7.862009 |
| Y2013 | Texas                | 11.474339 |
| Y2013 | Virginia             |  7.327907 |
| Y2013 | West Virginia        |  4.829826 |
| Y2014 | Alabama              | 10.189751 |
| Y2014 | Arkansas             |  9.112750 |
| Y2014 | Delaware             |  7.555417 |
| Y2014 | District of Columbia |  7.978333 |
| Y2014 | Florida              | 15.448762 |
| Y2014 | Georgia              | 10.818304 |
| Y2014 | Kentucky             |  6.659774 |
| Y2014 | Louisiana            | 12.699922 |
| Y2014 | Maryland             |  7.008941 |
| Y2014 | Mississippi          | 10.530401 |
| Y2014 | North Carolina       |  8.566662 |
| Y2014 | Oklahoma             |  8.813268 |
| Y2014 | South Carolina       | 10.428043 |
| Y2014 | Tennessee            |  7.575233 |
| Y2014 | Texas                | 11.538154 |
| Y2014 | Virginia             |  6.839179 |
| Y2014 | West Virginia        |  4.189849 |
| Y2015 | Alabama              | 12.543626 |
| Y2015 | Arkansas             | 10.780661 |
| Y2015 | Delaware             |  8.657917 |
| Y2015 | District of Columbia |  8.896667 |
| Y2015 | Florida              | 17.515896 |
| Y2015 | Georgia              | 12.789607 |
| Y2015 | Kentucky             |  8.019806 |
| Y2015 | Louisiana            | 14.846419 |
| Y2015 | Maryland             |  8.107795 |
| Y2015 | Mississippi          | 12.839426 |
| Y2015 | North Carolina       | 10.239342 |
| Y2015 | Oklahoma             |  9.778474 |
| Y2015 | South Carolina       | 12.299964 |
| Y2015 | Tennessee            |  9.361088 |
| Y2015 | Texas                | 12.421132 |
| Y2015 | Virginia             |  8.209383 |
| Y2015 | West Virginia        |  5.642818 |
| Y2016 | Alabama              | 11.866754 |
| Y2016 | Arkansas             | 10.927544 |
| Y2016 | Delaware             |  8.832361 |
| Y2016 | District of Columbia |  9.101250 |
| Y2016 | Florida              | 16.680100 |
| Y2016 | Georgia              | 12.087993 |
| Y2016 | Kentucky             |  8.387410 |
| Y2016 | Louisiana            | 14.964766 |
| Y2016 | Maryland             |  8.392500 |
| Y2016 | Mississippi          | 12.409924 |
| Y2016 | North Carolina       |  9.841467 |
| Y2016 | Oklahoma             | 10.167760 |
| Y2016 | South Carolina       | 11.791630 |
| Y2016 | Tennessee            |  9.147855 |
| Y2016 | Texas                | 12.880901 |
| Y2016 | Virginia             |  8.169474 |
| Y2016 | West Virginia        |  5.866697 |
| Y2017 | Alabama              | 12.359185 |
| Y2017 | Arkansas             | 11.046489 |
| Y2017 | Delaware             |  9.269167 |
| Y2017 | District of Columbia |  9.960834 |
| Y2017 | Florida              | 17.161580 |
| Y2017 | Georgia              | 12.650435 |
| Y2017 | Kentucky             |  8.477073 |
| Y2017 | Louisiana            | 15.401602 |
| Y2017 | Maryland             |  8.864080 |
| Y2017 | Mississippi          | 12.919746 |
| Y2017 | North Carolina       | 10.016312 |
| Y2017 | Oklahoma             |  9.960471 |
| Y2017 | South Carolina       | 12.105616 |
| Y2017 | Tennessee            |  9.333417 |
| Y2017 | Texas                | 12.882920 |
| Y2017 | Virginia             |  8.376147 |
| Y2017 | West Virginia        |  6.098197 |
| Y2018 | Alabama              | 11.990535 |
| Y2018 | Arkansas             | 10.279783 |
| Y2018 | Delaware             |  8.847917 |
| Y2018 | District of Columbia |  9.126250 |
| Y2018 | Florida              | 16.688346 |
| Y2018 | Georgia              | 12.221292 |
| Y2018 | Kentucky             |  8.075319 |
| Y2018 | Louisiana            | 14.524740 |
| Y2018 | Maryland             |  8.396684 |
| Y2018 | Mississippi          | 12.203547 |
| Y2018 | North Carolina       |  9.853292 |
| Y2018 | Oklahoma             |  9.157338 |
| Y2018 | South Carolina       | 11.755770 |
| Y2018 | Tennessee            |  9.175232 |
| Y2018 | Texas                | 12.072930 |
| Y2018 | Virginia             |  8.159727 |
| Y2018 | West Virginia        |  5.799614 |
| Y2019 | Alabama              | 12.341113 |
| Y2019 | Arkansas             | 10.576967 |
| Y2019 | Delaware             |  9.268194 |
| Y2019 | District of Columbia |  9.791250 |
| Y2019 | Florida              | 17.009061 |
| Y2019 | Georgia              | 12.729167 |
| Y2019 | Kentucky             |  8.359674 |
| Y2019 | Louisiana            | 14.407923 |
| Y2019 | Maryland             |  8.914427 |
| Y2019 | Mississippi          | 12.507703 |
| Y2019 | North Carolina       | 10.471858 |
| Y2019 | Oklahoma             |  9.294513 |
| Y2019 | South Carolina       | 12.227527 |
| Y2019 | Tennessee            |  9.573070 |
| Y2019 | Texas                | 12.009859 |
| Y2019 | Virginia             |  8.686776 |
| Y2019 | West Virginia        |  6.241008 |
| Y2020 | Alabama              | 12.285535 |
| Y2020 | Arkansas             | 10.509972 |
| Y2020 | Delaware             |  9.571944 |
| Y2020 | District of Columbia |  9.969583 |
| Y2020 | Florida              | 17.245578 |
| Y2020 | Georgia              | 12.686963 |
| Y2020 | Kentucky             |  8.207757 |
| Y2020 | Louisiana            | 14.834108 |
| Y2020 | Maryland             |  9.219514 |
| Y2020 | Mississippi          | 12.597368 |
| Y2020 | North Carolina       | 10.136233 |
| Y2020 | Oklahoma             |  9.413074 |
| Y2020 | South Carolina       | 12.211504 |
| Y2020 | Tennessee            |  9.262618 |
| Y2020 | Texas                | 12.420048 |
| Y2020 | Virginia             |  8.600852 |
| Y2020 | West Virginia        |  6.157992 |
| Y2021 | Alabama              | 11.803215 |
| Y2021 | Arkansas             | 10.749478 |
| Y2021 | Delaware             |  8.871806 |
| Y2021 | District of Columbia |  9.590000 |
| Y2021 | Florida              | 16.628458 |
| Y2021 | Georgia              | 11.995301 |
| Y2021 | Kentucky             |  8.075701 |
| Y2021 | Louisiana            | 14.709909 |
| Y2021 | Maryland             |  8.681406 |
| Y2021 | Mississippi          | 12.446601 |
| Y2021 | North Carolina       |  9.622996 |
| Y2021 | Oklahoma             |  9.877798 |
| Y2021 | South Carolina       | 11.453342 |
| Y2021 | Tennessee            |  9.062390 |
| Y2021 | Texas                | 12.474040 |
| Y2021 | Virginia             |  8.050179 |
| Y2021 | West Virginia        |  5.836871 |

**Question 6:** Let’s assess the relationship of heat and precipitation
by region. Returning to the original dataset, create a data frame that
shows the mean *maximum* temperature and mean precipitation for *all*
states in 2015, also including region as a subgroup. Then use ggplot to
create a scatterplot (geom_point) for these two variables, coloring the
points using the region variable.

``` r
heat_precip2015<-daymet_data %>%
  filter(year=="Y2015") %>%
  group_by(state, region) %>%
  summarise(tmax_mean=mean(median_tmax), mean_prcp=mean(sum_prcp))
```

    ## `summarise()` has grouped output by 'state'. You can override using the
    ## `.groups` argument.

``` r
ggplot(heat_precip2015,aes(x=tmax_mean, y=mean_prcp, color=region))+
  geom_point()
```

![](Lab1_Loading_data_and_basic_stats_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

**Question 7:** In the space below, explain what each function in your
code for question 6 does to the dataset in plain English.

{heat_precip2015\<-daymet_data - creates a new data frame from original
data frame

filter(year==“Y2015”) - filters “year” column to only include results
from desired year “Y2015”

group_by(state, region) - provides new data frame with desired columns
(state, region) to include from original data frame

summarise(tmax_mean=mean(median_tmax), mean_prcp=mean(sum_prcp) -
calculates summary statistics from provided data; calculates new
variable “tmax_mean”, which is the mean of original “median_tmax”
variable; calculates new variable “mean_prcp”, which is the mean of
original “sum_prcp” variable

ggplot(heat_precip2015,aes(x=tmax_mean, y=mean_prcp, color=region))+
geom_point() - calls ggplot package to create visualization of data;
states the data frame being worked with, variables to be represented on
x and y axis, and the variable by which color scheme is determined.
geom_point specifies type of visualization (scatterplot)}

**Bonus question:** In script 1-3, we covered ways of working with the
Daymet API. Create a script below that uses that daymetr package to
download data from Daymet for a place (or places) of your choosing, uses
a dplyr function (e.g., mutate, summarise, filter, etc.) to wrangle the
data, and then visualizes it using ggplot. In addition to this code,
write a short summary of a pattern that’s evident in the data you
visualized.

``` r
#Your code goes here
```

{Explanation goes here.}

**Lab reflection:** How do you feel about the work you did on this lab?
Was it easy, moderate, or hard? What were the biggest things you learned
by completing it?

I feel like I did the best work that I am capable of at the moment. It
was moderately challenging; I plan to include the issues that I
encountered while completing this lab. I’d say that I learned not to
panic when I encounter any errors or issues. Many of the problems that I
ran into were solved by simple troubleshooting and a consultation of my
resources, which was a relief. If I had more free time on my hands, I’d
be able to attempt the bonus question, however I’ve already allocated
about as much time as I can to this assignment. All in all, I learned
quite a bit and am happy to have been able to get some more practice and
repetitions in on my own.
