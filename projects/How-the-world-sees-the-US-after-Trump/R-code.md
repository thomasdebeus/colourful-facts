How the world sees the US after Trump
================

In this file the code that generated the data visualisation in the article [How the world sees the U.S.A. after Trump](https://medium.com/tdebeus/how-the-world-sees-the-u-s-a-after-trump-2f2ad6dd0e12) on [Colourful Facts](https://medium.com/tdebeus).

The visualisation deviates from the end result in the portfolio, this is because I finalise the visuals in [Sketch](https://www.sketchapp.com/).

First thing first: loading the used R packages.

``` r
library(tidyverse)
library(googlesheets)
library(gsheet)
library(reshape2)
```

Importing and prepping the data.

``` r
# See what's in google spreadsheets.
gs_ls()

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

# Preparing data frame for Favorability US.
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
summary(favUS2017)

# Rename year column. Getting ready for the merge.
colnames(confInTrump)[2] <- "Trump" 
colnames(favUS2017)[2] <- "US"

# Merging the two data frames
mergedDf <- merge(confInTrump, favUS2017, by = "Country")

# In how many countries is there a minority in favour of the US? 
filter(mergedDf, US < 50)
```

Time for visualising the comparison plot.
-----------------------------------------

Showing the difference in popularity between Trump and U.S.A. from 37 (in 2017 at least) countries.

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

![](R-code_files/figure-markdown_github-ascii_identifiers/comparison%20plot-1.png)