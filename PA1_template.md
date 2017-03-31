Loading Data
------------

    rm(list=ls())
    setwd("C:/Users/amora/OneDrive/Documents/R_Projects/Coursera/Reproducable_research/Project_1")
    activity<-read.csv("activity.csv",header=TRUE,na.strings="NA")
    activity$steps<-as.numeric(activity$steps)

What is the mean number of steps taken per day?
-----------------------------------------------

Creating Histogram with Steps taken per day

    TotalSteps<-as.data.frame(with(activity,tapply(steps,date,sum,na.rm=TRUE)))
    colnames(TotalSteps)<-c("Steps")
    hist(TotalSteps$Steps,main=paste("Histogram of Steps Takes"),xlab="Steps per Day")

![](PA1_template_files/figure-markdown_strict/StepsPerDay-1.png)

Calculating Mean and Median of Steps

    StepsSummary<-summary(TotalSteps$Steps)
    StepsSummary[3]

    ## Median 
    ##  10400

    StepsSummary[4]

    ## Mean 
    ## 9354

What is the average daly activity pattern?
------------------------------------------

Making a time series plot

    ActivitySummary<-as.data.frame(with(activity,tapply(steps,interval,mean,na.rm=TRUE)))
    ActivitySummary$Interval<-rownames(ActivitySummary)
    colnames(ActivitySummary)<-c("Avg_Steps","Interval")
    plot(x=ActivitySummary$Interval,ActivitySummary$Avg_Steps,
         type="l",
         main="Avg Steps per time Interval",
         xlab="Interval",
         ylab="Avg Steps taken per 5 minutes interval",
         col="blue")

![](PA1_template_files/figure-markdown_strict/MakingTimePlot-1.png)

Which Interval has the maximun number of steps?

    max.steps<-which.max(ActivitySummary$Avg_Steps)
    ActivitySummary[max.steps,]

    ##     Avg_Steps Interval
    ## 835  206.1698      835

Inputing Missing Values
-----------------------

Determining number of NAs in the Steps Column

    activity.summary<-summary(activity$steps)
    activity.summary[7]

    ## NA's 
    ## 2304

Replacing NAs with Average for that interval

    activity2<-activity
    for (i in 1:length(activity2$steps)) {
            if (is.na(activity2[i,1])==TRUE) {
                    activity2[i,1]<-ActivitySummary[which(ActivitySummary$Interval==activity2[i,3]),1]
            }
    }

Making Histogram with new data
------------------------------

    TotalSteps2<-as.data.frame(with(activity2,tapply(steps,date,sum,na.rm=TRUE)))
    colnames(TotalSteps2)<-c("Steps")
    hist(TotalSteps$Steps,main=paste("Histogram of Steps Takes"),xlab="Steps per Day")

![](PA1_template_files/figure-markdown_strict/Newhistogram-1.png)

Calculating Mean and Median of Steps

    StepsSummary<-summary(TotalSteps2$Steps)
    StepsSummary[3]

    ## Median 
    ##  10770

    StepsSummary[4]

    ##  Mean 
    ## 10770

Making weekday calculations

    ## Making Factor for Weekday / Weekend
    activity2$weekday<-as.factor(weekdays(as.Date(activity2$date)))
    activity2$wd<-as.factor(ifelse(activity2$weekday %in% c("Saturday","Sunday"), "Weekend","Weekday"))

    ## Taking Averages
    #Weekend
    activity2.weekend<-activity2[activity2$wd=="Weekend",]
    ActivitySummary2.weekend<-as.data.frame(with(activity2.weekend,tapply(steps,interval,mean,na.rm=TRUE)))
    ActivitySummary2.weekend$Interval<-rownames(ActivitySummary2.weekend)
    colnames(ActivitySummary2.weekend)<-c("Weekend_Steps","Interval")
    ActivitySummary2.weekend$Weekend_Steps<-as.numeric(ActivitySummary2.weekend$Weekend_Steps)

    #Weekdays
    activity2.weekday<-activity2[activity2$wd=="Weekday",]
    ActivitySummary2.weekday<-as.data.frame(with(activity2.weekday,tapply(steps,interval,mean,na.rm=TRUE)))
    ActivitySummary2.weekday$Interval<-rownames(ActivitySummary2.weekday)
    colnames(ActivitySummary2.weekday)<-c("Weekday_Steps","Interval2")
    ActivitySummary2.weekday$Weekday_Steps<-as.numeric(ActivitySummary2.weekday$Weekday_Steps)

    #Merging
    final.df<-cbind(ActivitySummary2.weekend,ActivitySummary2.weekday)

    #Plotting
    plot(x=ActivitySummary2.weekend$Interval,y=ActivitySummary2.weekend$Weekend_Steps,
         main="Steps per Time Interval by Weekday / Weekend",
         ylab="Avg. Steps Taken",
         xlab="Time Interval",
         type="l",
         ylim=c(0,250),
         col="blue")
    lines(x=ActivitySummary2.weekday$Interval,y=ActivitySummary2.weekday$Weekday_Steps,
          col="red")
    legend("topright",legend=c("Weekend","Weekday"),lty=c(1,1),col=c("blue","red"))

![](PA1_template_files/figure-markdown_strict/weekdays1-1.png)
