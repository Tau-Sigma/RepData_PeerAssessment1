# Reproducible Research: Peer Assessment 1
This document includes the code, figures and descriptive text required for Assignment 1 of the Reproducible Research course.

It is structured via the five respective assignment headings.

###Loading and preprocessing the data
The first task is to show any code required to

1. Load the data (i.e. read.csv())

2. Process/transform the data (if necessary) into a format suitable for your analysis

The data was manually unzipped from the zip file, which was replicated from github repository. The csv file was stored in the same directory. To switch to that directory and to load the data from there into R the following code is used:


```r
setwd("/home/sascha/Dokumente/Coursera/Reproducible research/Assignment 1/Solution")
activity_raw<-read.csv("activity.csv",header=TRUE,na.strings = "NA")
activity_raw$date<-as.Date(activity_raw$date, "%Y-%m-%d")
```

For now, no additional transformation is necessary.

###What is mean total number of steps taken per day?

For this part of the assignment, missing values in the dataset can be ignored. The tasks are as follows:

1. Calculate the total number of steps taken per day

2. Make a histogram of the total number of steps taken each day

3. Calculate and report the mean and median of the total number of steps taken per day

Via the following code the total number of steps taken per day is calculated and shown.


```r
library(plyr)
activity_by_day<-ddply(activity_raw, .(date), summarise, Steps = sum(steps)); #group by date and sum up steps
activity_by_day
```

```
##          date Steps
## 1  2012-10-01    NA
## 2  2012-10-02   126
## 3  2012-10-03 11352
## 4  2012-10-04 12116
## 5  2012-10-05 13294
## 6  2012-10-06 15420
## 7  2012-10-07 11015
## 8  2012-10-08    NA
## 9  2012-10-09 12811
## 10 2012-10-10  9900
## 11 2012-10-11 10304
## 12 2012-10-12 17382
## 13 2012-10-13 12426
## 14 2012-10-14 15098
## 15 2012-10-15 10139
## 16 2012-10-16 15084
## 17 2012-10-17 13452
## 18 2012-10-18 10056
## 19 2012-10-19 11829
## 20 2012-10-20 10395
## 21 2012-10-21  8821
## 22 2012-10-22 13460
## 23 2012-10-23  8918
## 24 2012-10-24  8355
## 25 2012-10-25  2492
## 26 2012-10-26  6778
## 27 2012-10-27 10119
## 28 2012-10-28 11458
## 29 2012-10-29  5018
## 30 2012-10-30  9819
## 31 2012-10-31 15414
## 32 2012-11-01    NA
## 33 2012-11-02 10600
## 34 2012-11-03 10571
## 35 2012-11-04    NA
## 36 2012-11-05 10439
## 37 2012-11-06  8334
## 38 2012-11-07 12883
## 39 2012-11-08  3219
## 40 2012-11-09    NA
## 41 2012-11-10    NA
## 42 2012-11-11 12608
## 43 2012-11-12 10765
## 44 2012-11-13  7336
## 45 2012-11-14    NA
## 46 2012-11-15    41
## 47 2012-11-16  5441
## 48 2012-11-17 14339
## 49 2012-11-18 15110
## 50 2012-11-19  8841
## 51 2012-11-20  4472
## 52 2012-11-21 12787
## 53 2012-11-22 20427
## 54 2012-11-23 21194
## 55 2012-11-24 14478
## 56 2012-11-25 11834
## 57 2012-11-26 11162
## 58 2012-11-27 13646
## 59 2012-11-28 10183
## 60 2012-11-29  7047
## 61 2012-11-30    NA
```

The next task is to create a histogram showing the total number of steps taken per day. This is done via the following code:


```r
par(mar=c(5,4,2,2))
hist(activity_by_day$Steps,breaks=61,xlab="Steps per day",ylab="Frequency",main="Histogram: Steps per day")
```

![](PA1_template_files/figure-html/histogram_steps_pd-1.png) 

The third and final task of this section is to calculate the mean and the median of the total steps taken per day. This can be done via the following code:


```r
mean_ts<-mean(activity_by_day$Steps,na.rm=TRUE)
median_ts<-mean(activity_by_day$Steps,na.rm=TRUE)
mean_ts
```

```
## [1] 10766.19
```

```r
median_ts
```

```
## [1] 10766.19
```

With the above code the calculated mean of total steps taken per day is 10766 and the median is 10766.

###What is the average daily activity pattern?

This question is to be answered by

1. making a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

2. answering, which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

The time series plot is created as follows

```r
av_steps_interval<-ddply(activity_raw, .(interval), summarise, steps_avg = mean(steps,na.rm=TRUE)); #group by interval and calculate mean for all days
plot(av_steps_interval$interval, av_steps_interval$steps_avg, col="red", main ="Average no. of steps by interval", ylab="No. of steps (average)", xlab="Interval",type="l")
```

