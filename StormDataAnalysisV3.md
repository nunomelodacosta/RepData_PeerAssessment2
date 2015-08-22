# Health and economic impact analysys report of storms and other severe weather events, based on NOAA storm database
Nuno Melo  
August 18, 2015  

# Synopsis  
**describes and summarizes your analysis in at most 10 complete sentences**  
In this report we aim to describe the economic and health impact caused by storms and other severe weather events, based on NOAA storm database. In particular it answers two questions:  

1. which types of events are most harmful to population health?
2. which types of events have the greatest economic consequences?

# Data description
The data for this analysys was downloaded from the Coursera web [site](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2in) [47Mb]. It is a comma-separated-value file compressed via the bzip2 algorithm to reduce its size.

The events in the database start in the year 1950 and end in November 2011. In the earlier years of the database there are generally fewer events recorded, most likely due to a lack of good records. More recent years should be considered more complete. There is also some documentation of the database available. Here you will find how some of the variables are constructed/defined. See below:  

* [National Weather Service Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf)

*  [National Climatic Data Center Storm Events FAQ](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2FNCDC%20Storm%20Events-FAQ%20Page.pdf)

# Data Processing  
We first load our required R packages

## Loading required packages

```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.2.1
```

```
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(lubridate)
library(ggplot2)
library(ggvis)
```

```
## Warning: package 'ggvis' was built under R version 3.2.1
```

```
## 
## Attaching package: 'ggvis'
## 
## The following object is masked from 'package:ggplot2':
## 
##     resolution
```

```r
library(xtable)
```

```
## Warning: package 'xtable' was built under R version 3.2.1
```

## R Session Software environment used in the analysis
This is the information about R, the OS and attached or loaded packages

```r
sessionInfo()
```

```
## R version 3.2.0 (2015-04-16)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 7 x64 (build 7601) Service Pack 1
## 
## locale:
## [1] LC_COLLATE=English_United States.1252 
## [2] LC_CTYPE=English_United States.1252   
## [3] LC_MONETARY=English_United States.1252
## [4] LC_NUMERIC=C                          
## [5] LC_TIME=English_United States.1252    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] xtable_1.7-4    ggvis_0.4.2     ggplot2_1.0.1   lubridate_1.3.3
## [5] dplyr_0.4.2    
## 
## loaded via a namespace (and not attached):
##  [1] Rcpp_0.12.0      knitr_1.11       magrittr_1.5     MASS_7.3-43     
##  [5] munsell_0.4.2    colorspace_1.2-6 R6_2.1.0         stringr_1.0.0   
##  [9] plyr_1.8.3       tools_3.2.0      parallel_3.2.0   grid_3.2.0      
## [13] gtable_0.1.2     DBI_0.3.1        htmltools_0.2.6  yaml_2.1.13     
## [17] assertthat_0.1   digest_0.6.8     shiny_0.12.2     reshape2_1.4.1  
## [21] formatR_1.2      mime_0.3         memoise_0.2.1    evaluate_0.7.2  
## [25] rmarkdown_0.7    stringi_0.5-5    scales_0.2.5     httpuv_1.3.3    
## [29] proto_0.3-10
```

## Downloading data
The name of the file containing the data is *StormData.csv.bz2* and will be downloaded directly, from the Coursera web [site](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2in) [47Mb], to your working directory, in case the file does not already exists



```r
if (!file.exists("StormData.csv.bz2"))
        download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2",
                      "StormData.csv.bz2", quiet = FALSE, mode = "wb")
```

## Reading the *StormData.csv.bz2* in csv format into R

```r
data <- read.csv("StormData.csv.bz2")
```

## Checking the data 
After reading the data we check the raw data summary info and the first few rows 

```r
glimpse(data)
```

