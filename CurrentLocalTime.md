let
  UTC_DateTimeZone = DateTimeZone.UtcNow(), 
  UTC_Date         = Date.From(UTC_DateTimeZone), 
  StartSummerTime  = Date.StartOfWeek(#date(Date.Year(UTC_Date), 3, 31), Day.Sunday), 
  StartWinterTime  = Date.StartOfWeek(#date(Date.Year(UTC_Date), 10, 31), Day.Sunday), 
  UTC_Offset       = if UTC_Date >= StartSummerTime and UTC_Date < StartWinterTime then 2 else 1, 
  CET_Timezone     = DateTimeZone.SwitchZone(UTC_DateTimeZone, UTC_Offset)
in
  CET_Timezone
