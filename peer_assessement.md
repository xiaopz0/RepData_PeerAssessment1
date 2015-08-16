# Reproducible Research: Peer Assessment 1

```r
mydata = read.csv("~/git/RepData_PeerAssessment1/activity.csv")
hist(mydata[,"steps"])
```

![](peer_assessement_files/figure-html/unnamed-chunk-1-1.png) 

```r
mean(mydata[,"steps"], na.rm = T)
```

```
## [1] 37.3826
```

```r
median(mydata[,"steps"], na.rm = T)
```

```
## [1] 0
```
## Loading and preprocessing the data
Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
library(zoo)
```

```
## 
## Attaching package: 'zoo'
## 
## The following objects are masked from 'package:base':
## 
##     as.Date, as.Date.numeric
```

```r
dates = levels(mydata$date)
newVariable = dates[as.numeric(mydata$date)]
mydataTS = zoo(mydata[,"steps"], as.Date(newVariable))
```

```
## Warning in zoo(mydata[, "steps"], as.Date(newVariable)): some methods for
## "zoo" objects do not work if the index entries in 'order.by' are not unique
```

```r
plot(mydataTS, type ="l")
```

![](peer_assessement_files/figure-html/unnamed-chunk-2-1.png) 


Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
myindex = which(mydata[,"steps"] %in% max(mydata[,"steps"], na.rm=T))
mydata[myindex,"interval"]
```

```
## [1] 615
```

```r
sum(is.na(mydata[,"steps"]))
```

```
## [1] 2304
```

## Imputing missing values

1. Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

2. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(mydata[,"steps"]))
```

```
## [1] 2304
```
3. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
myindex = is.na(mydata[,"steps"])
mydata[which(myindex),"steps"] = mean(mydata[,"steps"], na.rm = T)
```
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
hist(tapply(mydata$steps, mydata$date, FUN=sum))
```

![](peer_assessement_files/figure-html/unnamed-chunk-6-1.png) 
## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
newVariable = weekdays(as.POSIXlt(newVariable)) %in% c('Saturday', 'Sunday')
weekday = newVariable
for (i in 1:length(weekday)){
  if (weekday[i]){
    weekday[i] = "weekend"
  } 
  else {
    weekday[i] = "weekday"
  }
}
mydata = cbind(mydata,weekday)
```
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
library(lattice)
xyplot(steps ~ interval | weekday,
       data = mydata,
       type = "l")
```

![](peer_assessement_files/figure-html/unnamed-chunk-8-1.png) 