```
## Observations: 902297
## Variables:
## $ STATE__    (dbl) 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, ...
## $ BGN_DATE   (fctr) 4/18/1950 0:00:00, 4/18/1950 0:00:00, 2/20/1951 0:...
## $ BGN_TIME   (fctr) 0130, 0145, 1600, 0900, 1500, 2000, 0100, 0900, 20...
## $ TIME_ZONE  (fctr) CST, CST, CST, CST, CST, CST, CST, CST, CST, CST, ...
## $ COUNTY     (dbl) 97, 3, 57, 89, 43, 77, 9, 123, 125, 57, 43, 9, 73, ...
## $ COUNTYNAME (fctr) MOBILE, BALDWIN, FAYETTE, MADISON, CULLMAN, LAUDER...
## $ STATE      (fctr) AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, AL...
## $ EVTYPE     (fctr) TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TORNA...
## $ BGN_RANGE  (dbl) 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ BGN_AZI    (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ BGN_LOCATI (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ END_DATE   (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ END_TIME   (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ COUNTY_END (dbl) 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ COUNTYENDN (lgl) NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ END_RANGE  (dbl) 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ END_AZI    (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ END_LOCATI (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ LENGTH     (dbl) 14.0, 2.0, 0.1, 0.0, 0.0, 1.5, 1.5, 0.0, 3.3, 2.3, ...
## $ WIDTH      (dbl) 100, 150, 123, 100, 150, 177, 33, 33, 100, 100, 400...
## $ F          (int) 3, 2, 2, 2, 2, 2, 2, 1, 3, 3, 1, 1, 3, 3, 3, 4, 1, ...
## $ MAG        (dbl) 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ FATALITIES (dbl) 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 4, 0, ...
## $ INJURIES   (dbl) 15, 0, 2, 2, 2, 6, 1, 0, 14, 0, 3, 3, 26, 12, 6, 50...
## $ PROPDMG    (dbl) 25.0, 2.5, 25.0, 2.5, 2.5, 2.5, 2.5, 2.5, 25.0, 25....
## $ PROPDMGEXP (fctr) K, K, K, K, K, K, K, K, K, K, M, M, K, K, K, K, K,...
## $ CROPDMG    (dbl) 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ CROPDMGEXP (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ WFO        (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ STATEOFFIC (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ ZONENAMES  (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ LATITUDE   (dbl) 3040, 3042, 3340, 3458, 3412, 3450, 3405, 3255, 333...
## $ LONGITUDE  (dbl) 8812, 8755, 8742, 8626, 8642, 8748, 8631, 8558, 874...
## $ LATITUDE_E (dbl) 3051, 0, 0, 0, 0, 0, 0, 0, 3336, 3337, 3402, 3404, ...
## $ LONGITUDE_ (dbl) 8806, 0, 0, 0, 0, 0, 0, 0, 8738, 8737, 8644, 8640, ...
## $ REMARKS    (fctr) , , , , , , , , , , , , , , , , , , , , , , , , 
## $ REFNUM     (dbl) 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, ...
```

```r
head(data) 
```

```
##   STATE__           BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE
## 1       1  4/18/1950 0:00:00     0130       CST     97     MOBILE    AL
## 2       1  4/18/1950 0:00:00     0145       CST      3    BALDWIN    AL
## 3       1  2/20/1951 0:00:00     1600       CST     57    FAYETTE    AL
## 4       1   6/8/1951 0:00:00     0900       CST     89    MADISON    AL
## 5       1 11/15/1951 0:00:00     1500       CST     43    CULLMAN    AL
## 6       1 11/15/1951 0:00:00     2000       CST     77 LAUDERDALE    AL
##    EVTYPE BGN_RANGE BGN_AZI BGN_LOCATI END_DATE END_TIME COUNTY_END
## 1 TORNADO         0                                               0
## 2 TORNADO         0                                               0
## 3 TORNADO         0                                               0
## 4 TORNADO         0                                               0
## 5 TORNADO         0                                               0
## 6 TORNADO         0                                               0
##   COUNTYENDN END_RANGE END_AZI END_LOCATI LENGTH WIDTH F MAG FATALITIES
## 1         NA         0                      14.0   100 3   0          0
## 2         NA         0                       2.0   150 2   0          0
## 3         NA         0                       0.1   123 2   0          0
## 4         NA         0                       0.0   100 2   0          0
## 5         NA         0                       0.0   150 2   0          0
## 6         NA         0                       1.5   177 2   0          0
##   INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP WFO STATEOFFIC ZONENAMES
## 1       15    25.0          K       0                                    
## 2        0     2.5          K       0                                    
## 3        2    25.0          K       0                                    
## 4        2     2.5          K       0                                    
## 5        2     2.5          K       0                                    
## 6        6     2.5          K       0                                    
##   LATITUDE LONGITUDE LATITUDE_E LONGITUDE_ REMARKS REFNUM
## 1     3040      8812       3051       8806              1
## 2     3042      8755          0          0              2
## 3     3340      8742          0          0              3
## 4     3458      8626          0          0              4
## 5     3412      8642          0          0              5
## 6     3450      8748          0          0              6
```

