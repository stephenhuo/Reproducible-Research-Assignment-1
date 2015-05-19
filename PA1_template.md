---
title: "R markdown file"
author: "Yongyang Huo"
date: "Sunday, May 17, 2015"
output: html_document
---

Read data from file:


```r
##load data
data <- read.csv("C:/Downloads/repdata-data-activity/activity.csv")
```

Histogram of the total number of steps taken each day


```r
d1<-subset(data,steps!='NA')
d1[,2]<-as.Date(d1[,2],"%Y-%m-%d")
dates <- unique(d1[,2])
totals <- as.numeric(c())
for (i in 1:length(dates)){
        totals[i]=sum(d1[,1][which(d1[,2]==dates[i])])
}
hist(totals,main="Total number of steps taken each day",col="red")
```

![plot of chunk unnamed-chunk-2](figure/1.png) 

```r
meantotal=mean(totals)
mediantotal=median(totals)
```
The mean of total number of steps taken per day is 1.0766189 &times; 10<sup>4</sup>.
The median of total number of steps taken per day is 1.0765 &times; 10<sup>4</sup>.

Average daily activity pattern


```r
##get average number of steps for each interval
intervals <- unique(d1[,3])
stepseveryinterval<-as.numeric(c())
for (m in 1:length(intervals)){
        a <- subset(d1,interval==intervals[m])
        datesa <- unique(a[,2])
        stepseveryinterval[m]=mean(a[,1])
}
plot(intervals,stepseveryinterval,type="l")
```

![plot of chunk unnamed-chunk-3](figure/2.png) 

```r
maxsteps <- max(stepseveryinterval)
df<-data.frame("interval"=intervals, "step"=stepseveryinterval)
for (n in 1:length(df[,"interval"])){
        if(df[n,"step"]==maxsteps){
                maxinterval<-df[n,"interval"]
        }
}
```
The interval 835 contains the maximum number of steps.

Missing values


```r
##imput missing values with mean
countNAs <- 0
newsteps <-as.numeric(c())
for (i in 1:length(data[,1])){
        if (is.na(data[i,1])){
                countNAs <- countNAs + 1
                int <- data[i,3]
                for (j in 1:length(df[,1])){
                        if (df[j,1]==int){
                                newsteps[i]<-df[j,2]
                        }
                }
        }
        else{
                newsteps[i]=data[i,1]
        }
}
newdata <- data.frame("steps"=newsteps, "date"=data[,2],"interval"=data[,3])
newdata[,2]<-as.Date(newdata[,2],"%Y-%m-%d")
newdates <- unique(newdata[,2])
newtotals <- as.numeric(c())
for (i in 1:length(newdates)){
        newtotals[i]=sum(newdata[,1][which(newdata[,2]==newdates[i])])
}
hist(newtotals,main="Total number of steps taken each day",col="red")
```

![plot of chunk unnamed-chunk-4](figure/3.png) 

```r
newmeantotal=mean(newtotals)
newmediantotal=median(newtotals)
```
The mean of total number of steps taken per day is 1.0766189 &times; 10<sup>4</sup>.
The median of total number of steps taken per day is 1.0766189 &times; 10<sup>4</sup>.

The total numbers of steps increase because all the NA values are replaced with mean numbers.
The mean remains the same, and the median is the same as the mean because the data set is more evenly distributed.

Activity patterns between weekdays and weekends

```r
##lattice panel plot for weekday and weekend activity pattern
weekdays <-as.character(c())
for (t in 1:length(newdata[,1])){
        if(weekdays(newdata[t,2])=='Saturday'|weekdays(newdata[t,2])=='Sunday'){
                weekdays[t]<-'Weekend'
           }
        else{
                weekdays[t]<-'weekday'
        }
}
newdata[,"weekday"] <- weekdays
newstepseveryinterval<-as.numeric(c())
newweekdays <-as.character(c())
newintervals <- as.numeric(c())
for (m in 1:length(intervals)){
        b <- subset(newdata,interval==intervals[m])
        bweekday <- subset(b,weekday=='weekday')
        newstepseveryinterval[m]<-mean(bweekday[,1])
        newweekdays[m]<-'weekday'
        newintervals[m]<-intervals[m]
}
le <- length(newstepseveryinterval)
for (m in 1:length(intervals)){
        c <- subset(newdata,interval==intervals[m])
        cweekend <- subset(c,weekday!='weekday')
        newstepseveryinterval[le+m]<-mean(cweekend[,1])
        newweekdays[le+m]<-'weekend'
        newintervals[le+m]<-intervals[m]
}
df2<-data.frame("interval"=newintervals, "step"=newstepseveryinterval,"weekday"=newweekdays)
library(lattice)
xyplot(newstepseveryinterval ~ newintervals | factor(weekday), data=df2, type="l", layout=c(1,2),xlab="Interval",ylab="Number of steps")
```

![plot of chunk unnamed-chunk-5](figure/4.png) 
