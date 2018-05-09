+++
title = "Date Anomolies"

date = 2018-04-25
lastmod = 2018-04-25
draft = false


tags = ["dates", "R"]
summary = "About working with dates and time in R"


[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-default.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-ocean.png"
caption = "Ocean"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-dark.png"
caption = "Dark"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-forest.png"
caption = "Default"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-coffee-playfair.png"
caption = "Coffee theme with Playfair font"

[[gallery_item]]
album = "1"
image = "https://raw.githubusercontent.com/gcushen/hugo-academic/master/images/theme-1950s.png"
caption = "1950s"
+++


As we all know, dates can be a harrowing/boarderline traumatic experience. Here I give a condensed and hopefully rapid overview of dates in R.

There are multiple date classes in R. The simplest is the Date class, but there are also POSIXt dates and a relatively recent Lubridate package. 

## Date Class

These are the `easy' ones to deal with and are stored internally as integers from 1970-01-01.


```
as.Date("2017-01-12")
as.Date("2017-24-01", format = "%Y-%d-%m")
# Alternative origin - i.e. Excel
as.Date(400, origin = "1900-01-01")
difftime(as.Date("2018-01-01"), as.Date("2017-01-01"), units = "days")
```


## POSIXt - datetime variants

Portable Operating System Interface (POSIX) is an interoperability standard (that not many people seem to have much faith in). In R there is POSIXlt and POSIXct for local and for calendar time respecitively. The former is a list, the latter is a number (seconds since origin):

```
> unclass(as.POSIXlt(Sys.time()))
$sec
[1] 30.34591

$min
[1] 35

$hour
[1] 12

$mday
[1] 25

$mon
[1] 3

$year
[1] 118

$wday
[1] 3

$yday
[1] 114

$isdst
[1] 0

$zone
[1] "AEST"

$gmtoff
[1] 36000

attr(,"tzone")
[1] ""     "AEST" "AEDT"



# POSIXct
unclass(Sys.time())
[1] 1524623797
```


You can display (or store) dates in different formats by using the format command or option:

```
as.POSIXct("101221 10:22", format = "%y%m%d %H:%M")
[1] "2010-12-21 10:22:00 AEDT"

as.character(as.POSIXct("101221 10:22", 
             format = "%y%m%d %H:%M"), format = "%m-%d-%y %H:%M")
[1] "12-21-10 10:22"

```

Data frames do weird things to dates. Specifically, when you store a `POSIXlt` variable in a `data.frame` it is converted to a POSIXct. However, if you assign a POSIXlt to a data.frame field using `$` then the lt class is retained!

```
hotdate <- "20081101 01:20:00"
ct <- as.POSIXct(hotdate, format = "%Y%m%d %H:%M:%S")
lt <- as.POSIXlt(hotdate, format = "%Y%m%d %H:%M:%S")

df <- data.frame(orig = hotdate, 
                 ct = ct, 
                 lt = lt)

# POSIXlt converted to ct
> str(df)
'data.frame': 1 obs. of  3 variables:
 $ orig: Factor w/ 1 level "20081101 01:20:00": 1
 $ ct  : POSIXct, format: "2008-11-01 01:20:00"
 $ lt  : POSIXct, format: "2008-11-01 01:20:00"

# POSIXlt NOT converted to ct!!!!
df$lt2 <- as.POSIXlt(hotdate, format = "%Y%m%d %H:%M:%S")
str(df)
'data.frame': 1 obs. of  4 variables:
 $ orig: Factor w/ 1 level "20081101 01:20:00": 1
 $ ct  : POSIXct, format: "2008-11-01 01:20:00"
 $ lt  : POSIXct, format: "2008-11-01 01:20:00"
 $ lt2 : POSIXlt, format: "2008-11-01 01:20:00"


# Rounding
# NOPE
df[, "ct"] <- round(df[, "ct"], units = "hours")

# FORCE
df[, "ct"] <- as.POSIXct(round(ct, units = "hours"))

# NAH
df[, "lt"] <- round(lt, units = "hours")
Warning message:
  In `[<-.data.frame`(`*tmp*`, , "lt", 
                      value = list(sec = 0, min = 0L,  :
                      provided 11 variables to replace 1 variables

# MAGIC via $  !
df$lt <- round(lt, units = "hours")                     
```

## Lubridate 

Makes life a bit easier (sometimes) and represents time via different objects. Important concepts are:

+ instants
+ intervals
+ durations
+ periods


```
ymd_hms("2012-12-31 23:59:59")
## [1] "2012-12-31 23:59:59 UTC"

month(ldate) <- 8

# An _instant_ in time:
lubridate::now()

# Last day of the current month
lubridate::ceiling_date(now(), unit = "month") - lubridate::days(1)

# The time between two instants is an _interval_ 

# Interval constructed using _duration_ i.e. dyears
# dyears record the exact number of seconds in a time span and deals with
# leap years, daylight savings etc so you need to be careful in its use.
lubridate::ymd_hms(hotdate) - lubridate::dyears(1)

# versus
# Interval constructed using _period_ i.e. years
lubridate::ymd_hms(hotdate) - lubridate::years(1)


# Nice
now() %within% interval(ymd("2012-07-01"), ymd("2019-06-30"))
```

Painless?

