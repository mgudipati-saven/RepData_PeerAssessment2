# Severe Weather Events that have Greatest Health and Economic Consequences


## Synopsis

## Loading and Processing the Raw Data
The data for this analysis comes from [Storm Data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2) in bzip2 compressed CSV format. The events in the database start in the year 1950 and end in November 2011. In the earlier years of the database there are generally fewer events recorded, most likely due to a lack of good records. More recent years should be considered more complete.

### Reading the data
First unzip the bzip2 compressed file, process the CSV formatted data and read into a data table.


```r
library(data.table)
NOAA.DT = data.table(read.csv(bzfile("repdata-data-StormData.csv.bz2")))
```


Check the first few rows. There are **902,297** rows in this dataset.

```r
dim(NOAA.DT)
```

```
## [1] 902297     37
```

```r
head(NOAA.DT, 5)
```

```
##   STATE__           BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE
## 1       1  4/18/1950 0:00:00     0130       CST     97     MOBILE    AL
## 2       1  4/18/1950 0:00:00     0145       CST      3    BALDWIN    AL
## 3       1  2/20/1951 0:00:00     1600       CST     57    FAYETTE    AL
## 4       1   6/8/1951 0:00:00     0900       CST     89    MADISON    AL
## 5       1 11/15/1951 0:00:00     1500       CST     43    CULLMAN    AL
##    EVTYPE BGN_RANGE BGN_AZI BGN_LOCATI END_DATE END_TIME COUNTY_END
## 1 TORNADO         0                                               0
## 2 TORNADO         0                                               0
## 3 TORNADO         0                                               0
## 4 TORNADO         0                                               0
## 5 TORNADO         0                                               0
##   COUNTYENDN END_RANGE END_AZI END_LOCATI LENGTH WIDTH F MAG FATALITIES
## 1         NA         0                      14.0   100 3   0          0
## 2         NA         0                       2.0   150 2   0          0
## 3         NA         0                       0.1   123 2   0          0
## 4         NA         0                       0.0   100 2   0          0
## 5         NA         0                       0.0   150 2   0          0
##   INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP WFO STATEOFFIC ZONENAMES
## 1       15    25.0          K       0                                    
## 2        0     2.5          K       0                                    
## 3        2    25.0          K       0                                    
## 4        2     2.5          K       0                                    
## 5        2     2.5          K       0                                    
##   LATITUDE LONGITUDE LATITUDE_E LONGITUDE_ REMARKS REFNUM
## 1     3040      8812       3051       8806              1
## 2     3042      8755          0          0              2
## 3     3340      8742          0          0              3
## 4     3458      8626          0          0              4
## 5     3412      8642          0          0              5
```


Filter out the observations if fatalities or injuries or crop damage or property damage have zero values. This will allow us to work with a smaller subset of the data.

```r
noaa.non.zero.data = NOAA.DT[NOAA.DT$FATALITIES > 0 | NOAA.DT$INJURIES > 0 | 
    NOAA.DT$PROPDMG > 0 | NOAA.DT$CROPDMG > 0, ]
dim(noaa.non.zero.data)
```

```
## [1] 254633     37
```


Interested in **EVTYPE, FATALITIES, INJURIES, PROPDMG and CROPDMG** columns as they are related to human health and economic consequences. Extract these columns and print their brief summary. **Observe that there are no missing values.**


```r
summary(NOAA.DT$EVTYPE)
```

```
##                     HAIL                TSTM WIND        THUNDERSTORM WIND 
##                   288661                   219940                    82563 
##                  TORNADO              FLASH FLOOD                    FLOOD 
##                    60652                    54277                    25326 
##       THUNDERSTORM WINDS                HIGH WIND                LIGHTNING 
##                    20843                    20212                    15754 
##               HEAVY SNOW               HEAVY RAIN             WINTER STORM 
##                    15708                    11723                    11433 
##           WINTER WEATHER             FUNNEL CLOUD         MARINE TSTM WIND 
##                     7026                     6839                     6175 
## MARINE THUNDERSTORM WIND               WATERSPOUT              STRONG WIND 
##                     5812                     3796                     3566 
##     URBAN/SML STREAM FLD                 WILDFIRE                 BLIZZARD 
##                     3392                     2761                     2719 
##                  DROUGHT                ICE STORM           EXCESSIVE HEAT 
##                     2488                     2006                     1678 
##               HIGH WINDS         WILD/FOREST FIRE             FROST/FREEZE 
##                     1533                     1457                     1342 
##                DENSE FOG       WINTER WEATHER/MIX           TSTM WIND/HAIL 
##                     1293                     1104                     1028 
##  EXTREME COLD/WIND CHILL                     HEAT                HIGH SURF 
##                     1002                      767                      725 
##           TROPICAL STORM           FLASH FLOODING             EXTREME COLD 
##                      690                      682                      655 
##            COASTAL FLOOD         LAKE-EFFECT SNOW        FLOOD/FLASH FLOOD 
##                      650                      636                      624 
##                LANDSLIDE                     SNOW          COLD/WIND CHILL 
##                      600                      587                      539 
##                      FOG              RIP CURRENT              MARINE HAIL 
##                      538                      470                      442 
##               DUST STORM                AVALANCHE                     WIND 
##                      427                      386                      340 
##             RIP CURRENTS              STORM SURGE            FREEZING RAIN 
##                      304                      261                      250 
##              URBAN FLOOD     HEAVY SURF/HIGH SURF        EXTREME WINDCHILL 
##                      249                      228                      204 
##             STRONG WINDS           DRY MICROBURST    ASTRONOMICAL LOW TIDE 
##                      196                      186                      174 
##                HURRICANE              RIVER FLOOD               LIGHT SNOW 
##                      174                      173                      154 
##         STORM SURGE/TIDE            RECORD WARMTH         COASTAL FLOODING 
##                      148                      146                      143 
##               DUST DEVIL         MARINE HIGH WIND        UNSEASONABLY WARM 
##                      141                      135                      126 
##                 FLOODING   ASTRONOMICAL HIGH TIDE        MODERATE SNOWFALL 
##                      120                      103                      101 
##           URBAN FLOODING               WINTRY MIX        HURRICANE/TYPHOON 
##                       98                       90                       88 
##            FUNNEL CLOUDS               HEAVY SURF              RECORD HEAT 
##                       87                       84                       81 
##                   FREEZE                HEAT WAVE                     COLD 
##                       74                       74                       72 
##              RECORD COLD                      ICE  THUNDERSTORM WINDS HAIL 
##                       64                       61                       61 
##      TROPICAL DEPRESSION                    SLEET         UNSEASONABLY DRY 
##                       60                       59                       56 
##                    FROST              GUSTY WINDS      THUNDERSTORM WINDSS 
##                       53                       53                       51 
##       MARINE STRONG WIND                    OTHER               SMALL HAIL 
##                       48                       48                       47 
##                   FUNNEL             FREEZING FOG             THUNDERSTORM 
##                       46                       45                       45 
##       Temperature record          TSTM WIND (G45)         Coastal Flooding 
##                       43                       39                       38 
##              WATERSPOUTS    MONTHLY PRECIPITATION                    WINDS 
##                       37                       36                       36 
##                  (Other) 
##                     2940
```