## Processing data


```r
# From the dataset we select first only the variables relevant to our analysis, and create a new dataset storm_data.
# Pls observe we keep the original data intact throughout our analysys and calculations. Transformations are done in storm_data

selected_variables <- c("EVTYPE", "FATALITIES", "INJURIES", 
                        "PROPDMG", "PROPDMGEXP", "CROPDMG", "CROPDMGEXP") 
storm_data <- tbl_df(data) %>% select(one_of(selected_variables))

# Normalizing and calculating the economic impact

# We first normalize the expenditures as per described in 
# the National Weather Service Storm # Data Documentation.
# Normalized values will be stored in PROPDMG_NORM and CROPDMG_NORM 
# according to the following rules:
# B is billiion, i.e. 1000000000
# M is millions, i.e. 1000000
# K is thousand, i.e. 1000
# All other levels will not be taken in consideration, 
# because there is not enough information, and we will set them as NA

# Auxliary function to nomarlize PROPDMGEXP and CROPDMGEXP
ExpenditureNormalization <- function(exp) {
        new_exp <- 0
        
        new_exp[grep("K|k", exp)] <- "1000"
        new_exp[grep("M|m", exp)] <- "1000000"
        new_exp[grep("B|b", exp)] <- "1000000000"
        as.numeric(new_exp)
}

# Creating two new variables PROPDMG_NORM and CROPDMG_NORM with
# the total damage expenditures (values in $)
storm_data <- storm_data %>% mutate(
        PROPDMG_NORM = PROPDMG * ExpenditureNormalization(PROPDMGEXP),
        CROPDMG_NORM = CROPDMG * ExpenditureNormalization(CROPDMGEXP))

# Creating a summarized dataset with the sum of fatalities,
# injuries and fatalities + injuries per event type EVTYPE

summary_calculations <- storm_data %>% group_by(EVTYPE) %>%
        summarise(
                fatalities = sum(FATALITIES, na.rm = TRUE),
                injuries= sum(INJURIES, na.rm = TRUE),
                total_human = fatalities + injuries,
                property = sum(PROPDMG_NORM, na.rm = TRUE),
                crop = sum(CROPDMG_NORM, na.rm = TRUE),
                total_economic = property + crop
        )
```

# Graphs


```r
df_graph_human <- summary_calculations %>% top_n(10, total_human)
graph_human <- ggplot(df_graph_human, aes(EVTYPE)) +
        geom_bar(aes(weight=total_human)) +
        labs(title = "Top 10 events most harmful to population health",
             x = "Event", y = "Human fatalities plus injuries") +
        theme(axis.text.x = element_text(angle = 90, hjust = 1))

df_graph_economic <- summary_calculations %>% top_n(10, total_economic)
graph_economic <- ggplot(df_graph_economic, aes(EVTYPE)) + geom_bar(aes(weight=total_economic)) +
        labs(title = "Top 10 events  with the greatest economic consequences\n
             Property plus Crops",
             x = "Event", y = "Dolars") +
        theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

# Results 


```r
#  Selecting in descending order the "Top 10" events causing the 
#  highest fatalities, injuries, fatalities + injuries, property, 
#  crop and property + crop damages

top10_fatalities <- summary_calculations %>% top_n(10, fatalities) %>%
        select(EVTYPE, fatalities) %>% arrange(desc(fatalities))

top10_injuries <- summary_calculations %>% top_n(10, injuries) %>%
        select(EVTYPE, injuries) %>% arrange(desc(injuries))

top10_human <- summary_calculations %>% top_n(10, total_human) %>%
        select(EVTYPE, total_human) %>% arrange(desc(total_human))

