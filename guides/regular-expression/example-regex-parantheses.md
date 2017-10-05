Regular Expression in R example
================

``` r
library(tidyverse)
library(xlsx)
# Import dataset
data <- read.xlsx("~/Work/Projects/Portfolio/asielzoekerbuurten/opvanglocaties_azielzoekers.xlsx", sheetIndex = 1)
# Convert to tibble
data <- as.tibble(data)
```

The following column needs some cleaning up.

``` r
# The column that needs some cleaning up
data$Locatie
```

    ##  [1] Alkmaar (RVB)               Almelo                     
    ##  [3] Almere (regulier)           Amersfoort (regulier)      
    ##  [5] Amsterdam ALO               Arnhem (Eldershofsweg)     
    ##  [7] Arnhem (zuid)               Arnhem AMV                 
    ##  [9] Arnhem AMV                  Assen (regulier)           
    ## [11] Baexem                      Balk                       
    ## [13] Budel Cranendonck           Burgum (regulier)          
    ## [15] Delfzijl                    Den Helder (doggershoek)   
    ## [17] Deventer (AMV)              Drachten                   
    ## [19] Drachten (AMV)              Dronten                    
    ## [21] Echt                        Emmen (regulier)           
    ## [23] Gilze                       Goes                       
    ## [25] Grave                       Hardenberg                 
    ## [27] Harderwijk                  Heerhugowaard              
    ## [29] Heerlen (regulier)          Hoogeveen (de Grittenborgh)
    ## [31] Katwijk                     KCO                        
    ## [33] Leersum                     Luttelgeest                
    ## [35] Maastricht (AVO)            Maastricht (malberg)       
    ## [37] Middelburg                  Musselkanaal               
    ## [39] Nijmegen                    Nijmegen (Stieltjeslaan)   
    ## [41] Oisterwijk                  Overberg                   
    ## [43] Overloon                    Rijswijk                   
    ## [45] Rotterdam                   Schalkhaar                 
    ## [47] Sint Annaparochie           Sneek                      
    ## [49] Sweikhuizen                 Ter Apel                   
    ## [51] Tilburg (AMV)               Utrecht                    
    ## [53] Utrecht Overvecht           Veenhuizen                 
    ## [55] Wageningen (bosrandweg)     Weert                      
    ## [57] Winterswijk                 Zeewolde                   
    ## [59] Zeist                       Zutphen                    
    ## [61] Zweelo                     
    ## 60 Levels: Alkmaar (RVB) Almelo ... Zweelo

Regular expression pattern
--------------------------

You usually wouldn't remove these parantheses from this column because they are locations and not cities. Also in this dataset you already have a column with cities: `Plaats`. But just for the sake of practice we will remove the parantheses and its content (and the whitespace between the city name and the parantheses).

``` r
# Remove the parentheses and its content
gsub(" \\([A-Za-z]*\\)", "", as.character(data$Locatie))
```

    ##  [1] "Alkmaar"                     "Almelo"                     
    ##  [3] "Almere"                      "Amersfoort"                 
    ##  [5] "Amsterdam ALO"               "Arnhem"                     
    ##  [7] "Arnhem"                      "Arnhem AMV"                 
    ##  [9] "Arnhem AMV"                  "Assen"                      
    ## [11] "Baexem"                      "Balk"                       
    ## [13] "Budel Cranendonck"           "Burgum"                     
    ## [15] "Delfzijl"                    "Den Helder"                 
    ## [17] "Deventer"                    "Drachten"                   
    ## [19] "Drachten"                    "Dronten"                    
    ## [21] "Echt"                        "Emmen"                      
    ## [23] "Gilze"                       "Goes"                       
    ## [25] "Grave"                       "Hardenberg"                 
    ## [27] "Harderwijk"                  "Heerhugowaard"              
    ## [29] "Heerlen"                     "Hoogeveen (de Grittenborgh)"
    ## [31] "Katwijk"                     "KCO"                        
    ## [33] "Leersum"                     "Luttelgeest"                
    ## [35] "Maastricht"                  "Maastricht"                 
    ## [37] "Middelburg"                  "Musselkanaal"               
    ## [39] "Nijmegen"                    "Nijmegen"                   
    ## [41] "Oisterwijk"                  "Overberg"                   
    ## [43] "Overloon"                    "Rijswijk"                   
    ## [45] "Rotterdam"                   "Schalkhaar"                 
    ## [47] "Sint Annaparochie"           "Sneek"                      
    ## [49] "Sweikhuizen"                 "Ter Apel"                   
    ## [51] "Tilburg"                     "Utrecht"                    
    ## [53] "Utrecht Overvecht"           "Veenhuizen"                 
    ## [55] "Wageningen"                  "Weert"                      
    ## [57] "Winterswijk"                 "Zeewolde"                   
    ## [59] "Zeist"                       "Zutphen"                    
    ## [61] "Zweelo"

``` r
# Store in new data column
data$Locatie <- gsub(".\\([A-Za-z]*\\)", "", as.character(data$Locatie))
```

When you disect the pattern in the `gsub()` regular expression function you should wncode every character from left to right:

-   first you see a white space then
-   `\\(` : Because `(` is a meta character you have to 'escape' it by adding two backslashes in front.
-   `[A-Za-z]*` : This returns any CAPITAL or lower case letter. The `*` says look for 0 or more letters.
-   `\\)` : At last we want to return for the closing parenthesis.

This pattern: `" \\([A-Za-z]*\\)"` will return `(RVB)` but also `(regulier)`.

Our new dataset
---------------

``` r
data
```

    ## # A tibble: 61 x 7
    ##    Centrum.nr       Locatie                Straat     Nr     Plaats
    ##         <dbl>         <chr>                <fctr> <fctr>     <fctr>
    ##  1          1       Alkmaar          Robonsbosweg      1    Alkmaar
    ##  2          2        Almelo      Vriezenveenseweg   170a     Almelo
    ##  3          3        Almere           Biathlonweg     15     Almere
    ##  4          4    Amersfoort Barchman Wuytierslaan     53 Amersfoort
    ##  5          5 Amsterdam ALO           Willinklaan  1,3&5  Amsterdam
    ##  6          6        Arnhem        Eldershofseweg     51     Arnhem
    ##  7          7        Arnhem       Groningensingel   1225     Arnhem
    ##  8          8    Arnhem AMV        Frombergstraat      1     Arnhem
    ##  9          9    Arnhem AMV        Apeldoornseweg     43     Arnhem
    ## 10         10         Assen          Schepersmaat      3      Assen
    ## # ... with 51 more rows, and 2 more variables: Provincie <fctr>,
    ## #   Opvang.cap <dbl>
