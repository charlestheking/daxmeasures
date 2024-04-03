
/*
Creates dates with dirrefrent languages need to modify to fit situation

*/

let
    Today = Date.From( DateTime.LocalNow() ),
    StartDate = #date( 2021, 1, 1 ),
    EndDate = Date.EndOfYear( Today ),
    TranslateColumnNames = Table.FromRows(Json.Document(Binary.Decompress(Binary.FromText("bZLRboMwDEV/xeJ18BdUU1exauKhmqo+eMK01UqQaNjUv58dEidh5QV870lwbnw8FjVaKkp5zUNxKhcBtsbSmaZgwCtZvDn7k1DkN+RXqKGmHueb9Tq0Fk3HH91C8FM1jZj8yJeozWjspfK7NcgLKt3TefA+D1+krpQDZT4OFF3EYeVBexknmxGwE2WFxeYTMD8CV5OFsQe3jMlF+EEDy5oY34arBNxcTUdPwRofd7gaJWs8k3GCgA7Ztns4EH2bkEQQ0jBE8zHKp4aoPcuCrGW3Q2zkY2aDppj37pcFxFv6Fw8lrt5+FT35eZUBfMpwUXzAeE1B10sKbrwiQUKSsTnhYpxJhx4P8eT0OjQPu+H9B7sZXsH7vr9T6NMX4rmN1UyrOGVqLz0nfoheCY0+gVyTSrjmEnd7d4cjHpkyLRKvw0fwJi7Ocbbg5cl8eVETOP0B", BinaryEncoding.Base64), Compression.Deflate)), let _t = ((type nullable text) meta [Serialized.Text = true]) in type table [English = _t, Nederlands = _t]),
    LanguageParameters = Table.FromRows(Json.Document(Binary.Decompress(Binary.FromText("i45Wcs1Lz8kszlDSUUrN03X1A9LOpUVFqXklSrE60Up+qSmpRTmJeSnFQIm8HF0/HyDtUZqZkpmeqhQbCwA=", BinaryEncoding.Base64), Compression.Deflate)), let _t = ((type nullable text) meta [Serialized.Text = true]) in type table [Language = _t, Culture = _t, DefaultValue = _t]),
    CultureCode = LanguageParameters[Culture]  { List.PositionOf( LanguageParameters[Language] , CalendarLanguage ) },
    Defaultvalue = LanguageParameters[DefaultValue] { List.PositionOf( LanguageParameters[Language] , CalendarLanguage ) },
    #"List of Dates" = List.Dates( StartDate, Duration.Days( EndDate - StartDate ) +1, #duration( 1, 0, 0, 0 ) ),
    #"Converted to Table" = Table.FromList( #"List of Dates", Splitter.SplitByNothing(), type table[Date = Date.Type] ),
    #"Insert Date Integer" = Table.AddColumn(#"Converted to Table", "Date Integer", each Number.From( Date.ToText( [Date], "yyyyMMdd" ) ) , Int64.Type ) ,
    #"Insert Year" = Table.AddColumn(#"Insert Date Integer", "Year", each Date.Year([Date]), Int64.Type),
    // Creates a dynamic year value called 'Current' that moves with the current date. Put this value in a slicer and it automatically switches to the Current period.
    #"Add Year Default" = Table.AddColumn(#"Insert Year", "Year Default", each if Date.Year( Today ) = [Year] then Defaultvalue else Text.From( [Year] ), type text),
    #"Insert YYYY-MM" = Table.AddColumn(#"Add Year Default", "YYYY-MM", each Date.ToText( [Date], "yyyy-MM"), type text),
    #"Insert Month-Year" = Table.AddColumn(#"Insert YYYY-MM", "Month-Year", each Text.Proper( Date.ToText( [Date], "MMM yyyy", CultureCode ) ), type text),
    #"Insert Month Number" = Table.AddColumn(#"Insert Month-Year", "Month Number", each Date.Month([Date]), Int64.Type),
    #"Insert Month Name" = Table.AddColumn(#"Insert Month Number", "Month Name", each Text.Proper( Date.MonthName([Date], CultureCode ) ), type text),
    #"Insert Month Name Short" = Table.AddColumn(#"Insert Month Name", "Month Name Short", each Text.Proper( Date.ToText( [Date] , "MMM", CultureCode ) ), type text),
    // Creates a dynamic year value called 'Current' that moves with the current date. Put this value in a slicer and it automatically switches to the current period.
    #"Add Month Name Default" = Table.AddColumn(#"Insert Month Name Short", "Month Name Default", each if Date.Month( Today ) = [Month Number] then Defaultvalue else [Month Name], type text ),
    #"Insert Start of Month" = Table.AddColumn(#"Add Month Name Default", "Start of Month", each Date.StartOfMonth([Date]), type date),
    #"Inserted End of Month" = Table.AddColumn(#"Insert Start of Month", "End of Month", each Date.EndOfMonth( [Date] ), type date),
    #"Inserted Days in Month" = Table.AddColumn(#"Inserted End of Month", "Days in Month", each Date.DaysInMonth([Date]), Int64.Type),
    #"Add ISO Week" = Table.AddColumn(#"Inserted Days in Month", "ISO Weeknumber", each let
