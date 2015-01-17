# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
activity = read.csv('activity.csv')
activity <- transform(activity, date = as.Date(date))
```

## What is mean total number of steps taken per day?


```r
sumSteps = aggregate(steps ~ date, activity, sum, na.action=na.omit)
hist(sumSteps$steps, breaks=10, main="Total number of steps taken each day", xlab="Steps", ylab="Count")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
mean(sumSteps$steps)
```

```
## [1] 10766.19
```

```r
median(sumSteps$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?


```r
meanSteps = aggregate(steps ~ interval, activity, mean, na.action=na.omit)
plot(x = meanSteps$interval, y = meanSteps$steps, type='l', main="Aaverage number of steps taken", xlab="Interval", ylab="Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
meanSteps[which.max(meanSteps$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

## Imputing missing values


```r
length(which(is.na(activity$steps)))
```

```
## [1] 2304
```

```r
activity_fill <- activity
for (i in which(is.na(activity_fill$steps))) {
  activity_fill[i, "steps"] <- meanSteps[meanSteps$interval == activity_fill[i, "interval"],"steps"]
}
sumSteps_fill = aggregate(steps ~ date, activity_fill, sum)
hist(sumSteps_fill$steps, breaks=10, main="Total number of steps taken each day (including missing value)", xlab="Steps", ylab="Count")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
mean(sumSteps_fill$steps)
```

```
## [1] 10766.19
```

```r
median(sumSteps_fill$steps)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?


```r
library(lattice)
activity_fill$weekend_flag <- ifelse(weekdays(activity_fill[,"date"]) == "일요일", "weekend", "weekday")
meanSteps_week = aggregate(steps ~ interval + weekend_flag, activity_fill, mean)
xyplot(steps ~ interval | weekend_flag, meanSteps_week, layout=c(1,2), type="l", main="Average number of steps taken (including missing value)", xlab="Interval", ylab="Number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
