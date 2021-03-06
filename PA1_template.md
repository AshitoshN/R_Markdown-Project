---
title: "Reproducible Research"
author: "Ashitosh Nale"
date: "27/06/2020"
output:
  html_document: default
  pdf_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Assignment week 2 Coursera

This is an R Markdown document for Assignment week 2 Coursera.

We are provided with the data from personal activity monitoring device. This device collects data at 5 interval through out the day. The data consists of two months of data from an anonymous individual collected during the month of October and November,2012 and include the number of steps taken in 5 intervals each day.

## Loading the Data
```{r}
data <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day ?


Firstly we will calculate the total number of steps taken per day

```{r}
m <- tapply(data$steps,data$date, sum ,na.rm = FALSE)
```

Lets remove the missing values 

```{r}
steps_per_day <- m[complete.cases(m)]
head(steps_per_day)
```

Plot the histogram for Total number of steps taken each day

```{r}
hist(steps_per_day, main = "Total Number of steps taken each day", ylab = "No of Days")
```

Calculate Mean and Median of total number of steps taken each day

```{r}
Mean <- mean(steps_per_day)
Mean
Median <- median(steps_per_day)
Median
```

So the calculated mean and median is `r Mean` and `r Median` respectively.

## What is the average daily actvity pattern ?

```{r}
steps <- tapply(data$steps,data$interval,mean, na.rm = TRUE)
plot(names(steps),steps,type = "l",xlab = "Interval")
```

Which 5 min-Interval, on average across all the day in the dataset, contains the maximum number of steps?

```{r}
names(which.max(steps))
```

So the $`r names(which.max(steps))`^{th}$  5 min interval contains maximum number of steps.

```{r}
n <- data.frame(tapply(data$steps,data$interval, mean ,na.rm = TRUE))
colnames(n)= "Mean"
n[is.na(n)] <- 0
head(n)
```

The NAs in steps coloumn are replaced by taking average of steps of corresponding Intervals 

```{r}
data[is.na(data)] = n$Mean
```

Now we have the data with zero **NA** values.  

```{r}
sum(is.na(data))
```

```{r}
steps <- tapply(data$steps,data$date,sum)
```

Histogram of the total number of steps taken each day & calculate and report mean & median total number of steps taken per day.

```{r}
hist(steps,xlab = "Steps",ylab = "frequency", main = "Total Daily Steps")
```

Calculate Mean and Median of total number of steps taken each day

```{r}
Mean1 <- mean(steps)
Mean1
Median1 <- median(steps)
Median1
```

Compare Old Mean Values and new one

```{r}
Mean
Mean1
Median
Median1
```

## Are there differences in activity patterns between weekdays and weekends?

Addimg the coloumn "Days" is data as Weekend or Weekday

```{r}
data$date <- as.Date(data$date,"%Y-%m-%d")
data$day <- weekdays(data$date)
data$Days <- ifelse(data$day =="Saturday" | data$day=="Sunday", "Weekend", "Weekday" )
```
 
Averaging no. of steps along with Interval and Days
 
```{r}
data1 <- aggregate(data$steps,list(data$Days,data$interval),mean)
names(data1) <- c("Days","Interval","Steps")
```

Comparison of Number of Steps in each Interval

```{r}
library(ggplot2)
g <- ggplot(data1,aes(Interval,Steps,color = Days))
g + geom_line() + facet_grid(Days~.) +ggtitle("Comparison of Number of Steps in each Interval")
```