# Analysis of Severe Weather Events

> Title: Your document should have a title that **briefly** summarizes your data
> analysis

Peer assessment 2 assignment for Coursera course [Reproducible Research](Reproducible Research).


## Synopsis

> Synopsis: Immediately after the title, there should be a **synopsis** which
> describes and summarizes your analysis in at **most 10 complete sentences**.

**To be completed**

> ## Introduction
> 
> Storms and other severe weather events can cause both public health and economic
> problems for communities and municipalities. Many severe events can result in
> fatalities, injuries, and property damage, and preventing such outcomes to the
> extent possible is a key concern.
> 
> This project involves exploring the U.S. National Oceanic and Atmospheric
> Administration's (NOAA) storm database. This database tracks characteristics of
> major storms and weather events in the United States, including when and where
> they occur, as well as estimates of any fatalities, injuries, and property
> damage.
> 
> ### Questions
> 
> Your data analysis must address the following questions:
> 
> Across the United States, which types of events (as indicated in the `EVTYPE`
> variable) are most harmful with respect to popuulation health?
> 
> Across the United States, which types of events have the greatest economic
> consequences?
> 
> Consider writing your report as if it were to be read by a government or
> municipal manager who might be responsible for preparing for severe weather
> events and will need to prioritize resources for different types of events.
> However, there is no need to make any specific recommendations in your report.


## Data Processing

> ## Data
> 
> The data for this assignment come in the form of a comma-separated-value file
> compressed via the bzip2 algorithm to reduce its size. You can download the file
> from the course web site:
> 
> [StormData](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2)
> [47Mb] There is also some documentation of the database available. Here you will
> find how some of the variables are constructed/defined.
> 
> National Weather Service [Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf)
> 
> National Climatic Data Center Storm Events [FAQ](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2FNCDC%20Storm%20Events-FAQ%20Page.pdf)
> 
> The events in the database start in the year 1950 and end in November 2011. In
> the earlier years of the database there are generally fewer events recorded,
> most likely due to a lack of good records. More recent years should be
> considered more complete.

> There should be a section titled **Data Processing** which describes (in words
> and code) how the data were loaded into R and processed for analysis. In
> particular, your analysis must start from the raw CSV file containing the data.
> You cannot do any preprocessing outside the document. If preprocessing is time-
> consuming you may consider using the `cache = TRUE` option for certain code
> chunks.

Load packages.
  

```r
packages <- c("data.table", "ggplot2", "xtable")
sapply(packages, require, character.only = TRUE, quietly = TRUE)
```

```
## data.table    ggplot2     xtable 
##       TRUE       TRUE       TRUE
```


