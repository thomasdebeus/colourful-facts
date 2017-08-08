Accessing Tidy CBS (Centraal Bureau voor Statetiek) data
================

When you're in need of [CBS open data](http://statline.cbs.nl/Statweb/) you end up transposing and rearranging columns and rows. This can be a devious and frustrating task. The data you get from the cbs table manager isn't 'Tidy'. Tidy data was coined by Chief Scientist at R Studio and Professor in statistics Hadley Wickham. In his paper [Tidy Data](http://vita.had.co.nz/papers/tidy-data.pdf) he says:

> Tidy datasets are easy to manipulate, model and visualise, and have a specific structure: each variable is a column, each observation is a row, and each type of observational unit is a table.

When you already work in R you can therefore better use the `cbsodataR` [package by Edwin de Jong](https://github.com/edwindj/cbsodataR). This package will give you access tot the CBS API and will return a tidy data set.

You can include R code in the document as follows:

``` r
library(cbsodataR)

# Retrieve a list of all the data sets on CBS Statline 
tables <- get_table_list(Language = "nl")
```

When importing a data set from CBS Statline it's recommended to first search for the right data set on [CBS Statline](http://statline.cbs.nl/Statweb/) then copy paste the title of that data set in the search bar of your `tables` variable. You'll immediatly get the right identifier to use when you want to access the data set.

``` r
# In the following example the data set "Bevolkingsontwikkeling; regio per maand" is used.
df <- get_data("37230ned")
```
