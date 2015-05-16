#The Most Harmful Types of Weather Events with Respect to Public Health and Economic Problems

##Synopsis

In this report, we aim to describe that which types of severe weather events are most harmful with respect to public health and economic problems. To find out the results, we obtained the data from the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. From these data, 

##Packages Included

Here are the packages needed in this report.


```r
library(dplyr)
```

##Data Processing

From the course [*Reproducible Research*](https://class.coursera.org/repdata-014), we get the [data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2) which is in the form of a comma-separated-value file compressed via the bzip2 algorithm to reduce its size. And you will find how some of the variables are constructed/defined in [National Weather Service Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf) and [National Climatic Data Center Storm Events FAQ](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2FNCDC%20Storm%20Events-FAQ%20Page.pdf).

###Downloading and Reading in the data

We first download the data and read in it. The header data is read and the strings are not treated as factors.


```r
if (!file.exists("StormData.csv.bz2")) {
    download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2", "StormData.csv.bz2")
}

stormdata <- read.csv(bzfile("StormData.csv.bz2"), header = T, stringsAsFactors = F, 
                      na.strings = "", quote = "")
```

After reading in the data we review some information in this dataset.


```r
str(stormdata)
```

```
## 'data.frame':	692288 obs. of  37 variables:
##  $ STATE__   : chr  "1.00" "1.00" "1.00" "1.00" ...
##  $ BGN_DATE  : chr  "4/18/1950 0:00:00" "4/18/1950 0:00:00" "2/20/1951 0:00:00" "6/8/1951 0:00:00" ...
##  $ BGN_TIME  : chr  "0130" "0145" "1600" "0900" ...
##  $ TIME_ZONE : chr  "CST" "CST" "CST" "CST" ...
##  $ COUNTY    : chr  "97.00" "3.00" "57.00" "89.00" ...
##  $ COUNTYNAME: chr  "MOBILE" "BALDWIN" "FAYETTE" "MADISON" ...
##  $ STATE     : chr  "AL" "AL" "AL" "AL" ...
##  $ EVTYPE    : chr  "TORNADO" "TORNADO" "TORNADO" "TORNADO" ...
##  $ BGN_RANGE : chr  "0.00" "0.00" "0.00" "0.00" ...
##  $ BGN_AZI   : chr  NA NA NA NA ...
##  $ BGN_LOCATI: chr  NA NA NA NA ...
##  $ END_DATE  : chr  NA NA NA NA ...
##  $ END_TIME  : chr  NA NA NA NA ...
##  $ COUNTY_END: chr  "0.00" "0.00" "0.00" "0.00" ...
##  $ COUNTYENDN: chr  NA NA NA NA ...
##  $ END_RANGE : chr  "0.00" "0.00" "0.00" "0.00" ...
##  $ END_AZI   : chr  NA NA NA NA ...
##  $ END_LOCATI: chr  NA NA NA NA ...
##  $ LENGTH    : chr  "14.00" "2.00" "0.10" "0.00" ...
##  $ WIDTH     : chr  "100.00" "150.00" "123.00" "100.00" ...
##  $ F         : chr  "3" "2" "2" "2" ...
##  $ MAG       : chr  "0.00" "0.00" "0.00" "0.00" ...
##  $ FATALITIES: chr  "0.00" "0.00" "0.00" "0.00" ...
##  $ INJURIES  : chr  "15.00" "0.00" "2.00" "2.00" ...
##  $ PROPDMG   : chr  "25.00" "2.50" "25.00" "2.50" ...
##  $ PROPDMGEXP: chr  "K" "K" "K" "K" ...
##  $ CROPDMG   : chr  "0.00" "0.00" "0.00" "0.00" ...
##  $ CROPDMGEXP: chr  NA NA NA NA ...
##  $ WFO       : chr  NA NA NA NA ...
##  $ STATEOFFIC: chr  NA NA NA NA ...
##  $ ZONENAMES : chr  NA NA NA NA ...
##  $ LATITUDE  : chr  "3040.00" "3042.00" "3340.00" "3458.00" ...
##  $ LONGITUDE : chr  "8812.00" "8755.00" "8742.00" "8626.00" ...
##  $ LATITUDE_E: chr  "3051.00" "0.00" "0.00" "0.00" ...
##  $ LONGITUDE_: chr  "8806.00" "0.00" "0.00" "0.00" ...
##  $ REMARKS   : chr  NA NA NA NA ...
##  $ REFNUM    : chr  "1.00" "2.00" "3.00" "4.00" ...
```

###Getting the Subset of the Data

The columns we are interested in are as follows:

-   `EVTYPE`: The weather events.

-   `FATALITIES`: The number of fatalities.

-   `INJURIES`: The number of injuries.

-   `PROPDMG`: The estimates of property damage entered as actual dollar amounts.

-   `PROPDMGEXP`: A multiplier where Hundred (H), Thousand (K), Million (M), Billion (B)

-   `CROPDMG`: Crop damage information.

-   `CROPDMGEXP`: A multiplier where Hundred (H), Thousand (K), Million (M), Billion (B)


```r
sub_storm <- select(stormdata, EVTYPE, FATALITIES, INJURIES, PROPDMG, PROPDMGEXP, CROPDMG,
                    CROPDMGEXP)
```

We only choose the rows where the events of `EVTYPE` are permitted storm data events.

```r
permitted_events <- c("Astronomical Low Tide", "Avalanche", "Blizzard", "Coastal Flood", "Cold",
                      "Wind Chill, Debris Flow", "Dense Fog", "Dense Smoke", "Drought", 
                      "Dust Devil", "Dust Storm", "Excessive Heat", "Extreme Cold", "Wind Chill",
                      "Flash Flood", "Flood", "Frost", "Freeze", "Funnel Cloud", "Freezing Fog",
                      "Hail", "Heat", "Heavy Rain", "Heavy Snow", "High Surf", "High Wind",
                      "Hurricane","Typhoon", "Ice Storm", "Lake-Effect Snow", "Lakeshore Flood",
                      "Lightning", "Marine Hail", "Marine High Wind", "Marine Strong Wind", 
                      "Marine Thunderstorm Wind", "Rip Current", "Seiche, Sleet", "Storm Surge",
                      "Tide", "Strong Wind", "Thunderstorm Wind", "Tornado", "Tropical Depression",
                      "Tropical Storm", "Tsunami", "Volcanic Ash", "Waterspout", "Wildfire", 
                      "Winter Storm", "Winter Weather")
permitted_events <- toupper(permitted_events)
sub_storm <- filter(sub_storm, !is.na(EVTYPE))
Encoding(sub_storm$EVTYPE) <- "UTF-8"
sub_storm$EVTYPE <- toupper(as.character(sub_storm$EVTYPE))
sub_storm <- filter(sub_storm, EVTYPE %in% permitted_events)
```


```r
class(sub_storm)
```

```
## [1] "data.frame"
```

```r
head(unique(sub_storm$CROPDMGEXP), 100)
```

```
## [1] NA  "K" "M" "B" "0" "k"
```

We also need to change the class of `EVTYPE`, `FATALITIES`, `INJURIES`, `PROPDMG`, `PROPDMGEXP`, `CROPDMG`, `CROPDMGEXP` into numeric.


```r
for (i in 1:nrow(sub_storm)) {
    if (!is.na(sub_storm[i, "PROPDMGEXP"])) {
        if (sub_storm[i, "PROPDMGEXP"] == "B") {
            sub_storm[i, "PROPDMGEXP"] = 9
        } else if (sub_storm[i, "PROPDMGEXP"] == "M") {
            sub_storm[i, "PROPDMGEXP"] = 6
        } else if (sub_storm[i, "PROPDMGEXP"] == "K") {
            sub_storm[i, "PROPDMGEXP"] = 3
        } else if (sub_storm[i, "PROPDMGEXP"] == "H" | sub_storm[i, "PROPDMGEXP"] == 
            "h") {
            sub_storm[i, "PROPDMGEXP"] = 2
        } else {
            sub_storm[i, "PROPDMGEXP"] = as.numeric(sub_storm[i, "PROPDMGEXP"])
        }
    }
}
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```
## Warning: NAs introduced by coercion
```

```r
for (i in 1:nrow(sub_storm)) {
    if (!is.na(sub_storm[i, "CROPDMGEXP"])) {
        if (sub_storm[i, "CROPDMGEXP"] == "B") {
            sub_storm[i, "CROPDMGEXP"] = 9
        } else if (sub_storm[i, "CROPDMGEXP"] == "M") {
            sub_storm[i, "CROPDMGEXP"] = 6
        } else if (sub_storm[i, "CROPDMGEXP"] == "K") {
            sub_storm[i, "CROPDMGEXP"] = 3
        } else if (sub_storm[i, "CROPDMGEXP"] == "H" | sub_storm[i, "CROPDMGEXP"] == 
            "h") {
            sub_storm[i, "CROPDMGEXP"] = 2
        } else {
            sub_storm[i, "CROPDMGEXP"] = as.numeric(sub_storm[i, "PROPDMGEXP"])
        }
    }
}
for (i in 2:ncol(sub_storm)) {
    sub_storm[, i] <- as.numeric(sub_storm[, i])
}
```

We then group the subset by `EVTYPE` and summarise the other four columns with the function `sum()`. 


```r
g_event <- group_by(sub_storm, EVTYPE)
tail(g_event)
```

```
## Source: local data frame [6 x 7]
## Groups: EVTYPE
## 
##        EVTYPE FATALITIES INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP
## 1        HAIL          0        0       0         NA     250          3
## 2        HAIL          0        0       0         NA       0         NA
## 3        HAIL          0        0     200          3     100          3
## 4 FLASH FLOOD          0        0       0         NA       0         NA
## 5        HAIL          0        0       0         NA       0         NA
## 6        HAIL          0        0       0         NA       0         NA
```


##Results