Fix URL reading for knitr. See [Stackoverflow](http://stackoverflow.com/a/20003380).


```r
setInternet2(TRUE)
```


### Download and unzip files

**Don't run this subsection during testing**

Download the storm data documentation files.


```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf"
f <- file.path(getwd(), "StormDataDocumentation.pdf")
download.file(url, f, mode = "wb")
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2FNCDC%20Storm%20Events-FAQ%20Page.pdf"
f <- file.path(getwd(), "StormEventsFAQ.pdf")
download.file(url, f, mode = "wb")
```


Download the zipped storm data file.


```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
f <- file.path(getwd(), "StormData.csv.bz2")
download.file(url, f, mode = "wb")
```


Unzip the data file.


```r
executable <- file.path("C:", "Program Files (x86)", "7-Zip", "7z.exe")
parameters <- "x"
switch <- "-aoa"
cmd <- paste(paste0("\"", executable, "\""), parameters, paste0("\"", f, "\""), 
    switch)
cmd
system(cmd)
```



### Read data file

Read the CSV file as a data frame.
Then convert to a data table.


```r
f <- file.path(getwd(), "StormData.csv.bz2")
D <- read.csv(f, stringsAsFactors = FALSE)
D <- data.table(D)
str(D)
```

```
## Classes 'data.table' and 'data.frame':	902297 obs. of  37 variables:
##  $ STATE__   : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_DATE  : chr  "4/18/1950 0:00:00" "4/18/1950 0:00:00" "2/20/1951 0:00:00" "6/8/1951 0:00:00" ...
##  $ BGN_TIME  : chr  "0130" "0145" "1600" "0900" ...
##  $ TIME_ZONE : chr  "CST" "CST" "CST" "CST" ...
##  $ COUNTY    : num  97 3 57 89 43 77 9 123 125 57 ...
##  $ COUNTYNAME: chr  "MOBILE" "BALDWIN" "FAYETTE" "MADISON" ...
##  $ STATE     : chr  "AL" "AL" "AL" "AL" ...
##  $ EVTYPE    : chr  "TORNADO" "TORNADO" "TORNADO" "TORNADO" ...
##  $ BGN_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ BGN_AZI   : chr  "" "" "" "" ...
##  $ BGN_LOCATI: chr  "" "" "" "" ...
##  $ END_DATE  : chr  "" "" "" "" ...
##  $ END_TIME  : chr  "" "" "" "" ...
##  $ COUNTY_END: num  0 0 0 0 0 0 0 0 0 0 ...
##  $ COUNTYENDN: logi  NA NA NA NA NA NA ...
##  $ END_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ END_AZI   : chr  "" "" "" "" ...
##  $ END_LOCATI: chr  "" "" "" "" ...
##  $ LENGTH    : num  14 2 0.1 0 0 1.5 1.5 0 3.3 2.3 ...
##  $ WIDTH     : num  100 150 123 100 150 177 33 33 100 100 ...
##  $ F         : int  3 2 2 2 2 2 2 1 3 3 ...
##  $ MAG       : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: chr  "K" "K" "K" "K" ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: chr  "" "" "" "" ...
##  $ WFO       : chr  "" "" "" "" ...
##  $ STATEOFFIC: chr  "" "" "" "" ...
##  $ ZONENAMES : chr  "" "" "" "" ...
##  $ LATITUDE  : num  3040 3042 3340 3458 3412 ...
##  $ LONGITUDE : num  8812 8755 8742 8626 8642 ...
##  $ LATITUDE_E: num  3051 0 0 0 0 ...
##  $ LONGITUDE_: num  8806 0 0 0 0 ...
##  $ REMARKS   : chr  "" "" "" "" ...
##  $ REFNUM    : num  1 2 3 4 5 6 7 8 9 10 ...
##  - attr(*, ".internal.selfref")=<externalptr>
```



### Clean data

Rename the variables to lowercase for ease of coding.


```r
old <- names(D)
new <- tolower(old)
setnames(D, old, new)
```


Convert the `bgn_date` character class variable to a date class variable.


```r
bgn_date <- strsplit(D$bgn_date, "[^[:digit:]]")
bgn_date <- unlist(bgn_date)
bgn_date <- as.numeric(bgn_date)
bgn_date <- matrix(bgn_date, nrow = nrow(D), byrow = TRUE)
dateStr <- sprintf("%4d%02d%02d", bgn_date[, 3], bgn_date[, 1], bgn_date[, 2])
D <- D[, `:=`(beginDate, as.Date(dateStr, format = "%Y%m%d"))]
```


#### Group event types

List the number of unique values of `evtype`.
The number of unique values is too large to manage without some grouping.


```r
message(sprintf("Number of unique values of evtype: %.0d", length(unique(D$evtype))))
```

```
## Number of unique values of evtype: 985
```


`evtype` needs a lot of data cleaning.
Particularly, values need to be grouped to resolve spelling variations.
Also, records can have multiple events listed in the `evtype` variable.
Create indicator variables for common event types.

Define a helper function `freqtab` to help with grouping `evtype` values.


```r
indicator <- function(x) {
    indicator <- grepl(x, D$evtype, ignore.case = TRUE)
    show(unique(D[indicator, evtype]))
    indicator
}
```


Create an indicator for variations of **Wind**.


```r
regex <- "(WIND)|(WND)"
D <- D[, `:=`(eventWind, indicator(regex))]
```

```
##   [1] "TSTM WIND"                      "HURRICANE OPAL/HIGH WINDS"     
##   [3] "THUNDERSTORM WINDS"             "THUNDERSTORM WIND"             
##   [5] "HIGH WINDS"                     "THUNDERSTORM WINDS LIGHTNING"  
##   [7] "THUNDERSTORM WINDS/HAIL"        "WIND"                          
##   [9] "THUNDERSTORM WINDS HAIL"        "HIGH WIND"                     
##  [11] "WIND CHILL"                     "HIGH WIND/BLIZZARD"            
##  [13] "HIGH WIND AND HIGH TIDES"       "HIGH WIND/BLIZZARD/FREEZING RA"
##  [15] "HIGH WIND AND HEAVY SNOW"       "RECORD COLD AND HIGH WIND"     
##  [17] "HIGH WINDS HEAVY RAINS"         "HIGH WIND/ BLIZZARD"           
##  [19] "BLIZZARD/HIGH WIND"             "HIGH WIND/LOW WIND CHILL"      
##  [21] "HIGH WINDS AND WIND CHILL"      "HEAVY SNOW/HIGH WINDS/FREEZING"
##  [23] "WIND CHILL/HIGH WIND"           "HIGH WIND/WIND CHILL/BLIZZARD" 
##  [25] "HIGH WIND/WIND CHILL"           "HIGH WIND/HEAVY SNOW"          
##  [27] "HIGH WIND/SEAS"                 "HIGH WINDS/HEAVY RAIN"         
##  [29] "HEAVY SNOW/WIND"                "WIND DAMAGE"                   
##  [31] "THUNDERSTORM WINDS/FUNNEL CLOU" "WINTER STORM/HIGH WIND"        
##  [33] "WINTER STORM/HIGH WINDS"        "GUSTY WINDS"                   
##  [35] "STRONG WINDS"                   "SNOW AND WIND"                 
##  [37] "HIGH WINDS DUST STORM"          "WINTER STORM HIGH WINDS"       
##  [39] "SEVERE THUNDERSTORM WINDS"      "THUNDERSTORMS WINDS"           
##  [41] "FLOOD/RAIN/WINDS"               "WINDS"                         
##  [43] "FLASH FLOOD WINDS"              "STRONG WIND"                   
##  [45] "HIGH WIND DAMAGE"               "FLOOD/RAIN/WIND"               
##  [47] "DOWNBURST WINDS"                "DRY MICROBURST WINDS"          
##  [49] "DRY MIRCOBURST WINDS"           "MICROBURST WINDS"              
##  [51] "HIGH WINDS 57"                  "HIGH WINDS 66"                 
##  [53] "HIGH WINDS 76"                  "HIGH WINDS 63"                 
##  [55] "HIGH WINDS 67"                  "HEAVY SNOW/HIGH WINDS"         
##  [57] "HIGH WINDS 82"                  "HIGH WINDS 80"                 
##  [59] "HIGH WINDS 58"                  "LIGHTNING THUNDERSTORM WINDSS" 
##  [61] "HIGH WINDS 73"                  "HIGH WINDS 55"                 
##  [63] "THUNDERSTORM WINDS 60"          "THUNDERSTORM WINDSS"           
##  [65] "HIGH WINDS/FLOODING"            "TORNADOES, TSTM WIND, HAIL"    
##  [67] "LIGHTNING THUNDERSTORM WINDS"   "THUNDERSTORM WINDS53"          
##  [69] "THUNDERSTORM WINDS 13"          "HEAVY SNOW/HIGH WIND"          
##  [71] "HIGH WINDS/"                    "EXTREME WIND CHILLS"           
##  [73] "HIGH  WINDS"                    "EXTREME WIND CHILL"            
##  [75] "GRADIENT WINDS"                 "THUNDERSTORM WINDS URBAN FLOOD"
##  [77] "THUNDERSTORM WINDS SMALL STREA" "BLOWING SNOW- EXTREME WIND CHI"
##  [79] "SNOW- HIGH WIND- WIND CHILL"    "THUNDERSTORM WINDS 2"          
##  [81] "TSTM WIND 51"                   "TSTM WIND 50"                  
##  [83] "TSTM WIND 52"                   "TSTM WIND 55"                  
##  [85] "THUNDERSTORM WINDS 61"          "THUNDERTORM WINDS"             
##  [87] "HAIL/WINDS"                     "WIND STORM"                    
##  [89] "HAIL/WIND"                      "WIND/HAIL"                     
##  [91] "THUNDERSTORMS WIND"             "THUNDERSTORM  WINDS"           
##  [93] "TUNDERSTORM WIND"               "THUNDERTSORM WIND"             
##  [95] "THUNDERSTORM WINDS/ HAIL"       "THUNDERSTORM WIND/LIGHTNING"   
##  [97] "THUNDESTORM WINDS"              "HIGH WIND 63"                  
##  [99] "HIGH WINDS/COASTAL FLOOD"       "THUNDERSTORM WIND G50"         
## [101] "THUNDERSTORM WINDS/HEAVY RAIN"  "THUNDERSTROM WINDS"            
## [103] "THUNDERSTORM WINDS      LE CEN" "BLIZZARD AND EXTREME WIND CHIL"
## [105] "LOW WIND CHILL"                 "BLOWING SNOW & EXTREME WIND CH"
## [107] "THUNDERSTORM WINDS G"           "DUST STORM/HIGH WINDS"         
## [109] "THUNDERSTORM WIND G60"          "THUNDERSTORM WINDS."           
## [111] "THUNDERSTORM WIND G55"          "THUNDERSTORM WINDS G60"        
## [113] "THUNDERSTORM WINDS FUNNEL CLOU" "THUNDERSTORM WINDS 62"         
## [115] "HEAVY SNOW AND HIGH WINDS"      "HEAVY SNOW/HIGH WINDS & FLOOD" 
## [117] "THUNDERSTORM WINDS/FLASH FLOOD" "HIGH WIND 70"                  
## [119] "THUNDERSTORM WINDS 53"          "RAIN AND WIND"                 
## [121] "THUNDERSTORM WIND 59"           "THUNDERSTORM WIND 52"          
## [123] "THUNDERSTORM WIND 69"           "LIGHTNING AND WINDS"           
## [125] "TSTM WIND G58"                  "THUNDERSTORMW WINDS"           
## [127] "THUNDERSTORM WIND 60 MPH"       "THUNDERSTORM WIND 65MPH"       
## [129] "THUNDERSTORM WIND/ TREES"       "THUNDERSTORM WIND/AWNING"      
## [131] "THUNDERSTORM WIND 98 MPH"       "THUNDERSTORM WIND TREES"       
## [133] "THUNDERSTORM WIND 59 MPH"       "THUNDERSTORM WINDS 63 MPH"     
## [135] "THUNDERSTORM WIND/ TREE"        "THUNDERSTORM WIND 65 MPH"      
## [137] "THUNDERSTORM WIND."             "THUNDERSTORM WIND 59 MPH."     
## [139] "THUNDERSTORM WINDSHAIL"         "THUDERSTORM WINDS"             
## [141] "STORM FORCE WINDS"              "THUNDERSTORM WINDS AND"        
## [143] "HEAVY RAIN; URBAN FLOOD WINDS;" "TSTM WIND DAMAGE"              
## [145] "RAIN/WIND"                      "THUNDERSTORM WINDS 50"         
## [147] "THUNDERSTORM WIND G52"          "THUNDERSTORM WINDS 52"         
## [149] "THUNDERSTORM WIND G51"          "THUNDERSTORM WIND G61"         
## [151] "THUNDERESTORM WINDS"            "THUNDERSTORM WINDS/FLOODING"   
## [153] "THUNDEERSTORM WINDS"            "THUNDERSTORM WIND 50"          
## [155] "THUNERSTORM WINDS"              "HIGH WINDS/COLD"               
## [157] "COLD/WINDS"                     "THUNDERSTORM WIND 56"          
## [159] "ICE/STRONG WINDS"               "EXTREME WIND CHILL/BLOWING SNO"
## [161] "SNOW/HIGH WINDS"                "HIGH WINDS/SNOW"               
## [163] "HEAVY SNOW AND STRONG WINDS"    "BLOWING SNOW/EXTREME WIND CHIL"
## [165] "THUNDERSTORM WIND/HAIL"         "TSTM WINDS"                    
## [167] "TSTM WIND 65)"                  "THUNDERSTORM WINDS/ FLOOD"     
## [169] "HIGH WIND AND SEAS"             "THUNDERSTORMWINDS"             
## [171] "THUNDERSTORM WINDS HEAVY RAIN"  "THUNDERSTROM WIND"             
## [173] "HIGH WIND 48"                   "EXTREME WINDCHILL"             
## [175] "TSTM WIND/HAIL"                 "High Wind"                     
## [177] "Tstm Wind"                      "Wind"                          
## [179] "Wind Damage"                    "Strong Wind"                   
## [181] "Heavy Rain and Wind"            "Thunderstorm Wind"             
## [183] "HEAVY RAIN/WIND"                "Strong Winds"                  
## [185] "Strong winds"                   "Whirlwind"                     
## [187] "Gusty Wind"                     "Gradient wind"                 
## [189] "Gusty wind/rain"                "GUSTY WIND/HVY RAIN"           
## [191] "TSTM WIND (G45)"                "Gusty Winds"                   
## [193] "GUSTY WIND"                     "TSTM WIND 40"                  
## [195] "TSTM WIND 45"                   "TSTM WIND (41)"                
## [197] "TSTM WIND (G40)"                "TSTM WND"                      
## [199] " TSTM WIND"                     "STRONG WIND GUST"              
## [201] "Gusty winds"                    "GRADIENT WIND"                 
## [203] "Flood/Strong Wind"              "TSTM WIND AND LIGHTNING"       
## [205] "gradient wind"                  "Heavy surf and wind"           
## [207] " TSTM WIND (G45)"               "TSTM WIND  (G45)"              
## [209] "HIGH WIND (G40)"                "TSTM WIND (G35)"               
## [211] "WAKE LOW WIND"                  "COLD WIND CHILL TEMPERATURES"  
## [213] "BITTER WIND CHILL"              "BITTER WIND CHILL TEMPERATURES"
## [215] "WIND ADVISORY"                  "GUSTY WIND/HAIL"               
## [217] "EXTREME WINDCHILL TEMPERATURES" "WIND AND WAVE"                 
## [219] " WIND"                          "TSTM WIND G45"                 
## [221] "NON-SEVERE WIND DAMAGE"         "THUNDERSTORM WIND (G40)"       
## [223] "WIND GUSTS"                     "GUSTY LAKE WIND"               
## [225] "WND"                            "NON-TSTM WIND"                 
## [227] "NON TSTM WIND"                  "GUSTY THUNDERSTORM WINDS"      
## [229] "MARINE TSTM WIND"               "WHIRLWIND"                     
## [231] "EXTREME COLD/WIND CHILL"        "GUSTY THUNDERSTORM WIND"       
## [233] "COLD/WIND CHILL"                "MARINE HIGH WIND"              
## [235] "MARINE THUNDERSTORM WIND"       "MARINE STRONG WIND"
```


Create an indicator for variations of **Hail**.


```r
regex <- "HAIL"
D <- D[, `:=`(eventHail, indicator(regex))]
```

```
##  [1] "HAIL"                       "THUNDERSTORM WINDS/HAIL"   
##  [3] "THUNDERSTORM WINDS HAIL"    "HAIL 1.75)"                
##  [5] "HAIL STORM"                 "HAIL 75"                   
##  [7] "TORNADOES, TSTM WIND, HAIL" "SMALL HAIL"                
##  [9] "HAIL 80"                    "FUNNEL CLOUD/HAIL"         
## [11] "HAIL 0.75"                  "HAIL 1.00"                 
## [13] "HAIL/WINDS"                 "HAIL/WIND"                 
## [15] "HAIL 1.75"                  "WIND/HAIL"                 
## [17] "THUNDERSTORM WINDS/ HAIL"   "HAIL 225"                  
## [19] "HAIL 0.88"                  "DEEP HAIL"                 
## [21] "HAIL 88"                    "HAIL 175"                  
## [23] "HAIL 100"                   "HAIL 150"                  
## [25] "HAIL 075"                   "HAIL 125"                  
## [27] "HAIL 200"                   "HAIL FLOODING"             
## [29] "HAIL DAMAGE"                "THUNDERSTORM HAIL"         
## [31] "HAIL 088"                   "THUNDERSTORM WINDSHAIL"    
## [33] "HAIL/ICY ROADS"             "HAIL ALOFT"                
## [35] "THUNDERSTORM WIND/HAIL"     "HAIL 275"                  
## [37] "HAIL 450"                   "HAILSTORM"                 
## [39] "HAILSTORMS"                 "TSTM WIND/HAIL"            
## [41] "small hail"                 "Hail(0.75)"                
## [43] "Small Hail"                 "GUSTY WIND/HAIL"           
## [45] "LATE SEASON HAIL"           "NON SEVERE HAIL"           
## [47] "MARINE HAIL"
```


Create an indicator for variations of **Flood**.


```r
regex <- "FLOOD"
D <- D[, `:=`(eventFlood, indicator(regex))]
```

```
##   [1] "ICE STORM/FLASH FLOOD"          "FLASH FLOOD"                   
##   [3] "FLASH FLOODING"                 "FLOODING"                      
##   [5] "FLOOD"                          "FLASH FLOODING/THUNDERSTORM WI"
##   [7] "BREAKUP FLOODING"               "RIVER FLOOD"                   
##   [9] "COASTAL FLOOD"                  "FLOOD WATCH/"                  
##  [11] "FLASH FLOODS"                   "FLOODING/HEAVY RAIN"           
##  [13] "HEAVY SURF COASTAL FLOODING"    "URBAN FLOODING"                
##  [15] "URBAN/SMALL FLOODING"           "LOCAL FLOOD"                   
##  [17] "FLOOD/FLASH FLOOD"              "FLOOD/RAIN/WINDS"              
##  [19] "FLASH FLOOD WINDS"              "URBAN/SMALL STREAM FLOODING"   
##  [21] "STREAM FLOODING"                "FLASH FLOOD/"                  
##  [23] "FLOOD/RAIN/WIND"                "SMALL STREAM URBAN FLOOD"      
##  [25] "URBAN FLOOD"                    "HEAVY RAIN/FLOODING"           
##  [27] "COASTAL FLOODING"               "HIGH WINDS/FLOODING"           
##  [29] "URBAN/SMALL STREAM FLOOD"       "MINOR FLOODING"                
##  [31] "URBAN/SMALL STREAM  FLOOD"      "URBAN AND SMALL STREAM FLOOD"  
##  [33] "SMALL STREAM FLOODING"          "FLOODS"                        
##  [35] "SMALL STREAM AND URBAN FLOODIN" "SMALL STREAM/URBAN FLOOD"      
##  [37] "SMALL STREAM AND URBAN FLOOD"   "RURAL FLOOD"                   
##  [39] "THUNDERSTORM WINDS URBAN FLOOD" "MAJOR FLOOD"                   
##  [41] "ICE JAM FLOODING"               "STREET FLOOD"                  
##  [43] "SMALL STREAM FLOOD"             "LAKE FLOOD"                    
##  [45] "URBAN AND SMALL STREAM FLOODIN" "RIVER AND STREAM FLOOD"        
##  [47] "MINOR FLOOD"                    "HIGH WINDS/COASTAL FLOOD"      
##  [49] "RIVER FLOODING"                 "FLOOD/RIVER FLOOD"             
##  [51] "MUD SLIDES URBAN FLOODING"      "HEAVY SNOW/HIGH WINDS & FLOOD" 
##  [53] "HAIL FLOODING"                  "THUNDERSTORM WINDS/FLASH FLOOD"
##  [55] "HEAVY RAIN AND FLOOD"           "LOCAL FLASH FLOOD"             
##  [57] "FLOOD/FLASH FLOODING"           "COASTAL/TIDAL FLOOD"           
##  [59] "FLASH FLOOD/FLOOD"              "FLASH FLOOD FROM ICE JAMS"     
##  [61] "FLASH FLOOD - HEAVY RAIN"       "FLASH FLOOD/ STREET"           
##  [63] "FLASH FLOOD/HEAVY RAIN"         "HEAVY RAIN; URBAN FLOOD WINDS;"
##  [65] "FLOOD FLASH"                    "FLOOD FLOOD/FLASH"             
##  [67] "TIDAL FLOOD"                    "FLOOD/FLASH"                   
##  [69] "HEAVY RAINS/FLOODING"           "THUNDERSTORM WINDS/FLOODING"   
##  [71] "HIGHWAY FLOODING"               "FLASH FLOOD/ FLOOD"            
##  [73] "HEAVY RAIN/MUDSLIDES/FLOOD"     "BEACH EROSION/COASTAL FLOOD"   
##  [75] "SNOWMELT FLOODING"              "FLASH FLOODING/FLOOD"          
##  [77] "BEACH FLOOD"                    "THUNDERSTORM WINDS/ FLOOD"     
##  [79] "FLOOD & HEAVY RAIN"             "FLOOD/FLASHFLOOD"              
##  [81] "URBAN SMALL STREAM FLOOD"       "URBAN FLOOD LANDSLIDE"         
##  [83] "URBAN FLOODS"                   "HEAVY RAIN/URBAN FLOOD"        
##  [85] "FLASH FLOOD/LANDSLIDE"          "LANDSLIDE/URBAN FLOOD"         
##  [87] "FLASH FLOOD LANDSLIDES"         "Minor Flooding"                
##  [89] "Ice jam flood (minor"           "Coastal Flooding"              
##  [91] "COASTALFLOOD"                   "Erosion/Cstl Flood"            
##  [93] "Tidal Flooding"                 "River Flooding"                
##  [95] "Flood/Flash Flood"              "STREET FLOODING"               
##  [97] "Flood"                          "TIDAL FLOODING"                
##  [99] " COASTAL FLOOD"                 "Urban Flooding"                
## [101] "Urban flood"                    "Urban Flood"                   
## [103] "Coastal Flood"                  "coastal flooding"              
## [105] "Flood/Strong Wind"              "COASTAL FLOODING/EROSION"      
## [107] "URBAN/STREET FLOODING"          "COASTAL  FLOODING/EROSION"     
## [109] "FLOOD/FLASH/FLOOD"              " FLASH FLOOD"                  
## [111] "CSTL FLOODING/EROSION"          "LAKESHORE FLOOD"
```


Create an indicator for variations of **Tornado**.


```r
regex <- "(NADO)|(\\bTOR\\S+?O\\b)"
D <- D[, `:=`(eventTornado, indicator(regex))]
```

```
##  [1] "TORNADO"                    "TORNADO F0"                
##  [3] "GUSTNADO AND"               "TORNADOS"                  
##  [5] "WATERSPOUT/TORNADO"         "WATERSPOUT TORNADO"        
##  [7] "WATERSPOUT-TORNADO"         "TORNADOES, TSTM WIND, HAIL"
##  [9] "GUSTNADO"                   "COLD AIR TORNADO"          
## [11] "WATERSPOUT/ TORNADO"        "TORNADO F3"                
## [13] "TORNDAO"                    "TORNADO F1"                
## [15] "TORNADO/WATERSPOUT"         "TORNADO F2"                
## [17] "TORNADOES"                  "TORNADO DEBRIS"
```


Create an indicator for variations of **Lightning**.


```r
regex <- "\\bL\\S+?G\\b"
D <- D[, `:=`(eventLightning, indicator(regex))]
```

```
##  [1] "LIGHTNING"                      "THUNDERSTORM WINDS LIGHTNING"  
##  [3] "LIGHTING"                       "LIGHTNING AND HEAVY RAIN"      
##  [5] "HEAVY RAIN/LIGHTNING"           "LIGHTNING/HEAVY RAIN"          
##  [7] "LIGHTNING THUNDERSTORM WINDSS"  "LIGHTNING THUNDERSTORM WINDS"  
##  [9] "LIGHTNING INJURY"               "LIGHTNING AND THUNDERSTORM WIN"
## [11] "LIGNTNING"                      "THUNDERSTORM WIND/LIGHTNING"   
## [13] "LIGHTNING."                     "LIGHTNING FIRE"                
## [15] "LIGHTNING DAMAGE"               "LIGHTNING AND WINDS"           
## [17] "LIGHTNING  WAUSEON"             "TSTM WIND AND LIGHTNING"       
## [19] " LIGHTNING"
```


Create an indicator for variations of **Snow, Ice, Freeze, or Winter Weather**.


```r
regex <- "(SNOW)|(ICE)|(ICY)|(FREEZ)|(WINT)"
D <- D[, `:=`(eventSnow, indicator(regex))]
```

```
##   [1] "FREEZING RAIN"                  "SNOW"                          
##   [3] "ICE STORM/FLASH FLOOD"          "SNOW/ICE"                      
##   [5] "WINTER STORM"                   "HEAVY SNOW"                    
##   [7] "FREEZE"                         "HIGH WIND/BLIZZARD/FREEZING RA"
##   [9] "HIGH WIND AND HEAVY SNOW"       "ICE STORM"                     
##  [11] "HEAVY SNOW/HIGH"                "HEAVY SNOW/HIGH WINDS/FREEZING"
##  [13] "HIGH WIND/HEAVY SNOW"           "RECORD SNOWFALL"               
##  [15] "HEAVY SNOW/WIND"                "WINTER STORM/HIGH WIND"        
##  [17] "WINTER STORM/HIGH WINDS"        "SNOW AND WIND"                 
##  [19] "WINTER STORM HIGH WINDS"        "WINTER STORMS"                 
##  [21] "HEAVY SNOWPACK"                 "ICE"                           
##  [23] "BLIZZARD/HEAVY SNOW"            "HEAVY SNOW/HIGH WINDS"         
##  [25] "BLOWING SNOW"                   "FREEZING DRIZZLE"              
##  [27] "LIGHT SNOW AND SLEET"           "FIRST SNOW"                    
##  [29] "FREEZING RAIN AND SLEET"        "WINTRY MIX"                    
##  [31] "WINTER WEATHER"                 "SLEET/RAIN/SNOW"               
##  [33] "RAIN/SNOW"                      "SNOW/RAIN/SLEET"               
##  [35] "DAMAGING FREEZE"                "HEAVY SNOW/HIGH WIND"          
##  [37] "FREEZING RAIN/SNOW"             "THUNDERSNOW"                   
##  [39] "HEAVY RAIN/SNOW"                "SNOW/SLEET/FREEZING RAIN"      
##  [41] "GLAZE ICE"                      "EARLY SNOW"                    
##  [43] "HEAVY SNOW/BLOWING SNOW"        "SLEET/ICE STORM"               
##  [45] "BLOWING SNOW- EXTREME WIND CHI" "SNOW AND HEAVY SNOW"           
##  [47] "SNOW/HEAVY SNOW"                "FREEZING RAIN/SLEET"           
##  [49] "ICE JAM FLOODING"               "SNOW- HIGH WIND- WIND CHILL"   
##  [51] "ICE/SNOW"                       "HEAVY SNOW/BLIZZARD"           
##  [53] "SNOW AND ICE"                   "SNOWSTORM"                     
##  [55] "SNOW AND ICE STORM"             "HEAVY SNOW/SLEET"              
##  [57] "AGRICULTURAL FREEZE"            "HEAVY SNOW/ICE STORM"          
##  [59] "HEAVY SNOW AND ICE STORM"       "SNOW/RAIN"                     
##  [61] "ICE FLOES"                      "SNOW SQUALLS"                  
##  [63] "SNOW SQUALL"                    "BLIZZARD/FREEZING RAIN"        
##  [65] "HEAVY LAKE SNOW"                "HEAVY SNOW/FREEZING RAIN"      
##  [67] "LAKE EFFECT SNOW"               "HEAVY WET SNOW"                
##  [69] "BLIZZARD AND HEAVY SNOW"        "HEAVY SNOW AND ICE"            
##  [71] "ICE STORM AND SNOW"             "HEAVY SNOW ANDBLOWING SNOW"    
##  [73] "HEAVY SNOW/ICE"                 "BLOWING SNOW & EXTREME WIND CH"
##  [75] "GLAZE/ICE STORM"                "HEAVY SNOW/WINTER STORM"       
##  [77] "BLIZZARD/WINTER STORM"          "ICE JAM"                       
##  [79] "FROST\\FREEZE"                  "HARD FREEZE"                   
##  [81] "HEAVY SNOW AND HIGH WINDS"      "HEAVY SNOW/HIGH WINDS & FLOOD" 
##  [83] "WET SNOW"                       "SNOW/ICE STORM"                
##  [85] "LIGHT SNOW"                     "RECORD SNOW"                   
##  [87] "SNOW/COLD"                      "FLASH FLOOD FROM ICE JAMS"     
##  [89] "HEAVY SNOW SQUALLS"             "HEAVY SNOW/SQUALLS"            
##  [91] "HEAVY SNOW-SQUALLS"             "ICY ROADS"                     
##  [93] "SNOW FREEZING RAIN"             "LACK OF SNOW"                  
##  [95] "SNOW/SLEET"                     "SNOW/FREEZING RAIN"            
##  [97] "SNOW DROUGHT"                   "HEAVY SNOW   FREEZING RAIN"    
##  [99] "ICE AND SNOW"                   "FREEZING RAIN AND SNOW"        
## [101] "FREEZING RAIN SLEET AND"        "HEAVY SNOW & ICE"              
## [103] "FREEZING DRIZZLE AND FREEZING"  "HAIL/ICY ROADS"                
## [105] "SNOW SHOWERS"                   "HEAVY SNOW/BLIZZARD/AVALANCHE" 
## [107] "RECORD SNOW/COLD"               "FREEZING RAIN SLEET AND LIGHT" 
## [109] "SLEET & FREEZING RAIN"          "SNOW/ BITTER COLD"             
## [111] "SNOW SLEET"                     "EARLY FREEZE"                  
## [113] "ICE/STRONG WINDS"               "SNOW/HIGH WINDS"               
## [115] "HIGH WINDS/SNOW"                "SNOWMELT FLOODING"             
## [117] "HEAVY SNOW AND STRONG WINDS"    "SNOW ACCUMULATION"             
## [119] "BLOWING SNOW/EXTREME WIND CHIL" "SNOW/ ICE"                     
## [121] "SNOW/BLOWING SNOW"              "NEAR RECORD SNOW"              
## [123] "SLEET/SNOW"                     "SNOW/SLEET/RAIN"               
## [125] "SNOW AND COLD"                  "PROLONG COLD/SNOW"             
## [127] "SNOW\\COLD"                     "WINTER MIX"                    
## [129] "SNOWFALL RECORD"                "HEAVY SNOW AND"                
## [131] "LAKE-EFFECT SNOW"               "Ice jam flood (minor"          
## [133] "Snow"                           "Freeze"                        
## [135] "Snow Squalls"                   "Light Snow/Flurries"           
## [137] "Damaging Freeze"                "Icy Roads"                     
## [139] "Wintry Mix"                     "blowing snow"                  
## [141] "Ice Fog"                        "Freezing Rain"                 
## [143] "Late-season Snowfall"           "Winter Weather"                
## [145] "Snow squalls"                   "Ice/Snow"                      
## [147] "Snow Accumulation"              "Freezing Fog"                  
## [149] "Drifting Snow"                  "Heavy snow shower"             
## [151] "LATE SNOW"                      "Record May Snow"               
## [153] "Record Winter Snow"             "Light snow"                    
## [155] "Late Season Snowfall"           "Light Snow"                    
## [157] "Black Ice"                      "Snow and Ice"                  
## [159] "Freezing Spray"                 "Light Snowfall"                
## [161] "Freezing Drizzle"               "Blowing Snow"                  
## [163] "Early snowfall"                 "Monthly Snowfall"              
## [165] "Seasonal Snowfall"              "Thundersnow shower"            
## [167] "COLD AND SNOW"                  "SLEET/FREEZING RAIN"           
## [169] "BLACK ICE"                      "Wintry mix"                    
## [171] "Frost/Freeze"                   "LATE FREEZE"                   
## [173] "Lake Effect Snow"               "Snow and sleet"                
## [175] "Freezing rain"                  "Icestorm/Blizzard"             
## [177] "Freezing drizzle"               "Mountain Snows"                
## [179] "WINTERY MIX"                    "MODERATE SNOW"                 
## [181] "MODERATE SNOWFALL"              "ICE PELLETS"                   
## [183] "SNOW AND SLEET"                 "LIGHT SNOW/FREEZING PRECIP"    
## [185] "EARLY SNOWFALL"                 "FREEZING FOG"                  
## [187] "EXCESSIVE SNOW"                 "LIGHT FREEZING RAIN"           
## [189] "MONTHLY SNOWFALL"               "ICE ROADS"                     
## [191] "LATE SEASON SNOW"               "WINTER WEATHER MIX"            
## [193] "SNOW ADVISORY"                  "UNUSUALLY LATE SNOW"           
## [195] "ACCUMULATED SNOWFALL"           "FALLING SNOW/ICE"              
## [197] "PATCHY ICE"                     "FROST/FREEZE"                  
## [199] "WINTER WEATHER/MIX"             "ICE ON ROAD"
```


Create an indicator for variations of **Rain**.


```r
regex <- "RAIN"
D <- D[, `:=`(eventRain, indicator(regex))]
```

```
##  [1] "FREEZING RAIN"                  "HEAVY RAIN"                    
##  [3] "HEAVY RAINS"                    "LIGHTNING AND HEAVY RAIN"      
##  [5] "HEAVY RAIN/LIGHTNING"           "LIGHTNING/HEAVY RAIN"          
##  [7] "HIGH WINDS HEAVY RAINS"         "HIGH WINDS/HEAVY RAIN"         
##  [9] "RECORD RAINFALL"                "FLOODING/HEAVY RAIN"           
## [11] "RAINSTORM"                      "FLOOD/RAIN/WINDS"              
## [13] "FLOOD/RAIN/WIND"                "HEAVY RAIN/FLOODING"           
## [15] "FREEZING RAIN AND SLEET"        "SLEET/RAIN/SNOW"               
## [17] "RAIN/SNOW"                      "SNOW/RAIN/SLEET"               
## [19] "FREEZING RAIN/SNOW"             "HEAVY RAIN/SNOW"               
## [21] "SNOW/SLEET/FREEZING RAIN"       "FREEZING RAIN/SLEET"           
## [23] "HEAVY RAIN/SEVERE WEATHER"      "RAIN"                          
## [25] "SNOW/RAIN"                      "BLIZZARD/FREEZING RAIN"        
## [27] "HEAVY SNOW/FREEZING RAIN"       "THUNDERSTORM WINDS/HEAVY RAIN" 
## [29] "HVY RAIN"                       "HEAVY RAIN AND FLOOD"          
## [31] "RAIN AND WIND"                  "EXCESSIVE RAIN"                
## [33] "SNOW FREEZING RAIN"             "SNOW/FREEZING RAIN"            
## [35] "TORRENTIAL RAIN"                "FLASH FLOOD - HEAVY RAIN"      
## [37] "HEAVY SNOW   FREEZING RAIN"     "FREEZING RAIN AND SNOW"        
## [39] "FREEZING RAIN SLEET AND"        "FLASH FLOOD/HEAVY RAIN"        
## [41] "HEAVY RAIN; URBAN FLOOD WINDS;" "RAIN/WIND"                     
## [43] "FREEZING RAIN SLEET AND LIGHT"  "RECORD/EXCESSIVE RAINFALL"     
## [45] "SLEET & FREEZING RAIN"          "HEAVY RAINS/FLOODING"          
## [47] "HEAVY RAIN/MUDSLIDES/FLOOD"     "EXCESSIVE RAINFALL"            
## [49] "HEAVY RAINFALL"                 "THUNDERSTORM WINDS HEAVY RAIN" 
## [51] "SNOW/SLEET/RAIN"                "FLOOD & HEAVY RAIN"            
## [53] "HEAVY RAIN/URBAN FLOOD"         "HEAVY RAIN/SMALL STREAM URBAN" 
## [55] "Heavy Rain"                     "Heavy Rain and Wind"           
## [57] "Heavy Rain/High Surf"           "Rain Damage"                   
## [59] "Torrential Rainfall"            "Freezing Rain"                 
## [61] "HEAVY RAIN/WIND"                "Heavy rain"                    
## [63] "Gusty wind/rain"                "GUSTY WIND/HVY RAIN"           
## [65] "Monthly Rainfall"               "SLEET/FREEZING RAIN"           
## [67] "TSTM HEAVY RAIN"                "RAIN (HEAVY)"                  
## [69] "Freezing rain"                  "UNSEASONAL RAIN"               
## [71] "EARLY RAIN"                     "PROLONGED RAIN"                
## [73] "MONTHLY RAINFALL"               "LIGHT FREEZING RAIN"           
## [75] "LOCALLY HEAVY RAIN"             "RECORD LOW RAINFALL"           
## [77] "HEAVY RAIN EFFECTS"
```


Calculate the proportion of records that don't satisfy any one of the defined indicators.
Calculate the number of unique event types among these records.
List the ungrouped unique event types.


```r
where <- expression(eventWind == FALSE & eventHail == FALSE & eventFlood == 
    FALSE & eventTornado == FALSE & eventLightning == FALSE & eventSnow == FALSE & 
    eventRain == FALSE)
ungrouped <- D[eval(where), list(n = .N, prop = .N/nrow(D))]
prop <- D[eval(where), .N/nrow(D)]
message(sprintf("Number (%%) of records that don't satisfy any one of the defined indicators: %.0d (%.2f%%)", 
    ungrouped$n, ungrouped$prop * 100))
```

```
## Number (%) of records that don't satisfy any one of the defined indicators: 35761 (3.96%)
```

```r
uniqueEvtype <- unique(D[eval(where), evtype])
message(sprintf("Number of unique event types that don't satisfy any one of the defined indicators: %.0d", 
    length(uniqueEvtype)))
```

```
## Number of unique event types that don't satisfy any one of the defined indicators: 383
```

```r
uniqueEvtype[order(uniqueEvtype)]
```

```
##   [1] "   HIGH SURF ADVISORY"      " WATERSPOUT"               
##   [3] "?"                          "ABNORMAL WARMTH"           
##   [5] "ABNORMALLY DRY"             "ABNORMALLY WET"            
##   [7] "APACHE COUNTY"              "ASTRONOMICAL HIGH TIDE"    
##   [9] "ASTRONOMICAL LOW TIDE"      "AVALANCE"                  
##  [11] "AVALANCHE"                  "BEACH EROSIN"              
##  [13] "Beach Erosion"              "BEACH EROSION"             
##  [15] "BELOW NORMAL PRECIPITATION" "BLIZZARD"                  
##  [17] "Blizzard Summary"           "BLIZZARD WEATHER"          
##  [19] "BLOW-OUT TIDE"              "BLOW-OUT TIDES"            
##  [21] "BLOWING DUST"               "BRUSH FIRE"                
##  [23] "BRUSH FIRES"                "COASTAL EROSION"           
##  [25] "Coastal Storm"              "COASTAL STORM"             
##  [27] "COASTAL SURGE"              "COASTALSTORM"              
##  [29] "Cold"                       "COLD"                      
##  [31] "COLD AIR FUNNEL"            "COLD AIR FUNNELS"          
##  [33] "Cold and Frost"             "COLD AND FROST"            
##  [35] "COLD AND WET CONDITIONS"    "Cold Temperature"          
##  [37] "COLD TEMPERATURES"          "COLD WAVE"                 
##  [39] "COLD WEATHER"               "COOL AND WET"              
##  [41] "COOL SPELL"                 "DAM BREAK"                 
##  [43] "DAM FAILURE"                "DENSE FOG"                 
##  [45] "DENSE SMOKE"                "DOWNBURST"                 
##  [47] "DRIEST MONTH"               "DROUGHT"                   
##  [49] "DROUGHT/EXCESSIVE HEAT"     "DROWNING"                  
##  [51] "DRY"                        "DRY CONDITIONS"            
##  [53] "DRY HOT WEATHER"            "DRY MICROBURST"            
##  [55] "DRY MICROBURST 50"          "DRY MICROBURST 53"         
##  [57] "DRY MICROBURST 58"          "DRY MICROBURST 61"         
##  [59] "DRY MICROBURST 84"          "DRY PATTERN"               
##  [61] "DRY SPELL"                  "DRY WEATHER"               
##  [63] "DRYNESS"                    "DUST DEVEL"                
##  [65] "Dust Devil"                 "DUST DEVIL"                
##  [67] "DUST DEVIL WATERSPOUT"      "DUST STORM"                
##  [69] "DUSTSTORM"                  "Early Frost"               
##  [71] "EARLY FROST"                "EXCESSIVE"                 
##  [73] "Excessive Cold"             "EXCESSIVE HEAT"            
##  [75] "EXCESSIVE HEAT/DROUGHT"     "EXCESSIVE PRECIPITATION"   
##  [77] "EXCESSIVE WETNESS"          "EXCESSIVELY DRY"           
##  [79] "Extended Cold"              "Extreme Cold"              
##  [81] "EXTREME COLD"               "EXTREME HEAT"              
##  [83] "EXTREME/RECORD COLD"        "EXTREMELY WET"             
##  [85] "FIRST FROST"                "FLASH FLOOODING"           
##  [87] "FOG"                        "FOG AND COLD TEMPERATURES" 
##  [89] "FOREST FIRES"               "Frost"                     
##  [91] "FROST"                      "FUNNEL"                    
##  [93] "Funnel Cloud"               "FUNNEL CLOUD"              
##  [95] "FUNNEL CLOUD."              "FUNNEL CLOUDS"             
##  [97] "FUNNELS"                    "Glaze"                     
##  [99] "GLAZE"                      "GRASS FIRES"               
## [101] "GROUND BLIZZARD"            "HAZARDOUS SURF"            
## [103] "HEAT"                       "HEAT DROUGHT"              
## [105] "Heat Wave"                  "HEAT WAVE"                 
## [107] "HEAT WAVE DROUGHT"          "HEAT WAVES"                
## [109] "HEAT/DROUGHT"               "Heatburst"                 
## [111] "HEAVY MIX"                  "HEAVY PRECIPATATION"       
## [113] "Heavy Precipitation"        "HEAVY PRECIPITATION"       
## [115] "HEAVY SEAS"                 "HEAVY SHOWER"              
## [117] "HEAVY SHOWERS"              "Heavy Surf"                
## [119] "HEAVY SURF"                 "HEAVY SURF/HIGH SURF"      
## [121] "HEAVY SWELLS"               "HIGH"                      
## [123] "HIGH  SWELLS"               "HIGH SEAS"                 
## [125] "High Surf"                  "HIGH SURF"                 
## [127] "HIGH SURF ADVISORIES"       "HIGH SURF ADVISORY"        
## [129] "HIGH SWELLS"                "HIGH TEMPERATURE RECORD"   
## [131] "HIGH TIDES"                 "HIGH WATER"                
## [133] "HIGH WAVES"                 "Hot and Dry"               
## [135] "HOT PATTERN"                "HOT SPELL"                 
## [137] "HOT WEATHER"                "HOT/DRY PATTERN"           
## [139] "HURRICANE"                  "HURRICANE-GENERATED SWELLS"
## [141] "Hurricane Edouard"          "HURRICANE EMILY"           
## [143] "HURRICANE ERIN"             "HURRICANE FELIX"           
## [145] "HURRICANE GORDON"           "HURRICANE OPAL"            
## [147] "HURRICANE/TYPHOON"          "HYPERTHERMIA/EXPOSURE"     
## [149] "HYPOTHERMIA"                "Hypothermia/Exposure"      
## [151] "HYPOTHERMIA/EXPOSURE"       "LANDSLIDE"                 
## [153] "LANDSLIDES"                 "Landslump"                 
## [155] "LANDSLUMP"                  "LANDSPOUT"                 
## [157] "LARGE WALL CLOUD"           "LOW TEMPERATURE"           
## [159] "LOW TEMPERATURE RECORD"     "Marine Accident"           
## [161] "MARINE MISHAP"              "Metro Storm, May 26"       
## [163] "Microburst"                 "MICROBURST"                
## [165] "Mild and Dry Pattern"       "MILD PATTERN"              
## [167] "MILD/DRY PATTERN"           "MIXED PRECIP"              
## [169] "Mixed Precipitation"        "MIXED PRECIPITATION"       
## [171] "MONTHLY PRECIPITATION"      "MONTHLY TEMPERATURE"       
## [173] "MUD SLIDE"                  "MUD SLIDES"                
## [175] "MUD/ROCK SLIDE"             "Mudslide"                  
## [177] "MUDSLIDE"                   "MUDSLIDE/LANDSLIDE"        
## [179] "Mudslides"                  "MUDSLIDES"                 
## [181] "No Severe Weather"          "NONE"                      
## [183] "NORMAL PRECIPITATION"       "NORTHERN LIGHTS"           
## [185] "Other"                      "OTHER"                     
## [187] "PATCHY DENSE FOG"           "Prolong Cold"              
## [189] "PROLONG COLD"               "PROLONG WARMTH"            
## [191] "RAPIDLY RISING WATER"       "RECORD  COLD"              
## [193] "Record Cold"                "RECORD COLD"               
## [195] "RECORD COLD/FROST"          "RECORD COOL"               
## [197] "Record dry month"           "RECORD DRYNESS"            
## [199] "Record Heat"                "RECORD HEAT"               
## [201] "RECORD HEAT WAVE"           "Record High"               
## [203] "RECORD HIGH"                "RECORD HIGH TEMPERATURE"   
## [205] "RECORD HIGH TEMPERATURES"   "RECORD LOW"                
## [207] "RECORD PRECIPITATION"       "Record temperature"        
## [209] "RECORD TEMPERATURE"         "Record Temperatures"       
## [211] "RECORD TEMPERATURES"        "RECORD WARM"               
## [213] "RECORD WARM TEMPS."         "Record Warmth"             
## [215] "RECORD WARMTH"              "RECORD/EXCESSIVE HEAT"     
## [217] "RED FLAG CRITERIA"          "RED FLAG FIRE WX"          
## [219] "REMNANTS OF FLOYD"          "RIP CURRENT"               
## [221] "RIP CURRENTS"               "RIP CURRENTS HEAVY SURF"   
## [223] "RIP CURRENTS/HEAVY SURF"    "ROCK SLIDE"                
## [225] "ROGUE WAVE"                 "ROTATING WALL CLOUD"       
## [227] "ROUGH SEAS"                 "ROUGH SURF"                
## [229] "Saharan Dust"               "SAHARAN DUST"              
## [231] "SEICHE"                     "SEVERE COLD"               
## [233] "SEVERE THUNDERSTORM"        "SEVERE THUNDERSTORMS"      
## [235] "SEVERE TURBULENCE"          "SLEET"                     
## [237] "SLEET STORM"                "SMALL STREAM"              
## [239] "SMALL STREAM AND"           "Sml Stream Fld"            
## [241] "SMOKE"                      "SOUTHEAST"                 
## [243] "STORM SURGE"                "STORM SURGE/TIDE"          
## [245] "Summary August 10"          "Summary August 11"         
## [247] "Summary August 17"          "Summary August 2-3"        
## [249] "Summary August 21"          "Summary August 28"         
## [251] "Summary August 4"           "Summary August 7"          
## [253] "Summary August 9"           "Summary Jan 17"            
## [255] "Summary July 23-24"         "Summary June 18-19"        
## [257] "Summary June 5-6"           "Summary June 6"            
## [259] "Summary of April 12"        "Summary of April 13"       
## [261] "Summary of April 21"        "Summary of April 27"       
## [263] "Summary of April 3rd"       "Summary of August 1"       
## [265] "Summary of July 11"         "Summary of July 2"         
## [267] "Summary of July 22"         "Summary of July 26"        
## [269] "Summary of July 29"         "Summary of July 3"         
## [271] "Summary of June 10"         "Summary of June 11"        
## [273] "Summary of June 12"         "Summary of June 13"        
## [275] "Summary of June 15"         "Summary of June 16"        
## [277] "Summary of June 18"         "Summary of June 23"        
## [279] "Summary of June 24"         "Summary of June 3"         
## [281] "Summary of June 30"         "Summary of June 4"         
## [283] "Summary of June 6"          "Summary of March 14"       
## [285] "Summary of March 23"        "Summary of March 24"       
## [287] "SUMMARY OF MARCH 24-25"     "SUMMARY OF MARCH 27"       
## [289] "SUMMARY OF MARCH 29"        "Summary of May 10"         
## [291] "Summary of May 13"          "Summary of May 14"         
## [293] "Summary of May 22"          "Summary of May 22 am"      
## [295] "Summary of May 22 pm"       "Summary of May 26 am"      
## [297] "Summary of May 26 pm"       "Summary of May 31 am"      
## [299] "Summary of May 31 pm"       "Summary of May 9-10"       
## [301] "Summary Sept. 25-26"        "Summary September 20"      
## [303] "Summary September 23"       "Summary September 3"       
## [305] "Summary September 4"        "Summary: Nov. 16"          
## [307] "Summary: Nov. 6-7"          "Summary: Oct. 20-21"       
## [309] "Summary: October 31"        "Summary: Sept. 18"         
## [311] "Temperature record"         "THUNDERSTORM"              
## [313] "THUNDERSTORM DAMAGE"        "THUNDERSTORM DAMAGE TO"    
## [315] "THUNDERSTORM W INDS"        "THUNDERSTORM WINS"         
## [317] "THUNDERSTORMS"              "THUNDERSTORMW"             
## [319] "THUNDERSTORMW 50"           "TROPICAL DEPRESSION"       
## [321] "TROPICAL STORM"             "TROPICAL STORM ALBERTO"    
## [323] "TROPICAL STORM DEAN"        "TROPICAL STORM GORDON"     
## [325] "TROPICAL STORM JERRY"       "TSTM"                      
## [327] "TSTMW"                      "TSUNAMI"                   
## [329] "TYPHOON"                    "Unseasonable Cold"         
## [331] "UNSEASONABLY COLD"          "UNSEASONABLY COOL"         
## [333] "UNSEASONABLY COOL & WET"    "UNSEASONABLY DRY"          
## [335] "UNSEASONABLY HOT"           "UNSEASONABLY WARM"         
## [337] "UNSEASONABLY WARM & WET"    "UNSEASONABLY WARM AND DRY" 
## [339] "UNSEASONABLY WARM YEAR"     "UNSEASONABLY WARM/WET"     
## [341] "UNSEASONABLY WET"           "UNSEASONAL LOW TEMP"       
## [343] "UNUSUAL WARMTH"             "UNUSUAL/RECORD WARMTH"     
## [345] "UNUSUALLY COLD"             "UNUSUALLY WARM"            
## [347] "URBAN AND SMALL"            "URBAN AND SMALL STREAM"    
## [349] "URBAN SMALL"                "URBAN/SMALL"               
## [351] "URBAN/SMALL STREAM"         "URBAN/SMALL STRM FLDG"     
## [353] "URBAN/SML STREAM FLD"       "URBAN/SML STREAM FLDG"     
## [355] "VERY DRY"                   "VERY WARM"                 
## [357] "VOG"                        "Volcanic Ash"              
## [359] "VOLCANIC ASH"               "Volcanic Ash Plume"        
## [361] "VOLCANIC ASHFALL"           "VOLCANIC ERUPTION"         
## [363] "WALL CLOUD"                 "WALL CLOUD/FUNNEL CLOUD"   
## [365] "WARM DRY CONDITIONS"        "WARM WEATHER"              
## [367] "WATER SPOUT"                "WATERSPOUT"                
## [369] "WATERSPOUT-"                "WATERSPOUT FUNNEL CLOUD"   
## [371] "WATERSPOUT/"                "WATERSPOUTS"               
## [373] "WAYTERSPOUT"                "wet micoburst"             
## [375] "WET MICROBURST"             "Wet Month"                 
## [377] "WET WEATHER"                "Wet Year"                  
## [379] "WILD FIRES"                 "WILD/FOREST FIRE"          
## [381] "WILD/FOREST FIRES"          "WILDFIRE"                  
## [383] "WILDFIRES"
```


Create an **Other** indicator for ungrouped event types.


```r
D <- D[, `:=`(eventOther, eventWind == FALSE & eventHail == FALSE & eventFlood == 
    FALSE & eventTornado == FALSE & eventLightning == FALSE & eventSnow == FALSE & 
    eventRain == FALSE)]
```



```r
groupby <- expression(list(eventWind, eventHail, eventFlood, eventTornado, eventLightning, 
    eventSnow, eventRain, eventOther))
D[, .N, eval(groupby)][order(eventWind, eventHail, eventFlood, eventTornado, 
    eventLightning, eventSnow, eventRain, eventOther, decreasing = TRUE)]
```

```
##     eventWind eventHail eventFlood eventTornado eventLightning eventSnow
##  1:      TRUE      TRUE      FALSE         TRUE          FALSE     FALSE
##  2:      TRUE      TRUE      FALSE        FALSE          FALSE     FALSE
##  3:      TRUE     FALSE       TRUE        FALSE          FALSE      TRUE
##  4:      TRUE     FALSE       TRUE        FALSE          FALSE     FALSE
##  5:      TRUE     FALSE       TRUE        FALSE          FALSE     FALSE
##  6:      TRUE     FALSE      FALSE        FALSE           TRUE     FALSE
##  7:      TRUE     FALSE      FALSE        FALSE          FALSE      TRUE
##  8:      TRUE     FALSE      FALSE        FALSE          FALSE     FALSE
##  9:      TRUE     FALSE      FALSE        FALSE          FALSE     FALSE
## 10:     FALSE      TRUE       TRUE        FALSE          FALSE     FALSE
## 11:     FALSE      TRUE      FALSE        FALSE          FALSE      TRUE
## 12:     FALSE      TRUE      FALSE        FALSE          FALSE     FALSE
## 13:     FALSE     FALSE       TRUE        FALSE          FALSE      TRUE
## 14:     FALSE     FALSE       TRUE        FALSE          FALSE     FALSE
## 15:     FALSE     FALSE       TRUE        FALSE          FALSE     FALSE
## 16:     FALSE     FALSE      FALSE         TRUE          FALSE     FALSE
## 17:     FALSE     FALSE      FALSE        FALSE           TRUE     FALSE
## 18:     FALSE     FALSE      FALSE        FALSE           TRUE     FALSE
## 19:     FALSE     FALSE      FALSE        FALSE          FALSE      TRUE
## 20:     FALSE     FALSE      FALSE        FALSE          FALSE      TRUE
## 21:     FALSE     FALSE      FALSE        FALSE          FALSE     FALSE
## 22:     FALSE     FALSE      FALSE        FALSE          FALSE     FALSE
##     eventWind eventHail eventFlood eventTornado eventLightning eventSnow
##     eventRain eventOther      N
##  1:     FALSE      FALSE      1
##  2:     FALSE      FALSE   1123
##  3:     FALSE      FALSE      1
##  4:      TRUE      FALSE      8
##  5:     FALSE      FALSE      9
##  6:     FALSE      FALSE     12
##  7:     FALSE      FALSE     27
##  8:      TRUE      FALSE     16
##  9:     FALSE      FALSE 363706
## 10:     FALSE      FALSE      1
## 11:     FALSE      FALSE      1
## 12:     FALSE      FALSE 289275
## 13:     FALSE      FALSE     17
## 14:      TRUE      FALSE     20
## 15:     FALSE      FALSE  82675
## 16:     FALSE      FALSE  60707
## 17:      TRUE      FALSE      3
## 18:     FALSE      FALSE  15765
## 19:      TRUE      FALSE    345
## 20:     FALSE      FALSE  40975
## 21:      TRUE      FALSE  11849
## 22:     FALSE       TRUE  35761
##     eventRain eventOther      N
```




## Results

> The analysis document must have **at least one figure containing as plot**.
> 
> Your analyis must have **no more than three figures**. Figures may have multiple
> plots in them (i.e. panel plots), but there cannot be more than three figures
> total.
> 

