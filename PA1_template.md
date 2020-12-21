---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
    theme: yeti
    highlight: zenburn
---
  


## Loading and preprocessing the data

#### 1) Install / load necessary libraries  


```r
# install packages if needed, otherwise load in libraries
if(!require(pacman)) install.packages('pacman')
pacman::p_load(tidyverse,DT, knitr, lattice)

# set default theme for ggplot graphs
theme_set(theme_minimal())
```

#### 2) Unzip and read data file

```r
# unzip and read activity data as data frame
df <- read.csv(unz("activity.zip", "activity.csv"), colClasses = c("integer","Date","integer"),
               stringsAsFactors = F)
```


## What is mean total number of steps taken per day?  
  
Note: summary statistics and plots below ignore all observations with `NAs`.  
  
#### 1) Calculate total number of steps per day.  
  
Below is a summary of steps per day in table form:  


```r
# calculate total steps per day; filter out missing observations
steps_per_day <- df %>% filter(!is.na(steps)) %>% 
  group_by(date) %>%
  summarise(steps = sum(steps)) 

# table of total steps per day
datatable(steps_per_day, options = list(pageLength = 10,
                                        columnDefs= list(list(className = 'dt-right',
                                                              targets = "_all"))))
```

<!--html_preserve--><div id="htmlwidget-a7116967f1cba527d78b" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-a7116967f1cba527d78b">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53"],["2012-10-02","2012-10-03","2012-10-04","2012-10-05","2012-10-06","2012-10-07","2012-10-09","2012-10-10","2012-10-11","2012-10-12","2012-10-13","2012-10-14","2012-10-15","2012-10-16","2012-10-17","2012-10-18","2012-10-19","2012-10-20","2012-10-21","2012-10-22","2012-10-23","2012-10-24","2012-10-25","2012-10-26","2012-10-27","2012-10-28","2012-10-29","2012-10-30","2012-10-31","2012-11-02","2012-11-03","2012-11-05","2012-11-06","2012-11-07","2012-11-08","2012-11-11","2012-11-12","2012-11-13","2012-11-15","2012-11-16","2012-11-17","2012-11-18","2012-11-19","2012-11-20","2012-11-21","2012-11-22","2012-11-23","2012-11-24","2012-11-25","2012-11-26","2012-11-27","2012-11-28","2012-11-29"],[126,11352,12116,13294,15420,11015,12811,9900,10304,17382,12426,15098,10139,15084,13452,10056,11829,10395,8821,13460,8918,8355,2492,6778,10119,11458,5018,9819,15414,10600,10571,10439,8334,12883,3219,12608,10765,7336,41,5441,14339,15110,8841,4472,12787,20427,21194,14478,11834,11162,13646,10183,7047]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>date<\/th>\n      <th>steps<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"pageLength":10,"columnDefs":[{"className":"dt-right","targets":"_all"},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
  
Here is a line plot representation of steps per day over time:


```r
# plot steps per day
ggplot(steps_per_day, aes(date,steps)) + geom_line() + 
  labs(title = "Steps per Day")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-4-1.png" style="display: block; margin: auto;" />
  
#### 2) Create histogram of steps per day  
  

```r
# plot distribution of steps/day
ggplot(steps_per_day, aes(steps)) + geom_histogram(bins=20) + 
  labs(title = "Steps per Day")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-5-1.png" style="display: block; margin: auto;" />
  
#### 3. Calculate mean and median steps per day  
  

```r
kable(
  steps_per_day %>%
    summarise(
      meanStepsDay = mean(steps),
      medianStepsDay = median(steps)
      ),
  format.args = list(big.mark = ","),
  digits = 0,
  col.names = c("Mean Steps per Day", "Median Steps per Day")
  )
```



| Mean Steps per Day| Median Steps per Day|
|------------------:|--------------------:|
|             10,766|               10,765|
## What is the average daily activity pattern?
  
#### 1. Make a time series plot of 5-minute interval and avg. steps taken

Let's first look at the steps by interval in table form.  

```r
# calculate mean steps per time interval
steps_per_interval <- df %>%
  filter(!is.na(steps)) %>% 
  group_by(interval) %>% 
  summarise(steps = mean(steps))

# table of total steps per time interval
datatable(steps_per_interval, options = list(pageLength = 10,
                         columnDefs= list(list(className = 'dt-right',targets = "_all")))) %>% 
  formatRound(columns = "steps",digits=2)
```

<!--html_preserve--><div id="htmlwidget-8fbda1028614e8e9f838" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-8fbda1028614e8e9f838">{"x":{"filter":"none","data":[["1","2","3","4","5","6","7","8","9","10","11","12","13","14","15","16","17","18","19","20","21","22","23","24","25","26","27","28","29","30","31","32","33","34","35","36","37","38","39","40","41","42","43","44","45","46","47","48","49","50","51","52","53","54","55","56","57","58","59","60","61","62","63","64","65","66","67","68","69","70","71","72","73","74","75","76","77","78","79","80","81","82","83","84","85","86","87","88","89","90","91","92","93","94","95","96","97","98","99","100","101","102","103","104","105","106","107","108","109","110","111","112","113","114","115","116","117","118","119","120","121","122","123","124","125","126","127","128","129","130","131","132","133","134","135","136","137","138","139","140","141","142","143","144","145","146","147","148","149","150","151","152","153","154","155","156","157","158","159","160","161","162","163","164","165","166","167","168","169","170","171","172","173","174","175","176","177","178","179","180","181","182","183","184","185","186","187","188","189","190","191","192","193","194","195","196","197","198","199","200","201","202","203","204","205","206","207","208","209","210","211","212","213","214","215","216","217","218","219","220","221","222","223","224","225","226","227","228","229","230","231","232","233","234","235","236","237","238","239","240","241","242","243","244","245","246","247","248","249","250","251","252","253","254","255","256","257","258","259","260","261","262","263","264","265","266","267","268","269","270","271","272","273","274","275","276","277","278","279","280","281","282","283","284","285","286","287","288"],[0,5,10,15,20,25,30,35,40,45,50,55,100,105,110,115,120,125,130,135,140,145,150,155,200,205,210,215,220,225,230,235,240,245,250,255,300,305,310,315,320,325,330,335,340,345,350,355,400,405,410,415,420,425,430,435,440,445,450,455,500,505,510,515,520,525,530,535,540,545,550,555,600,605,610,615,620,625,630,635,640,645,650,655,700,705,710,715,720,725,730,735,740,745,750,755,800,805,810,815,820,825,830,835,840,845,850,855,900,905,910,915,920,925,930,935,940,945,950,955,1000,1005,1010,1015,1020,1025,1030,1035,1040,1045,1050,1055,1100,1105,1110,1115,1120,1125,1130,1135,1140,1145,1150,1155,1200,1205,1210,1215,1220,1225,1230,1235,1240,1245,1250,1255,1300,1305,1310,1315,1320,1325,1330,1335,1340,1345,1350,1355,1400,1405,1410,1415,1420,1425,1430,1435,1440,1445,1450,1455,1500,1505,1510,1515,1520,1525,1530,1535,1540,1545,1550,1555,1600,1605,1610,1615,1620,1625,1630,1635,1640,1645,1650,1655,1700,1705,1710,1715,1720,1725,1730,1735,1740,1745,1750,1755,1800,1805,1810,1815,1820,1825,1830,1835,1840,1845,1850,1855,1900,1905,1910,1915,1920,1925,1930,1935,1940,1945,1950,1955,2000,2005,2010,2015,2020,2025,2030,2035,2040,2045,2050,2055,2100,2105,2110,2115,2120,2125,2130,2135,2140,2145,2150,2155,2200,2205,2210,2215,2220,2225,2230,2235,2240,2245,2250,2255,2300,2305,2310,2315,2320,2325,2330,2335,2340,2345,2350,2355],[1.71698113207547,0.339622641509434,0.132075471698113,0.150943396226415,0.0754716981132075,2.09433962264151,0.528301886792453,0.867924528301887,0,1.47169811320755,0.30188679245283,0.132075471698113,0.320754716981132,0.679245283018868,0.150943396226415,0.339622641509434,0,1.11320754716981,1.83018867924528,0.169811320754717,0.169811320754717,0.377358490566038,0.264150943396226,0,0,0,1.13207547169811,0,0,0.132075471698113,0,0.226415094339623,0,0,1.54716981132075,0.943396226415094,0,0,0,0,0.207547169811321,0.622641509433962,1.62264150943396,0.584905660377358,0.490566037735849,0.0754716981132075,0,0,1.18867924528302,0.943396226415094,2.56603773584906,0,0.339622641509434,0.358490566037736,4.11320754716981,0.660377358490566,3.49056603773585,0.830188679245283,3.11320754716981,1.11320754716981,0,1.56603773584906,3,2.24528301886792,3.32075471698113,2.9622641509434,2.09433962264151,6.05660377358491,16.0188679245283,18.3396226415094,39.4528301886792,44.4905660377358,31.4905660377358,49.2641509433962,53.7735849056604,63.4528301886792,49.9622641509434,47.0754716981132,52.1509433962264,39.3396226415094,44.0188679245283,44.1698113207547,37.3584905660377,49.0377358490566,43.811320754717,44.377358490566,50.5094339622642,54.5094339622642,49.9245283018868,50.9811320754717,55.6792452830189,44.3207547169811,52.2641509433962,69.5471698113208,57.8490566037736,56.1509433962264,73.377358490566,68.2075471698113,129.433962264151,157.528301886792,171.150943396226,155.396226415094,177.301886792453,206.169811320755,195.924528301887,179.566037735849,183.396226415094,167.018867924528,143.452830188679,124.037735849057,109.11320754717,108.11320754717,103.716981132075,95.9622641509434,66.2075471698113,45.2264150943396,24.7924528301887,38.7547169811321,34.9811320754717,21.0566037735849,40.5660377358491,26.9811320754717,42.4150943396226,52.6603773584906,38.9245283018868,50.7924528301887,44.2830188679245,37.4150943396226,34.6981132075472,28.3396226415094,25.0943396226415,31.9433962264151,31.3584905660377,29.6792452830189,21.3207547169811,25.5471698113208,28.377358490566,26.4716981132075,33.4339622641509,49.9811320754717,42.0377358490566,44.6037735849057,46.0377358490566,59.188679245283,63.8679245283019,87.6981132075472,94.8490566037736,92.7735849056604,63.3962264150943,50.1698113207547,54.4716981132075,32.4150943396226,26.5283018867925,37.7358490566038,45.0566037735849,67.2830188679245,42.3396226415094,39.8867924528302,43.2641509433962,40.9811320754717,46.2452830188679,56.4339622641509,42.7547169811321,25.1320754716981,39.9622641509434,53.5471698113208,47.3207547169811,60.811320754717,55.7547169811321,51.9622641509434,43.5849056603774,48.6981132075472,35.4716981132075,37.5471698113208,41.8490566037736,27.5094339622642,17.1132075471698,26.0754716981132,43.622641509434,43.7735849056604,30.0188679245283,36.0754716981132,35.4905660377358,38.8490566037736,45.9622641509434,47.7547169811321,48.1320754716981,65.3207547169811,82.9056603773585,98.6603773584906,102.11320754717,83.9622641509434,62.1320754716981,64.1320754716981,74.5471698113208,63.1698113207547,56.9056603773585,59.7735849056604,43.8679245283019,38.5660377358491,44.6603773584906,45.4528301886792,46.2075471698113,43.6792452830189,46.622641509434,56.3018867924528,50.7169811320755,61.2264150943396,72.7169811320755,78.9433962264151,68.9433962264151,59.6603773584906,75.0943396226415,56.5094339622642,34.7735849056604,37.4528301886792,40.6792452830189,58.0188679245283,74.6981132075472,85.3207547169811,59.2641509433962,67.7735849056604,77.6981132075472,74.2452830188679,85.3396226415094,99.4528301886792,86.5849056603774,85.6037735849057,84.8679245283019,77.8301886792453,58.0377358490566,53.3584905660377,36.3207547169811,20.7169811320755,27.3962264150943,40.0188679245283,30.2075471698113,25.5471698113208,45.6603773584906,33.5283018867925,19.622641509434,19.0188679245283,19.3396226415094,33.3396226415094,26.811320754717,21.1698113207547,27.3018867924528,21.3396226415094,19.5471698113208,21.3207547169811,32.3018867924528,20.1509433962264,15.9433962264151,17.2264150943396,23.4528301886792,19.2452830188679,12.4528301886792,8.0188679245283,14.6603773584906,16.3018867924528,8.67924528301887,7.79245283018868,8.13207547169811,2.62264150943396,1.45283018867925,3.67924528301887,4.81132075471698,8.50943396226415,7.07547169811321,8.69811320754717,9.75471698113208,2.20754716981132,0.320754716981132,0.113207547169811,1.60377358490566,4.60377358490566,3.30188679245283,2.84905660377358,0,0.830188679245283,0.962264150943396,1.58490566037736,2.60377358490566,4.69811320754717,3.30188679245283,0.641509433962264,0.226415094339623,1.07547169811321]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>interval<\/th>\n      <th>steps<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"pageLength":10,"columnDefs":[{"targets":2,"render":"function(data, type, row, meta) {\n    return type !== 'display' ? data : DTWidget.formatRound(data, 2, 3, \",\", \".\");\n  }"},{"className":"dt-right","targets":"_all"},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":["options.columnDefs.0.render"],"jsHooks":[]}</script><!--/html_preserve-->

Below is a line plot of steps per interval


```r
# find interval with maximum steps
max_int <- steps_per_interval$interval[steps_per_interval$steps == max(steps_per_interval$steps)]

# line plot of average steps per time interval
ggplot(steps_per_interval, aes(interval,steps)) + geom_line() + 
  labs(title="Mean Steps per Time Interval") +
  geom_vline(xintercept=max_int,color='red', linetype = "dashed") 
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

#### 2. Display interval with maximum steps  

```r
paste("Time interval with maximum average steps: ", max_int)
```

```
[1] "Time interval with maximum average steps:  835"
```


## Imputing missing values

#### 1. Calculate total number of rows with NAs  
  

```r
miss_rows <- sum(is.na(df$steps))
paste("Number of missing rows:",miss_rows)
```

```
[1] "Number of missing rows: 2304"
```
  
#### 2. Develop method for encoding missing values  
  
Let's use impute missing values by using the mean step value for the given time interval.  The mean values are already available in the data frame, `steps_per_interval`. Below are the first five records from this summary-level dataset.  


```r
# print first few records of steps_per_interval
kable(head(steps_per_interval), digits=1)
```



| interval| steps|
|--------:|-----:|
|        0|   1.7|
|        5|   0.3|
|       10|   0.1|
|       15|   0.2|
|       20|   0.1|
|       25|   2.1|
  
#### 3. Create new dataset with imputed values where missing  
  
Below we create a new data frame using mean steps per interval to impute missing values.  
  

```r
# create modified data frame with imputed missing step values
df_mod <- df %>%
  left_join(steps_per_interval, by="interval", suffix=c("","_imputed")) %>% 
  mutate(steps = ifelse(is.na(steps),steps_imputed,steps)) %>% 
  select(steps, date,interval)
```
#### 4. Create histogram and calculate mean and median steps using modified data frame


```r
# calculate total steps per day on modified df
steps_per_day_mod <- df_mod %>% 
  group_by(date) %>%
  summarise(steps = sum(steps)) 

# create histogram  of steps/day
ggplot(steps_per_day_mod, aes(steps)) + geom_histogram(bins=20) + 
  labs(title = "Steps per Day on Modified Data Frame")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-13-1.png" style="display: block; margin: auto;" />

```r
# mean and median steps per data on imputed, modified data frame
kable(
  steps_per_day_mod %>%
    summarise(
      meanStepsDay = mean(steps),
      medianStepsDay = median(steps)
      ),
  format.args = list(big.mark = ","),
  digits = 0,
  col.names = c("Mean Steps per Day", "Median Steps per Day")
  )
```



| Mean Steps per Day| Median Steps per Day|
|------------------:|--------------------:|
|             10,766|               10,766|

  

```r
# compare mean of modified df against original with missing values
diff_mean <- mean(steps_per_day_mod$steps) - mean(steps_per_day$steps)
paste0("The mean steps per day using imputed values changed by ",diff_mean, " compared to the original data.")
```

```
[1] "The mean steps per day using imputed values changed by 0 compared to the original data."
```

```r
# compare median of modified df against original with missing values
diff_median <- median(steps_per_day_mod$steps) - median(steps_per_day$steps)
paste0("The median steps per day using imputed values changed by ",round(diff_median,2), " compared to the original data.")
```

```
[1] "The median steps per day using imputed values changed by 1.19 compared to the original data."
```

## Are there differences in activity patterns between weekdays and weekends?  
  
#### 1. Create a new factor variable to determine if date is weekday or weekend.  

```r
df <- df %>% 
  mutate(day_type = factor(ifelse(weekdays(date) %in% c("Saturday","Sunday"), "weekend","weekday")))
```

#### 2. Make a panel plot average steps per interval by weekend/weekday


```r
# data frame to use for panel plot
steps_day_type <- df %>% filter(!is.na(steps)) %>% 
  group_by(day_type,interval) %>% 
  summarise(steps=mean(steps))

# panel plot
ggplot(steps_day_type, aes(interval,steps)) + geom_line() +
  facet_grid(day_type ~.) + labs(title = "Mean Steps by Time Interval and Day Type")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-17-1.png" style="display: block; margin: auto;" />
  
The weekday line plot indicates prominent step activity in the morning (i.e. before 10am).  On the other hand, the weekend plot reveals consistently higher activity in the late morning through early evening.  


