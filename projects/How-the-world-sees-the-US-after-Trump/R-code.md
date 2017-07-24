How the world sees the US after Trump
================

In this file the code that generated the data visualisation in the article [How the world sees the U.S.A. after Trump](https://medium.com/tdebeus/how-the-world-sees-the-u-s-a-after-trump-2f2ad6dd0e12) on [Colourful Facts](https://medium.com/tdebeus).

The visualisation deviates from the end result in the portfolio, this is because I finalise the visuals in [Sketch](https://www.sketchapp.com/).

First thing first importing and prepping the data.

``` r
# Needed packages.
library(tidyverse)
```

    ## Loading tidyverse: ggplot2
    ## Loading tidyverse: tibble
    ## Loading tidyverse: tidyr
    ## Loading tidyverse: readr
    ## Loading tidyverse: purrr
    ## Loading tidyverse: dplyr

    ## Conflicts with tidy packages ----------------------------------------------

    ## filter(): dplyr, stats
    ## lag():    dplyr, stats

``` r
library(googlesheets)
library(gsheet)
library(reshape2)
```

    ## 
    ## Attaching package: 'reshape2'

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     smiths

``` r
# Running authencation from google in Chrome browser.
gs_ls()
```

    ## # A tibble: 74 × 10
    ##                 sheet_title        author  perm version
    ##                       <chr>         <chr> <chr>   <chr>
    ## 1    worlds-view-of-America  thomasdebeus    rw     new
    ## 2  New tweet by @thierryba…  thomasdebeus    rw     new
    ## 3  New tweet by @fvdemocra…  thomasdebeus    rw     new
    ## 4  Access open Data reposi…  thomasdebeus    rw     new
    ## 5        Great data stories  thomasdebeus    rw     new
    ## 6  Design - Snowfallen sto… bobbie.johns…    rw     new
    ## 7      Untitled spreadsheet  thomasdebeus    rw     new
    ## 8      Untitled spreadsheet  thomasdebeus    rw     new
    ## 9  Overledenen__doodsoorza…  thomasdebeus    rw     new
    ## 10 Overledenen__doodsoo_06…  thomasdebeus    rw     new
    ## # ... with 64 more rows, and 6 more variables: updated <dttm>,
    ## #   sheet_key <chr>, ws_feed <chr>, alternate <chr>, self <chr>,
    ## #   alt_key <chr>

``` r
# Identify spreadsheet.
df <- gs_title("worlds-view-of-America")
```

    ## Sheet successfully identified: "worlds-view-of-America"

``` r
# Preparing confidence in Trump data frame.
# Accessing and storing Sheet1 (Confidence in U.S. presidents).
confInPres <- df %>% gs_read(ws = "Sheet1", range = cell_rows(1:38))
```

    ## Accessing worksheet titled 'Sheet1'.

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_character(),
    ##   `2001` = col_character(),
    ##   `2003` = col_character(),
    ##   `2005` = col_character(),
    ##   `2006` = col_character(),
    ##   `2007` = col_character(),
    ##   `2008` = col_character(),
    ##   `2009` = col_character(),
    ##   `2010` = col_character(),
    ##   `2011` = col_character(),
    ##   `2012` = col_character(),
    ##   `2013` = col_character(),
    ##   `2014` = col_character(),
    ##   `2015` = col_character(),
    ##   `2016` = col_character(),
    ##   `2017` = col_integer()
    ## )

``` r
# Paste "year" to variables.
colnames(confInPres) <- paste("year", colnames(confInPres), sep = "_")
# Rename country column
colnames(confInPres)[1] <- "Country"
# Data Frame with confidence in Trump.
confInTrump <- select(confInPres, Country, year_2017)

# Preparing dta frame for Favorability for US.
# Accessing and storing Sheet2 (Favorable view of U.S)
favUS <- df %>% gs_read(ws = "Sheet2", range = cell_rows(1:38))
```

    ## Accessing worksheet titled 'Sheet2'.

    ## Parsed with column specification:
    ## cols(
    ##   X1 = col_character(),
    ##   `2000` = col_character(),
    ##   `2002` = col_character(),
    ##   `2003` = col_character(),
    ##   `2004` = col_character(),
    ##   `2005` = col_character(),
    ##   `2006` = col_character(),
    ##   `2007` = col_character(),
    ##   `2008` = col_character(),
    ##   `2009` = col_character(),
    ##   `2010` = col_character(),
    ##   `2011` = col_character(),
    ##   `2012` = col_character(),
    ##   `2013` = col_character(),
    ##   `2014` = col_character(),
    ##   `2015` = col_character(),
    ##   `2016` = col_character(),
    ##   `2017` = col_integer()
    ## )

``` r
# Rename column names.
colnames(favUS) <- paste("year", colnames(favUS), sep = "_")
colnames(favUS)[1] <- "Country"
# Subsetting from the year 2017 to compare with sheet 1
favUS2017 <- select(favUS, Country, year_2017)

# Look at summary statistics (Same amount of countries? How do the mean and median differ?)
summary(confInTrump)
```

    ##    Country            year_2017    
    ##  Length:37          Min.   : 5.00  
    ##  Class :character   1st Qu.:14.00  
    ##  Mode  :character   Median :22.00  
    ##                     Mean   :26.78  
    ##                     3rd Qu.:39.00  
    ##                     Max.   :69.00

``` r
summary(favUS2017)
```

    ##    Country            year_2017    
    ##  Length:37          Min.   :15.00  
    ##  Class :character   1st Qu.:39.00  
    ##  Mode  :character   Median :49.00  
    ##                     Mean   :49.54  
    ##                     3rd Qu.:57.00  
    ##                     Max.   :84.00

``` r
# Rename year column. Getting ready for the merge.
colnames(confInTrump)[2] <- "Trump" 
colnames(favUS2017)[2] <- "US"

# Merging the two data frames
mergedDf <- merge(confInTrump, favUS2017, by = "Country")

# In how many countries is there a minority in favour of the US? 
filter(mergedDf, US < 50)
```

    ##        Country Trump US
    ## 1    Argentina    13 35
    ## 2    Australia    29 48
    ## 3       Canada    22 43
    ## 4        Chile    12 39
    ## 5       France    14 46
    ## 6      Germany    11 35
    ## 7       Greece    19 43
    ## 8        India    40 49
    ## 9    Indonesia    23 48
    ## 10      Jordan     9 15
    ## 11     Lebanon    15 34
    ## 12      Mexico     5 30
    ## 13 Netherlands    17 37
    ## 14      Russia    53 41
    ## 15       Spain     7 31
    ## 16      Sweden    10 45
    ## 17     Tunisia    18 27
    ## 18      Turkey    11 18
    ## 19   Venezuela    20 47

Time for visualising
--------------------

``` r
compPlot <- ggplot(mergedDf) +
  geom_segment(aes(x = Trump,
                   y = reorder(Country, -Trump),
                   xend = US,
                   yend = Country),
               colour = "#AFACAC",
               size = 2.5) +
  geom_point(aes(x = Trump, 
                 y = reorder(Country, -Trump)),
             colour = "#B42030",
             size = 3) +
  geom_point(aes(x = US, 
                 y = Country),
             colour = "#3C3970",
             size = 3) +
  geom_text(aes(x = Trump,
                y = reorder(Country, -Trump),
                label = Trump),
            nudge_x = -6,
            size = 3.5,
            colour = "#B42030") +
  geom_text(aes(x = US,
                y = Country,
                label = US),
            nudge_x = 6,
            size = 3.5,
            colour = "#3C3970") +
  # Adding a vertical mean line of US favorability in the plot.
  geom_vline(xintercept = mean(mergedDf$US),
             linetype = 5,
             colour = "#3C3970",
             alpha = 0.7) +
  labs(title = "Difference in popularity between\nTrump and U.S.A.",
       subtitle = "In percentage of respondents.") +
  xlab("% of respondents") +
  theme(axis.title = element_blank(),
        axis.text.x = element_blank(),
        axis.ticks = element_blank(),
        panel.grid.major.x = element_blank(),
        panel.grid.minor.x = element_blank(),
        panel.background = element_rect(fill = "#F4EBDB") )

# Plot
compPlot
```

<img src="R-code_files/figure-markdown_github-ascii_identifiers/comparison plot-1.png" width="50%" style="display: block; margin: auto;" />
