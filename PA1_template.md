# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

```r
  require(plyr)
```

```
## Loading required package: plyr
```

```r
  require(ggplot2)
```

```
## Loading required package: ggplot2
```

```r
  require(lattice)
```

```
## Loading required package: lattice
```

```r
  data <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
* Make a histogram of the total number of steps taken each day

```r
  ds1 <- ddply(data, .(date),  summarize,sum = sum(steps))
  hist(ds1$sum,col="red", xlab = "Total Steps per day", ylab = "Frequency", 
    main = "Total Steps taken per day")
```

![plot of chunk unnamed-chunk-2](./PA1_template_files/figure-html/unnamed-chunk-2.png) 

* Calculate `mean` and `median` total number of steps taken per day.

```r
  meanStepsPerDay <- mean(ds1$sum,na.rm=TRUE)
  medianStepsPerDay <- median(ds1$sum,na.rm=TRUE)
```
*
    + Mean of total steps per day:  10766 
    + Median of toal steps per day: 10765 

---

## What is the average daily activity pattern?
##### Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
  ds2 <- ddply(data, .(interval),  summarize,mean = mean(steps,na.rm=TRUE))
  xyplot( mean ~ interval , data=ds2 ,type ="l",xlab = "Time Intervals (5-minute)", 
    ylab = "Mean number of steps taken (all Days)", main = "Average number of Steps taken at different 5 minute Intervals")
```

![plot of chunk unnamed-chunk-4](./PA1_template_files/figure-html/unnamed-chunk-4.png) 

##### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
- Interval which contain maximum number of steps: 835

---

## Imputing missing values
##### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
missingCount <- sum(is.na(data$steps))
```
- No of missing values: 2304 


    
##### Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

  -  **Stategy Used** :  Replace missing values with mean for the interval
  

```r
  meanLookUp <-function(interval){
   ds2[which(ds2$interval == interval),]$mean
  }
  imputedData = data
  for (i in which(is.na(imputedData$steps))){
    imputedData[i,]$steps <- meanLookUp(imputedData[i,]$interval)
  }
```
##### Create a new dataset that is equal to the original dataset but with the missing data filled in.  
  - Imputed dataset is in imputedData  

###### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
  ds3 <- ddply(imputedData, .(date),  summarize,sum = sum(steps))
  hist(ds3$sum,col="green", xlab = "Total Steps per day", ylab = "Frequency", 
    main = "Total Steps taken per day")
```

![plot of chunk unnamed-chunk-7](./PA1_template_files/figure-html/unnamed-chunk-7.png) 

```r
  meanStepsPerDayImputed <- mean(ds3$sum)
  medianStepsPerDayImputed <- median(ds3$sum)
```


  - Mean  total steps per day (using inputedData):  10766 
  - Median total steps per day (using inputedData): 10766

  - Mean values are same because as we used average mean values for missing values. median values shows a little difference as inputed data set would move the median depending on where the missing values were in the original set .

---

## Are there differences in activity patterns between weekdays and weekends?
##### Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


##### Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

