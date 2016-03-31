# Reproducible Research: Peer Assessment 1
## Loading and preprocessing the data
##### 1. Load the data (i.e. read.csv())


```r
echo = TRUE
activityData <- read.csv('activity.csv')
```


```
## Warning: package 'ggplot2' was built under R version 3.2.4
```
##### 2. Process/transform the data (if necessary) into a format suitable for your analysis

## What is mean total number of steps taken per day?

##### 1. Calculate the total number of steps taken per day


```r
aggStepsByDay <- tapply(activityData$steps, activityData$date, sum, na.rm=TRUE)
```

##### 2. Make a histogram of the total number of steps taken each day

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)

##### 3. Calculate and report the mean and median of the total number of steps taken per day


```r
stepsByDayMean <- mean(aggStepsByDay)
stepsByDayMean
```

```
## [1] 9354.23
```

```r
stepsByDayMedian <- median(aggStepsByDay)
stepsByDayMedian
```

```
## [1] 10395
```

## What is the average daily activity pattern?
##### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
avgStepsPerInterval <- aggregate(x = list(AvgSteps=activityData$steps), by = list(Interval = activityData$interval), FUN = mean, na.rm = TRUE)
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)

##### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
avgStepsPerInterval[avgStepsPerInterval$AvgSteps == max(avgStepsPerInterval$AvgSteps), ]
```

```
##     Interval AvgSteps
## 104      835 206.1698
```

## Imputing missing values
##### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(activityData))
```

```
## [1] 2304
```

##### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
##### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
completeData <- activityData 
for (i in 1:nrow(completeData)) {
  if (is.na(completeData$steps[i])) {
    completeData$steps[i] <- avgStepsPerInterval[which(completeData$interval[i] == avgStepsPerInterval$Interval), ]$AvgSteps
  }
}
```

##### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
aggStepsByDayNA <- tapply(completeData$steps, completeData$date, sum, na.rm=TRUE)
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)


```r
stepsByDayMeanNA <- mean(aggStepsByDayNA)
stepsByDayMeanNA
```

```
## [1] 10766.19
```

```r
stepsByDayMedianNA <- median(aggStepsByDayNA)
stepsByDayMedianNA
```

```
## [1] 10766.19
```

After imputing the missing data the mean and the median are the same and they have both increased from the original numbers. 

## Are there differences in activity patterns between weekdays and weekends?
##### 1. Create a new factor variable in the dataset with two levels  weekday and weekend indicating whether a given date is a weekday or weekend day.


```r
completeData$dayType <-  ifelse(as.POSIXlt(completeData$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

##### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
avgCompleteData <- aggregate(steps ~ interval + dayType, data=completeData, mean)
```

![](PA1_template_files/figure-html/unnamed-chunk-16-1.png)