```r
mean(is.na(NOAA.DT$EVTYPE))
```

```
## [1] 0
```

```r
summary(NOAA.DT$FATALITIES)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0       0       0       0       0     583
```

```r
mean(is.na(NOAA.DT$FATALITIES))
```

```
## [1] 0
```

```r
summary(NOAA.DT$INJURIES)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     0.0     0.0     0.0     0.2     0.0  1700.0
```

```r
mean(is.na(NOAA.DT$INJURIES))
```

```
## [1] 0
```

```r
summary(NOAA.DT$PROPDMG)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0       0       0      12       0    5000
```

```r
mean(is.na(NOAA.DT$PROPDMG))
```

```
## [1] 0
```

```r
summary(NOAA.DT$CROPDMG)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     0.0     0.0     0.0     1.5     0.0   990.0
```

```r
mean(is.na(NOAA.DT$CROPDMG))
```

```
## [1] 0
```


## Results

### Events that are most harmful to population health
In order to arrive at this, consider EVTYPE, FATALITIES and INJURIES columns in the dataset. Aggregate FATALITIES and INJURIES by EVTTYPE and sort them in decresing order. Select the top few EVTYPEs.


```r
library(data.table)
fatalities.by.evtype = noaa.non.zero.data[, list(Fatalities = sum(FATALITIES)), 
    by = list(EVTYPE)][order(Fatalities, decreasing = TRUE)][Fatalities > 100]
fatalities.by.evtype
```

```
##                      EVTYPE Fatalities
##  1:                 TORNADO       5633
##  2:          EXCESSIVE HEAT       1903
##  3:             FLASH FLOOD        978
##  4:                    HEAT        937
##  5:               LIGHTNING        816
##  6:               TSTM WIND        504
##  7:                   FLOOD        470
##  8:             RIP CURRENT        368
##  9:               HIGH WIND        248
## 10:               AVALANCHE        224
## 11:            WINTER STORM        206
## 12:            RIP CURRENTS        204
## 13:               HEAT WAVE        172
## 14:            EXTREME COLD        160
## 15:       THUNDERSTORM WIND        133
## 16:              HEAVY SNOW        127
## 17: EXTREME COLD/WIND CHILL        125
## 18:             STRONG WIND        103
## 19:               HIGH SURF        101
## 20:                BLIZZARD        101
```

```r
injuries.by.evtype = noaa.non.zero.data[, list(Injuries = sum(INJURIES)), by = EVTYPE][order(Injuries, 
    decreasing = TRUE)][Injuries > 1000]
injuries.by.evtype
```

```
##                EVTYPE Injuries
##  1:           TORNADO    91346
##  2:         TSTM WIND     6957
##  3:             FLOOD     6789
##  4:    EXCESSIVE HEAT     6525
##  5:         LIGHTNING     5230
##  6:              HEAT     2100
##  7:         ICE STORM     1975
##  8:       FLASH FLOOD     1777
##  9: THUNDERSTORM WIND     1488
## 10:              HAIL     1361
## 11:      WINTER STORM     1321
## 12: HURRICANE/TYPHOON     1275
## 13:         HIGH WIND     1137
## 14:        HEAVY SNOW     1021
```


Plot bar graphs for Fatalities and Injuries across all the EVTYPEs. One can figure out the top weather events that cause great human health hazards.

```r
library(ggplot2)
library(gridExtra)
```

```
## Loading required package: grid
```

```r
g = ggplot(fatalities.by.evtype, aes(EVTYPE, Fatalities))
graph1 = g + geom_bar(stat = "identity") + theme(axis.text.x = element_text(angle = 90, 
    vjust = 0.5, hjust = 1)) + labs(x = "Event Type")

g = ggplot(injuries.by.evtype, aes(EVTYPE, Injuries))
graph2 = g + geom_bar(stat = "identity") + theme(axis.text.x = element_text(angle = 90, 
    vjust = 0.5, hjust = 1)) + labs(x = "Event Type")

grid.arrange(graph1, graph2, ncol = 2, main = "Top Weather Events Causing Human Harm")
```

![plot of chunk healthplot](figure/healthplot.png) 