![](PA1_template_files/figure-html/time_series-1.png) 

The data used to create this plot can then be used to find the interval with the maximum number of steps (on average).


```r
library(dplyr);
```

```
## 
## Attaching package: 'dplyr'
## 
## Die folgenden Objekte sind maskiert von 'package:plyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
## 
## Die folgenden Objekte sind maskiert von 'package:stats':
## 
##     filter, lag
## 
## Die folgenden Objekte sind maskiert von 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
interval_max<-filter(av_steps_interval,steps_avg==max(steps_avg))
interval_max[1,1]
```

```
## [1] 835
```

###Imputing missing values

This section of the assignment is about the missing values of the original data set. The tasks are as follows:

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The first task can be carried oout as follows:


```r
summary(activity_raw)
```

```
##      steps             date               interval     
##  Min.   :  0.00   Min.   :2012-10-01   Min.   :   0.0  
##  1st Qu.:  0.00   1st Qu.:2012-10-16   1st Qu.: 588.8  
##  Median :  0.00   Median :2012-10-31   Median :1177.5  
##  Mean   : 37.38   Mean   :2012-10-31   Mean   :1177.5  
##  3rd Qu.: 12.00   3rd Qu.:2012-11-15   3rd Qu.:1766.2  
##  Max.   :806.00   Max.   :2012-11-30   Max.   :2355.0  
##  NA's   :2304
```

The above summary shows that NAs only occur in the steps variable. It also gives the number of occurences but as the task was to calculate the number here the respective code:


```r
sum_na <- sum(is.na(activity_raw$steps))
```

Thus, answer is: 2304

The second task is to replace the NAs with an estimated value. I decided to use the mean (over all days) of the respective interval. This has already been calculated. A way to replace the NA in column steps is to merge the tables and then replace the NA in column A with the values of column B. To replace the values one has to create a subset of the rows with NA and in this subset replace the value in the steps column with the values of the steps_avg column of another subset of the table, which has been created by also looking for the NAs in the steps column. 

Task 3 is to use the devised strategy to create a new table with the replaced NAs. The code for this is as follows: 


```r
activity_narepl1<-merge(activity_raw,av_steps_interval,by="interval")
activity_narepl1$steps[is.na(activity_narepl1$steps)]<-activity_narepl1$steps_avg[is.na(activity_narepl1$steps)]
```

To carry out task 4 the code from above is recycled:


```r
activity_by_day_repl<-ddply(activity_narepl1, .(date), summarise, Steps = sum(steps)); 
mean_ts_repl<-mean(activity_by_day_repl$Steps)
median_ts_repl<-mean(activity_by_day_repl$Steps)
```

This gives us 10766 as mean and 10766 as median for the table with NAs replaced. Using the identical Function we can now determine if the values have changed.

Is the mean for both the same: TRUE  
Is the median for both the same: TRUE

In addition to the asked for new histogramm I have also replotted the original histogram, allowing for a direct comparison:


```r
par(mfcol=c(1,2),mar=c(5,4,2,2))
hist(activity_by_day_repl$Steps ,breaks=61, cex.axis=0.7, cex.main=0.8, cex.lab=0.7, xlab="Steps per day",ylab="Frequency",main="Steps per day\n(NAs replaced)")
hist(activity_by_day$Steps,breaks=61, cex.axis=0.7, cex.main=0.8, cex.lab=0.7, xlab="Steps per day",ylab="Frequency",main="Steps per day\n(original)")
```

![](PA1_template_files/figure-html/histogram_steps_pd_NAs_repl-1.png) 

As can be seen from the histograms the frequencies in which a certain number of steps has occured did actually change quite alot.

Finally we are asked to calculate the impact of our operation on the total daily number of steps. To answer this we will again recycle some code from above, merge the new result with the earlier one and then calculate the difference for each day:


```r
activity_by_day_repl<-ddply(activity_narepl1, .(date), summarise, Steps_incl_means = sum(steps)); #group by date and sum up steps
activity_by_day_merged<-merge(activity_by_day,activity_by_day_repl,by="date")
activity_by_day_merged$difference<-activity_by_day_merged$Steps_incl_means - activity_by_day_merged$Steps
activity_by_day_merged
```

