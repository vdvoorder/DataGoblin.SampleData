# SampleData.Dates

Generates a comprehensive date dimension table from full years based on input date columns, with calendar and fiscal year support.

## Parameters

- `earliest_date_column` (ANYREF): Column containing earliest dates
- `latest_date_column` (ANYREF): Column containing latest dates

## Returns

Date records with the following columns:
- Date, DateKey, DateFormatted
- Calendar periods: Year, Quarter, Month, Week, Day, DayOfWeek, DayOfYear
- Names: MonthName, MonthNameShort, DayName, DayNameShort, QuarterName
- Sort columns: MonthSort, DaySort, QuarterSort
- Formatted strings: YearMonth, YearMonthName, YearQuarter, YearWeek
- Fiscal periods: FiscalYear, FiscalQuarter, FiscalMonth (July 1 start)
- Flags: IsWeekend, IsWeekday, IsMonthStart, IsMonthEnd, IsQuarterStart, IsQuarterEnd, IsYearStart, IsYearEnd
- Current period flags: IsToday, IsCurrentMonth, IsCurrentQuarter, IsCurrentYear, IsPast, IsFuture

## Usage Example

```dax
SAMPLEDATA.DATES(
    'Orders'[OrderDate],           // Earliest date column
    'Orders'[DeliveredDate]        // Latest date column
)
```

## Features

- Automatically determines date range from input columns and extends to full years
- Complete calendar hierarchy with proper sorting columns
- Fiscal year support (July 1 - June 30)
- Extensive date formatting options for reporting
- Boolean flags for weekend/weekday, period starts/ends
- Current period identification relative to TODAY()
- Optimized for time intelligence functions in DAX
- International week numbering (Monday = 1)