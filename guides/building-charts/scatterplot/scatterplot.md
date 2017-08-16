Bubble chart
================

In this report we search for correlations between traffic deaths, the quality of infrastructures and alcohol consumption.

fist things first loading the needed packages.

``` r
library(tidyverse)
library(plyr)
library(ggthemes)
library(RSvgDevice)
library(stats)
library(ggrepel)
library(rvest)
library(extrafont)
library(countrycode)
```

Infrastructure quality data preperation
---------------------------------------

Retrieved from the [World Bank data base 2015](http://databank.worldbank.org/data/reports.aspx?source=2&series=IQ.WEF.PORT.XQ&country=#). The closer to the number 1 means the country has a good infrastructure. The closer to the number 7 means that the country has a bad infrastructure.

``` r
# Reading data, quality of infrastructure from World Bank 1 = bad, 7 = good.
infrastructure <- read_csv("/Users/Thomas/colourful-facts/guides/building-charts/scatterplot/data/quality-of-infrastructure.csv")
# Convert column into numeric
infrastructure$quality.of.infrastructure <- as.numeric(infrastructure$quality.of.infrastructure)
# Remove rows with numerics
infrastructure <- infrastructure[complete.cases(infrastructure),]
# Create tibble
infrastructure <- as.tibble(infrastructure)
# Change Country Code column name in "Code" (sometimes adding the package before the function helps)
infrastructure <- dplyr::rename(infrastructure, Code = Country_Code)
```

Traffic deaths per 100,000 citizens data preperation
----------------------------------------------------

Retrieved from a [2015 report from the World Health Organisation](http://www.who.int/violence_injury_prevention/road_safety_status/2015/en/). It was in PDF format so I first had to scrape it with Tabula software. Then I cleaned it a bit in google spreasheets, so I didn't have to transform the data a lot.

``` r
# Estimated traffic death rates per 100.000
trafficDeaths <- read_csv("/Users/Thomas/colourful-facts/guides/building-charts/scatterplot/data/estimated-traffic-death-rates-2015.csv")
# Prepare population dataframe
trafficDeaths$traffic.deaths.100000 <- as.numeric(trafficDeaths$traffic.deaths.100000)
# Add column with the country codes to later join the two datasets.
trafficDeaths <- trafficDeaths %>% 
  mutate(Country, 
         Code = countrycode(trafficDeaths$Country, "country.name", "wb")) %>%
  select(1,3,2)
```

Alcohol consumption per capita in liters data preperation
---------------------------------------------------------

Very recent data was hard to come by, the data is from 2010. I found [the data on wikipedia](https://en.wikipedia.org/wiki/List_of_countries_by_alcohol_consumption_per_capita#cite_note-2). The table on the page uses the data from the World Helath Organisation.

``` r
url <- "https://en.wikipedia.org/wiki/List_of_countries_by_alcohol_consumption_per_capita#cite_note-2"

# Pure alcohol consumption among persons (age 15+) in liters per capita per year, 2010
alcoholConsumption <- url %>% 
  read_html() %>%
  html_nodes(xpath = '//*[@id="mw-content-text"]/div/table[1]') %>%
  html_table()
# Storing the retrieved information in a variable and convert list into data frame.
alcoholConsumption <- alcoholConsumption[[1]]
# Select needed columns
alcoholConsumption <- alcoholConsumption %>%
  select(Country, Total)

alcoholConsumption <- alcoholConsumption %>% mutate(Country, 
                              Code = countrycode(alcoholConsumption$Country, "country.name", "wb")) %>%
  select(1,3,2)
```

Joining the three dataframes to one
-----------------------------------

``` r
# Joining all three dataframes
merged <- left_join(infrastructure, trafficDeaths, by = "Code") %>%
  left_join(., alcoholConsumption, by = "Code")
# Selct needed columns
merged <- merged[,c(4,2,3,5,7)]
# Convert character column to numeric
merged$quality.of.infrastructure <- as.numeric(merged$quality.of.infrastructure)
# Return rows with no NA data
merged <- merged[complete.cases(merged),]
# Change column names
merged <- merged %>% 
  dplyr::rename(infrastructure = quality.of.infrastructure,
         traffic.deaths = traffic.deaths.100000,
         alcohol.consumption = Total,
         Country = Country.x)
```

Fianally time for plotting
--------------------------

``` r
merged %>% 
  ggplot(aes(x = infrastructure, y = traffic.deaths)) +
  geom_point(aes(size = alcohol.consumption),
             alpha = 0.7,
             fill = "#1B5E20",
             colour = "white",
             shape = 21) +
  geom_smooth(method = lm,
              se = FALSE) +
  geom_label_repel(data = filter(merged, Country == "Thailand" |
                                  Country == "Malawi" | 
                                  Country == "Republic of Moldova" |
                                  Country == "Netherlands" |
                                  Country == "Malaysia"), 
                  aes(label = Country),
                  size = 3,
                  label.padding = unit(0.15, "lines"),
                  point.padding = unit(0.2, "lines")) +
  scale_y_continuous(breaks = seq(0,40,5)) +
  scale_x_reverse(limits = c(7,1)) +
  theme_economist() + 
  theme(text = element_text(family = "Roboto Condensed"),
        plot.background = element_rect(fill = "#F5F0E5"),
        panel.background = element_rect(fill = "#F5F0E5"),
        legend.background = element_rect(fill = "#F5F0E5"),
        legend.key = element_rect(fill = "#F5F0E5")) +
  labs(title = "Traffic deaths and a bad infrastructure seem correlated",
       subtitle = "A high alcohol consumtion per capita seems not correlated to given variables.",
       size = "Alcohol consumption in liters per capita:",
       x = "infrastructure: 7 = bad, 1 = good",
       y = "Traffic deaths per 100,000 citizens") +
  annotate("text", x = 5, y = 15, label = "Average", colour = "blue")
```

<img src="scatterplot_files/figure-markdown_github-ascii_identifiers/plot1-all-countries-1.png" style="display: block; margin: auto;" />

A high alcohol consumption doesn't have to lead to more traffic deaths in a country
-----------------------------------------------------------------------------------

Alcohol isn't the only factor. Countries with in comparison a high alcohol consumption **can** have a very low number of traffic deaths.

``` r
merged %>% 
  ggplot(aes(x = alcohol.consumption, y = traffic.deaths)) +
  geom_point(alpha = 0.7,
             size = 3,
             fill = "#1B5E20",
             colour = "white",
             shape = 21) +
  theme_economist() + 
  theme(text = element_text(family = "Roboto Condensed"),
        plot.background = element_rect(fill = "#F5F0E5"),
        panel.background = element_rect(fill = "#F5F0E5"),
        legend.background = element_rect(fill = "#F5F0E5"),
        legend.key = element_rect(fill = "#F5F0E5")) +
  labs(title = "High alcohol consumption does not seem to correlate with\nmore traffic deaths in a country",
       x = "Alcohol consumption in liters per capita",
       y = "Traffic deaths per 100,000 citizens")
```

<img src="scatterplot_files/figure-markdown_github-ascii_identifiers/plot2-alcohol-consumption-1.png" style="display: block; margin: auto;" />
