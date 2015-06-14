# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
unzip("activity.zip")
activity <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
1. Make a histogram of the total number of steps taken each day

```r
totalSteps <- aggregate(steps ~ date, data = activity, sum, na.rm=TRUE)
hist(totalSteps$steps)
```

![](PA1_files/figure-html/stepsHist-1.png) 

2. Calculate and report the mean and median total number of steps taken per day

```r
mean(totalSteps$steps, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(totalSteps$steps, na.rm=TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
stepsInterval <- aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
plot(stepsInterval, type="l")
```

![](PA1_files/figure-html/stepsInterval-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
stepsInterval$interval[which.max(stepsInterval$steps)]
```

```
## [1] 835
```

## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

*The mean of 5-minute interval steps (which is calculated before) is used to fill in the missing values.*

```r
activity <- merge(activity, stepsInterval, by = "interval", suffixes = c("", ".mean"))
activity$steps[is.na(activity$steps)] <- activity$steps.mean[is.na(activity$steps)]
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity$steps.mean <- NULL
activity <- activity[order(activity$date, activity$interval), ]
activity <- activity[, c(2, 3, 1)]
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
totalSteps <- aggregate(steps ~ date, data = activity, sum, na.rm=TRUE)
hist(totalSteps$steps)
```

![](PA1_files/figure-html/updateCompare-1.png) 

```r
mean(totalSteps$steps)
```

```
## [1] 10766.19
```

```r
median(totalSteps$steps)
```

```
## [1] 10766.19
```
*The mean does not change, but the median does change.*

## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activity$day <- as.POSIXlt(activity$date)$wday
activity$day <- ifelse(activity$day %in% c(0, 6), "weekend", "weekday")
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
stepsDay <- aggregate(steps ~ interval + day, data = activity, mean)
library(lattice)
xyplot(steps ~ interval | factor(day), data = stepsDay, layout = c(1,2), type="l", ylab = "Number of steps")
```

![](PA1_files/figure-html/dayPlot-1.png) 
