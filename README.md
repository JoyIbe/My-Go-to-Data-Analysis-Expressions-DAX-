# My Go-to Data Analysis Expressions (DAX)
My go-to expressions for building dashboard features in Power BI like custom visuals, calculated measures for seamless interpretation of data.

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
    CALENDAR(MIN('Table_name'[Date]), MAX('Table_name'[Date])),
"Year", YEAR([Date]),
"Quarter (Q)", FORMAT([Date], "\QQ"),
"Quarter", QUARTER([Date]),
"MonthName", FORMAT([Date], "mmm"),
"Month", MONTH([Date]),
"DayName", FORMAT([Date], "dddd"),
"Day of Week", WEEKDAY([Date])
)
```

2. ### Custom KPI for Time Series Analysis
   
``` dax
YoY = 
    VAR
        _difference = SUM('Table_name'[sales])- [Previous Year]
    VAR
        _direction = 
        SWITCH(
            TRUE(),
            _difference > 0, "▲",
            _difference < 0, "▼",
            "●"
        )
    VAR
        _percentChange = 
            _difference / [Previous Year]
    RETURN 
        _direction & " " & FORMAT(_percentChange, "##.##%") & " " & "vs PY" [OR ADD & " " & FORMAT([Previous Year]/1000000, "#,0.00") & "M"] -- Value of py
``` 

``` dax
YoY = 
    VAR
        _difference = SUM('Table_name'[sales])- [Previous Year]
    VAR
        _direction = 
        SWITCH(
            TRUE(),
            _difference > 0, "▲",
            _difference < 0, "▼",
            "●"
        )
    VAR
        _percentChange = 
            (CALCULATE(SUM('Table_name'[sales]), YEAR('Calendar'[Date])=2015) -  CALCULATE(SUM('Table_name'[sales]), YEAR('Calendar'[Date])=2014))/CALCULATE(SUM('Table_name'[sales]), YEAR('Calendar'[Date])=2014)
    RETURN 
        _direction & " " & FORMAT(_percentChange, "##.##%") & " " & "vs PY"
```

3. ### Custom KPI conditional formatting
   
``` dax
Eg Rev:
RevYoYColor = 
    VAR
        _difference = [Total Rev]- [PY Rev Org]
    RETURN 
        SWITCH(
            TRUE(),
            _difference > 0, "#348939",
            _difference < 0, "#9B1D1E",
            "#000000")
```
4. ### Dax for creating Agegroup Column

``` dax
Age Group = 
SWITCH(
    TRUE(),
    'Walmart Table'[customer_age] = "NULL" , "Unknown",
    VALUE('Table_name'[customer_age]) >= 40 && VALUE('Table_name'[customer_age]) <= 49, "40-49",
    VALUE('Table_name'[customer_age]) >= 50 && VALUE('Table_name'[customer_age]) <= 59, "50-59",
    VALUE('Table_name'[customer_age]) >= 60 && VALUE('Table_name'[customer_age]) <= 69, "60-69",
    VALUE('Table_name'[customer_age]) >= 70 &&  VALUE('Table_name'[customer_age]) <= 79, "70-79",
    VALUE('Table_name'[customer_age]) >= 80, "80+",
    "Unknown"
)
```


5. ### Searching for total and percentage of a particular variable
   
 ``` dax
Product x = 
FORMAT(
    CALCULATE(
    SUM('Table_name'[Revenue generated]),
    'Table_name'[Product type] = "x"
), 
"0.00" 
)

Percentage of x = 
VAR _TotalRevenue = SUM('Table_name'[Revenue generated]) 
RETURN FORMAT( DIVIDE('Table_name'[x], _TotalRevenue, 0), "0%"
 )
```

6. ###  Previous Year

``` dax
    CALCULATE( SUM('Table_name'[sales]),
    PREVIOUSYEAR('Calendar'[Date])
    )

ii) 
_prev =
    IF(
        NOT(ISBLANK([Total Rev])),
        CALCULATE(
            [Total Rev],
            PREVIOUSYEAR('Calendar'[Date])
        )
    )
RETURN
    DIVIDE(([Total Rev] - _prev), _prev)


iii) Previous Year = 
CALCULATE(
    [Total Sales],
    DATEADD('Date'[Date], -1, YEAR)
)

iv) xPrevious Year = 
   (CALCULATE('Table_name'[x], YEAR('Calendar'[Date])=2015) -  CALCULATE(('Table_name'[x]), YEAR('Calendar'[Date])=2014))/ CALCULATE('Table_name'[x, YEAR('Calendar'[Date])=2014)

v) xPrevious Year using VAR = 
VAR ThisYear = CALCULATE('Table_name'[x], YEAR('Calendar'[Date]) = 2015)
VAR PreviousYear = CALCULATE('Table_name'[x], YEAR('Calendar'[Date]) = 2014)
RETURN 
FORMAT(
    DIVIDE(This - PreviousYear, PreviousYear, 0), 
    "0.0%" & " " & "vs PY"
)
```
