# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
unzip(zipfile="C:\\datascience\\rr\\RepData_PeerAssessment1\\activity.zip")
activity<-read.csv("C:\\datascience\\rr\\RepData_PeerAssessment1\\activity.csv")
```


## What is mean total number of steps taken per day?
### 1. Make a histogram of the total number of steps taken each day

```r
steps <- tapply(activity$steps, activity$date, FUN=sum, na.rm=T)
hist(steps, xlab="Total Steps per Day")
```

![plot of chunk unnamed-chunk-2](./PA1_template_files/figure-html/unnamed-chunk-2.png) 

### 2. Calculate and report the mean and median total number of steps taken per day

```r
mean(steps, na.rm=T)
```

```
## [1] 9354
```

```r
median(steps, na.rm=T)
```

```
## [1] 10395
```

## What is the average daily activity pattern?
### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval 
### (x-axis) and the average number of steps taken, averaged across all days 
### (y-axis)

```r
linedata <- aggregate(x=list(steps=activity$steps), by=list
                      (interval=activity$interval), FUN=mean, na.rm=T)
plot(linedata, type="l", ylab="Avg Steps Taken", xlab="5 Minute Interval ID")
```

![plot of chunk unnamed-chunk-4](./PA1_template_files/figure-html/unnamed-chunk-4.png) 

### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
linedata[which.max(linedata$steps),]
```

```
##     interval steps
## 104      835 206.2
```

## Inputing missing values
### Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.  
### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(!complete.cases(activity))
```

```
## [1] 2304
```
### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.  
### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.  

```r
activity$steps <- ifelse(is.na(activity$steps), linedata$steps[match(
     linedata$interval, activity$interval)], activity$steps)
sum(!complete.cases(activity))
```

```
## [1] 0
```
### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of inputing missing data on the estimates of the total daily number of steps?

```r
steps <- tapply(activity$steps, activity$date, FUN=sum, na.rm=T)
hist(steps, xlab="Total Steps per Day")
```

![plot of chunk unnamed-chunk-8](./PA1_template_files/figure-html/unnamed-chunk-8.png) 

```r
mean(steps)
```

```
## [1] 10766
```

```r
median(steps)
```

```
## [1] 10766
```
Both the mean and the median number of steps per day increase after replacing NA
values with calculated means for their respective intervals.

## Are there differences in activity patterns between weekdays and weekends?
### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activity$day <- ifelse(weekdays(as.Date(activity$date))==c("Saturday", "Sunday"), 
                       "weekend", "weekday")
activity$day<-factor(activity$day)
```
### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
library(ggplot2)
daytypeavg <- aggregate(steps ~ interval + day, data=activity, mean)
ggplot(daytypeavg, aes(interval, steps)) + geom_line(color="blue") + facet_grid(day ~ .) +
    xlab("5 Minute Interval ID") + ylab("Average Number of steps")
```

![plot of chunk unnamed-chunk-10](./PA1_template_files/figure-html/unnamed-chunk-10.png) 
