What is mean total number of steps taken per day? For this part of the
assignment, you can ignore the missing values in the dataset.

1.  Calculate the total number of steps taken per day:

<!-- -->

    dfcomplete<-read.csv ("activity.csv",header = TRUE)
    df<-na.omit(dfcomplete)
    steps<- aggregate(df$steps,by=list(date=df$date),sum)
    names(steps)[2] <- "Steps"

1.  Make a histogram of the total number of steps taken each day:

<!-- -->

    hist(steps$`Steps`,main = "Total Number of Steps Each Day",xlab = "Steps Each Day")

![](PA1_template_files/figure-markdown_strict/hist-1.png)

1.  Calculate and report the mean and median of the total number of
    steps taken per day:

<!-- -->

    meannum = mean(steps$`Steps`)
    mediannum = median(steps$`Steps`)

The mean of total number of steps taken per day is 10766.1886792; the
median is 10765.

What is the average daily activity pattern? 1. Make a time series plot
(i.e. type="l") of the 5-minute interval (x-axis) and the average number
of steps taken, averaged across all days (y-axis)

    timeavg <-aggregate(df$steps,list(df$interval), mean)
    names(timeavg)<-c("interval","avg steps")
    plot(timeavg, type="l", main="5 min Interval Time Series", ylab="Average number of Steps", xlab="5 min interval", col="red")

![](PA1_template_files/figure-markdown_strict/time%20series-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    print(timeavg[which.max(timeavg$`avg steps`),1])

    ## [1] 835

Imputing missing values Note that there are a number of days/intervals
where there are missing values (coded as NAs). The presence of missing
days may introduce bias into some calculations or summaries of the data.

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs)

<!-- -->

    dfna<-dfcomplete[!complete.cases(dfcomplete),]
    print(nrow(dfna))

    ## [1] 2304

1.  Devise a strategy for filling in all of the missing values in
    the dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.

Strategy: Use the mean for that 5-minute interval for the NA values

3.Create a new dataset that is equal to the original dataset but with
the missing data filled in.

    dfnafill<- merge(x=dfna,y=timeavg, by="interval", all.x=TRUE)
    dfnafill<-dfnafill[,c(4,3,1)]
    names(dfnafill)[1]<-"steps"
    dfclean<-rbind(df,dfnafill)
    dfclean<-dfclean[order(dfclean$date, dfclean$interval),]

1.  Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- -->

    dfcleanday<-dfclean[,c(1,2)]
    dfcleanday<- aggregate(dfcleanday$steps,by=list(date=dfcleanday$date),sum)
    hist(dfcleanday$'x',main = "Total Number of Steps Each Day(NA fillded)",xlab = "Steps Each Day")

![](PA1_template_files/figure-markdown_strict/histnafill-1.png)

    meannafill<-mean(dfcleanday$x)
    mediannafill<-median(dfcleanday$x)

The mean of total number of steps taken per day is 10766.1886792; the
median is 10766.1886792.The values are about the same as the previous
estimates. The impact of imputing missing data on the estimates of the
total daily number of steps is the frequency increase due to the total
number of observations' increase.

Are there differences in activity patterns between weekdays and
weekends? For this part the weekdays,weekdays() function may be of some
help here. Use the dataset with the filled-in missing values for this
part.

1.  Create a new factor variable in the dataset with two levels -
    "weekday" and "weekend" indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    dfclean$date <-as.Date(dfclean$date,"%Y-%m-%d")
    dfclean$weekday<-weekdays(dfclean$date)
    dfclean<-within(dfclean,weekday[(weekday =='Saturday')|(weekday=='Sunday')]<-'weekend')
    dfclean<-within(dfclean,weekday[(weekday!='weekend')]<-'weekday')

1.  Make a panel plot containing a time series plot (i.e.type="l") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).

<!-- -->

    avgsteps <- aggregate(dfclean$steps ~ dfclean$interval + dfclean$weekday, dfclean, mean)
    names(avgsteps) <- c("interval","Day","AvgSteps")
    par(mfcol=c(2,1))
    avgstepsweekday <- avgsteps[avgsteps$Day == "weekday",]
    avgstepsweekend <- avgsteps[avgsteps$Day == "weekend",]
    plot(avgstepsweekday$interval, avgstepsweekday$AvgSteps,type = "l", col="blue", main="Weekday Avg.Steps",xlab = "Interval", ylab = "Avg Steps")
    plot(avgstepsweekend$interval, avgstepsweekend$AvgSteps,type = "l",col="red",xlab = "Interval", ylab = "Avg Steps",main="Weekend Avg.Steps")

![](PA1_template_files/figure-markdown_strict/weekdaysplot-1.png)
