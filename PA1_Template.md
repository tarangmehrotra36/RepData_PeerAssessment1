R Markdown
----------

Loading and preprocessing the data: We change the date column from
Factor to date class.

    features <- read.csv('./activity.csv', header = T, sep = ',')

    str(features)

    ## 'data.frame':    17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : Factor w/ 61 levels "10/01/2012","10/02/2012",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...

    features$date<-as.Date(features$date,"%m/%d/%Y")

    str(features)

    ## 'data.frame':    17568 obs. of  3 variables:
    ##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
    ##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
    ##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...

Including Plots
---------------

For calculating the total steps, date-wise after ignoring missing
values.

    features1<-features[which(!(is.na(features$steps))),]

    steps_total<-tapply(features1$steps,features1$date,sum)

Histogram of the total number of steps taken each day:

    hist(steps_total,main="Histogram of total number of steps per day")

![](Reproducible_research_files/figure-markdown_strict/unnamed-chunk-3-1.png)

To Calculate and report the mean and median of the total number of steps
taken per day

Median:

    summary(steps_total)[3]

    ## Median 
    ##  10765

Mean:

    summary(steps_total)[4]

    ##     Mean 
    ## 10766.19

Time series plot(type="l") of the 5-minute interval (x-axis) and the
average number of steps taken, averaged across all days:

    steps_mean_interval<-tapply(features1$steps,features1$interval,mean)

    plot(steps_mean_interval, type='l', 
         main="Average number of steps per interval for all days", xlab="Interval", 
         ylab="Average number of steps")

![](Reproducible_research_files/figure-markdown_strict/unnamed-chunk-6-1.png)

Which 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps?

    which.max(steps_mean_interval)

    ## 835 
    ## 104

Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NAs)

    v<-is.na(features)

    sum(v)

    ## [1] 2304

Create a new dataset that is equal to the original dataset but with the
missing data filled in with the average value of that interval:

    features3<-features

    features3$steps[which((is.na(features3$steps)))] <- steps_mean_interval[which((is.na(features3$steps)))%%nrow(steps_mean_interval)]
     
    steps_total_after_missing<-tapply(features3$steps,features3$date,sum)

    steps_total_after_missing

    ## 2012-10-01 2012-10-02 2012-10-03 2012-10-04 2012-10-05 2012-10-06 
    ##   10766.83     126.00   11352.00   12116.00   13294.00   15420.00 
    ## 2012-10-07 2012-10-08 2012-10-09 2012-10-10 2012-10-11 2012-10-12 
    ##   11015.00   10765.45   12811.00    9900.00   10304.00   17382.00 
    ## 2012-10-13 2012-10-14 2012-10-15 2012-10-16 2012-10-17 2012-10-18 
    ##   12426.00   15098.00   10139.00   15084.00   13452.00   10056.00 
    ## 2012-10-19 2012-10-20 2012-10-21 2012-10-22 2012-10-23 2012-10-24 
    ##   11829.00   10395.00    8821.00   13460.00    8918.00    8355.00 
    ## 2012-10-25 2012-10-26 2012-10-27 2012-10-28 2012-10-29 2012-10-30 
    ##    2492.00    6778.00   10119.00   11458.00    5018.00    9819.00 
    ## 2012-10-31 2012-11-01 2012-11-02 2012-11-03 2012-11-04 2012-11-05 
    ##   15414.00   10765.25   10600.00   10571.00   10765.26   10439.00 
    ## 2012-11-06 2012-11-07 2012-11-08 2012-11-09 2012-11-10 2012-11-11 
    ##    8334.00   12883.00    3219.00   10765.19   10767.21   12608.00 
    ## 2012-11-12 2012-11-13 2012-11-14 2012-11-15 2012-11-16 2012-11-17 
    ##   10765.00    7336.00   10765.64      41.00    5441.00   14339.00 
    ## 2012-11-18 2012-11-19 2012-11-20 2012-11-21 2012-11-22 2012-11-23 
    ##   15110.00    8841.00    4472.00   12787.00   20427.00   21194.00 
    ## 2012-11-24 2012-11-25 2012-11-26 2012-11-27 2012-11-28 2012-11-29 
    ##   14478.00   11834.00   11162.00   13646.00   10183.00    7047.00 
    ## 2012-11-30 
    ##   10765.98

Histogram of the new data with missing values replaced.

    hist(steps_total_after_missing,main="Histogram of total number of steps per day")

![](Reproducible_research_files/figure-markdown_strict/unnamed-chunk-10-1.png)

Mean and Median of the new data set:

    summary(steps_total_after_missing)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##      41    9819   10765   10766   12811   21194

Panel plot comparing the average number of steps taken per 5-minute
interval across weekdays and weekends:

    library(lattice)

    x<-(weekdays(features3$date,abbr = T ))
    x<-gsub("S(at|un)","Weekend",x)
    x<-gsub("Mon|Tue|Wed|Thu|Fri","Weekday",x)

    features3$day<-as.factor(x)

    avg_step_imp <- aggregate(steps ~ interval + day, data = features3, mean)

    xyplot(steps~interval|day,data=avg_step_imp,layout(1,2),type = "l")

![](Reproducible_research_files/figure-markdown_strict/unnamed-chunk-12-1.png)