```
##          date Steps Steps_incl_means difference
## 1  2012-10-01    NA         10766.19         NA
## 2  2012-10-02   126           126.00          0
## 3  2012-10-03 11352         11352.00          0
## 4  2012-10-04 12116         12116.00          0
## 5  2012-10-05 13294         13294.00          0
## 6  2012-10-06 15420         15420.00          0
## 7  2012-10-07 11015         11015.00          0
## 8  2012-10-08    NA         10766.19         NA
## 9  2012-10-09 12811         12811.00          0
## 10 2012-10-10  9900          9900.00          0
## 11 2012-10-11 10304         10304.00          0
## 12 2012-10-12 17382         17382.00          0
## 13 2012-10-13 12426         12426.00          0
## 14 2012-10-14 15098         15098.00          0
## 15 2012-10-15 10139         10139.00          0
## 16 2012-10-16 15084         15084.00          0
## 17 2012-10-17 13452         13452.00          0
## 18 2012-10-18 10056         10056.00          0
## 19 2012-10-19 11829         11829.00          0
## 20 2012-10-20 10395         10395.00          0
## 21 2012-10-21  8821          8821.00          0
## 22 2012-10-22 13460         13460.00          0
## 23 2012-10-23  8918          8918.00          0
## 24 2012-10-24  8355          8355.00          0
## 25 2012-10-25  2492          2492.00          0
## 26 2012-10-26  6778          6778.00          0
## 27 2012-10-27 10119         10119.00          0
## 28 2012-10-28 11458         11458.00          0
## 29 2012-10-29  5018          5018.00          0
## 30 2012-10-30  9819          9819.00          0
## 31 2012-10-31 15414         15414.00          0
## 32 2012-11-01    NA         10766.19         NA
## 33 2012-11-02 10600         10600.00          0
## 34 2012-11-03 10571         10571.00          0
## 35 2012-11-04    NA         10766.19         NA
## 36 2012-11-05 10439         10439.00          0
## 37 2012-11-06  8334          8334.00          0
## 38 2012-11-07 12883         12883.00          0
## 39 2012-11-08  3219          3219.00          0
## 40 2012-11-09    NA         10766.19         NA
## 41 2012-11-10    NA         10766.19         NA
## 42 2012-11-11 12608         12608.00          0
## 43 2012-11-12 10765         10765.00          0
## 44 2012-11-13  7336          7336.00          0
## 45 2012-11-14    NA         10766.19         NA
## 46 2012-11-15    41            41.00          0
## 47 2012-11-16  5441          5441.00          0
## 48 2012-11-17 14339         14339.00          0
## 49 2012-11-18 15110         15110.00          0
## 50 2012-11-19  8841          8841.00          0
## 51 2012-11-20  4472          4472.00          0
## 52 2012-11-21 12787         12787.00          0
## 53 2012-11-22 20427         20427.00          0
## 54 2012-11-23 21194         21194.00          0
## 55 2012-11-24 14478         14478.00          0
## 56 2012-11-25 11834         11834.00          0
## 57 2012-11-26 11162         11162.00          0
## 58 2012-11-27 13646         13646.00          0
## 59 2012-11-28 10183         10183.00          0
## 60 2012-11-29  7047          7047.00          0
## 61 2012-11-30    NA         10766.19         NA
```

Scrolling through the table one can easily see that a change only occured in those cases were there is an NA in the steps column. This implys that when an NA occured in the original data it occured for all intervals of the respective day.

TO give a final answer regarding the impact we can just sum up the steps over all days for both variables and calculate the difference:


```r
sum(activity_by_day_merged$Steps_incl_means) - sum(activity_by_day_merged$Steps, na.rm=TRUE)
```

```
## [1] 86129.51
```

Thus by replacing the missig values with the means the total number of steps increased by about 86129 steps.

###Are there differences in activity patterns between weekdays and weekends?

For the next part we are to use the original data and it is suggested to use the weekdays() function perform the following tasks:

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file for a similar plot.

Task one can be carried via the following code:


```r
activity_raw$weekday<-factor(weekdays(activity_raw$date)) #add new factor variable
activity_raw$weekday<- factor(activity_raw$weekday, levels = c(levels(activity_raw$weekday), "weekday","weekend")) #add the two new levels
weekday=c("Montag","Dienstag","Mittwoch","Donnerstag","Freitag") #These are German weekdays, depending on your location you might need to change them
weekend=c("Samstag","Sonntag")
activity_raw$weekday[activity_raw$weekday %in% weekday] <- "weekday" #replace old values
activity_raw$weekday[activity_raw$weekday %in% weekend] <- "weekend" #replace old values
activity_raw$weekday<- factor(activity_raw$weekday) #remove unused levels
```

For task 2 we first have to transform the data. For this transformation we reuse some of the code from above


```r
weekdays_interval<-ddply(activity_raw, .(interval,weekday), summarise, steps_avg = mean(steps,na.rm=TRUE)); #group by interval and calculate mean for all days
```

The following line produces the wanted plot:


```r
library(lattice)
xyplot(steps_avg~interval|weekday,data=weekdays_interval,layout=c(1,2),type="l",main ="Average no. of steps by interval", ylab="No. of steps (average)", xlab="Interval",cex.axis=0.7, cex.main=0.8, cex.lab=0.7)
```

![](PA1_template_files/figure-html/panel_plot-1.png) 

And this concludes Assignment 1!