top10_property <- summary_calculations %>% top_n(10, property) %>%
        select(EVTYPE, property) %>% arrange(desc(property))

top10_crop <- summary_calculations %>% top_n(10, crop) %>%
        select(EVTYPE, crop) %>% arrange(desc(crop))

top10_economic <- summary_calculations %>% top_n(10, total_economic) %>%
        select(EVTYPE, total_economic) %>% arrange(desc(total_economic))
```

## Population Health Impact
The events most harmful to population health are Tornados as shown per the **graph** and **table** below. They describe the **total fatalities plus injuries** for the 10 most impacting events.

![](StormDataAnalysisV3_files/figure-html/plot graph_human-1.png) 

```
## Source: local data frame [10 x 2]
## 
##               EVTYPE total_human
## 1            TORNADO       96979
## 2     EXCESSIVE HEAT        8428
## 3          TSTM WIND        7461
## 4              FLOOD        7259
## 5          LIGHTNING        6046
## 6               HEAT        3037
## 7        FLASH FLOOD        2755
## 8          ICE STORM        2064
## 9  THUNDERSTORM WIND        1621
## 10      WINTER STORM        1527
```

Below we present separately the the number of fatalities and injuries for the 10 most impacting events:    

### Fatalities

```
## Source: local data frame [10 x 2]
## 
##            EVTYPE fatalities
## 1         TORNADO       5633
## 2  EXCESSIVE HEAT       1903
## 3     FLASH FLOOD        978
## 4            HEAT        937
## 5       LIGHTNING        816
## 6       TSTM WIND        504
## 7           FLOOD        470
## 8     RIP CURRENT        368
## 9       HIGH WIND        248
## 10      AVALANCHE        224
```

### Injuries


```
## Source: local data frame [10 x 2]
## 
##               EVTYPE injuries
## 1            TORNADO    91346
## 2          TSTM WIND     6957
## 3              FLOOD     6789
## 4     EXCESSIVE HEAT     6525
## 5          LIGHTNING     5230
## 6               HEAT     2100
## 7          ICE STORM     1975
## 8        FLASH FLOOD     1777
## 9  THUNDERSTORM WIND     1488
## 10              HAIL     1361
```

## Economical consequences
The events most harmful to population health are Floods as shown per the **graph** and **table** below. They describe **total property plus crop** economical impact for the 10 most impacting events.

![](StormDataAnalysisV3_files/figure-html/plot graph_economic-1.png) 

```
## Source: local data frame [10 x 2]
## 
##               EVTYPE total_economic
## 1              FLOOD   150319678250
## 2  HURRICANE/TYPHOON    71913712800
## 3            TORNADO    57352113590
## 4        STORM SURGE    43323541000
## 5               HAIL    18758221170
## 6        FLASH FLOOD    17562128610
## 7            DROUGHT    15018672000
## 8          HURRICANE    14610229010
## 9        RIVER FLOOD    10148404500
## 10         ICE STORM     8967041310
```

Below we present in detailing the property and crop impact in dolars for the 10 most impacting events:  

### Property


```
## Source: local data frame [10 x 2]
## 
##               EVTYPE     property
## 1              FLOOD 144657709800
## 2  HURRICANE/TYPHOON  69305840000
## 3            TORNADO  56937160480
## 4        STORM SURGE  43323536000
## 5        FLASH FLOOD  16140811510
## 6               HAIL  15732266720
## 7          HURRICANE  11868319010
## 8     TROPICAL STORM   7703890550
## 9       WINTER STORM   6688497250
## 10         HIGH WIND   5270046260
```

### Crop



```
## Source: local data frame [10 x 2]
## 
##               EVTYPE        crop
## 1            DROUGHT 13972566000
## 2              FLOOD  5661968450
## 3        RIVER FLOOD  5029459000
## 4          ICE STORM  5022113500
## 5               HAIL  3025954450
## 6          HURRICANE  2741910000
## 7  HURRICANE/TYPHOON  2607872800
## 8        FLASH FLOOD  1421317100
## 9       EXTREME COLD  1292973000
## 10      FROST/FREEZE  1094086000
```
