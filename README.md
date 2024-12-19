# My Go-to Data Analysis Expressions (DAX)

1. ### DAX for creating a date table
``` dax
Calendar = 
VAR StartDate = MIN('Table_name'[Date])
VAR EndDate = MAX('Table_name'[Date])
VAR DateTable =
ADDCOLUMNS(
    CALENDAR(StartDate, EndDate),
"Year", YEAR([Date]),
"Quarter (Q)", FORMAT([Date], "\QQ"),
"Quarter", QUARTER([Date]),
"MonthName", FORMAT([Date], "mmm"),
"Month", MONTH([Date]),
"DayName", FORMAT([Date], "dddd"),
"Day of Week", WEEKDAY([Date])
)
RETURN
   DateTable
```

``` dax
Calendar = ADDCOLUMNS(
    CALENDAR(MIN('Spotify Dataset'[Date]), MAX('Spotify Dataset'[Date])),
"Year", YEAR([Date]),
"Quarter (Q)", FORMAT([Date], "\QQ"),
"Quarter", QUARTER([Date]),
"MonthName", FORMAT([Date], "mmm"),
"Month", MONTH([Date]),
"DayName", FORMAT([Date], "dddd"),
"Day of Week", WEEKDAY([Date])
)
```

2. ###