CurrentThursday = Date.AddDays([Date], 3 - Date.DayOfWeek([Date], Day.Monday ) ),
YearCurrThursday = Date.Year( CurrentThursday ),
FirstThursdayOfYear = Date.AddDays(#date( YearCurrThursday,1,7),- Date.DayOfWeek(#date(YearCurrThursday,1,1), Day.Friday) ),
ISO_Week = Duration.Days( CurrentThursday - FirstThursdayOfYear) / 7 + 1
in ISO_Week, Int64.Type ),
    #"Added Custom" = Table.AddColumn(#"Add ISO Week", "ISO W + Weeknumber", each "W" & Text.From( [ISO Weeknumber] ), type text ),
    #"Add ISO Year" = Table.AddColumn(#"Added Custom", "ISO Year", each let
CurrentThursday = Date.AddDays([Date], 3 - Date.DayOfWeek([Date], Day.Monday ) ),
YearCurrThursday = Date.Year( CurrentThursday )
in YearCurrThursday, Int64.Type ),
    #"Insert Start of Week" = Table.AddColumn(#"Add ISO Year", "Start of Week", each Date.StartOfWeek([Date], Day.Monday ), type date),
    #"Insert Quarter Number" = Table.AddColumn(#"Insert Start of Week", "Quarter Number", each Date.QuarterOfYear([Date]), Int64.Type),
    #"Added Quarter" = Table.AddColumn(#"Insert Quarter Number", "Quarter", each "Q" & Text.From( Date.QuarterOfYear([Date]) ), type text ),
    #"Add Year-Quarter" = Table.AddColumn(#"Added Quarter", "Year-Quarter", each Text.From( Date.Year([Date]) ) & "-Q" & Text.From( Date.QuarterOfYear([Date]) ), type text ),
    #"Insert Day Name" = Table.AddColumn(#"Add Year-Quarter", "Day Name", each Text.Proper( Date.DayOfWeekName([Date], CultureCode ) ), type text),
    #"Insert Day Name Short" = Table.AddColumn( #"Insert Day Name", "Day Name Short", each Text.Proper( Date.ToText( [Date], "ddd", CultureCode ) ), type text),
    #"Insert Day of Month Number" = Table.AddColumn(#"Insert Day Name Short", "Day of Month Number", each Date.Day([Date]), Int64.Type),
    #"Insert Day of Week" = Table.AddColumn(#"Insert Day of Month Number", "Day of Week Number", each Date.DayOfWeek([Date]), Int64.Type),
    #"Insert Day of Year" = Table.AddColumn(#"Insert Day of Week", "Day of Year Number", each Date.DayOfYear([Date]), Int64.Type),
    #"Add Day Offset" = Table.AddColumn(#"Insert Day of Year", "Day Offset", each Number.From( Date.From( Today ) - [Date] ) , Int64.Type ),
    #"Add Week Offset" = Table.AddColumn(#"Add Day Offset", "Week Offset", each ( Number.From( Date.StartOfWeek( [Date], Day.Monday) )- Number.From( Date.StartOfWeek( Today, Day.Monday ) ) ) / 7, Int64.Type ),
    #"Add Month Offset" = Table.AddColumn(#"Add Week Offset", "Month Offset", each ([Year] - Date.Year( Today ) ) *12 + ( [Month Number] - Date.Month( Today ) ), Int64.Type ),
    #"Add Quarter Offset" = Table.AddColumn(#"Add Month Offset", "Quarter Offset", each ( [Year] - Date.Year(Today) ) * 4 + Date.QuarterOfYear( [Date] ) - Date.QuarterOfYear( Today ), Int64.Type ),
    #"Add Year Offset" = Table.AddColumn(#"Add Quarter Offset", "Year Offset", each [Year] - Date.Year(Today), Int64.Type ),
    #"Insert Is Weekend" = Table.AddColumn(#"Add Year Offset", "Is Weekend", each if Date.DayOfWeek([Date], Day.Monday ) >= 5 then 1 else 0, Int64.Type ),
    #"Insert Is Weekday" = Table.AddColumn(#"Insert Is Weekend", "Is Weekday", each if Date.DayOfWeek([Date], Day.Monday ) < 5  then 1 else 0, Int64.Type ),
    #"Rename Columns to Language" = Table.RenameColumns(#"Insert Is Weekday", List.Zip( { List.Buffer( TranslateColumnNames[English] ), List.Buffer( Table.Column( TranslateColumnNames, CalendarLanguage ) ) } ), MissingField.Ignore )
in
    #"Rename Columns to Language"