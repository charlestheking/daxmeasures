## current local time in PQ
let
  UTC_DateTimeZone = DateTimeZone.UtcNow(), 
  UTC_Date         = Date.From(UTC_DateTimeZone), 
  StartSummerTime  = Date.StartOfWeek(#date(Date.Year(UTC_Date), 3, 31), Day.Sunday), 
  StartWinterTime  = Date.StartOfWeek(#date(Date.Year(UTC_Date), 10, 31), Day.Sunday), 
  UTC_Offset       = if UTC_Date >= StartSummerTime and UTC_Date < StartWinterTime then 2 else 1, 
  CET_Timezone     = DateTimeZone.SwitchZone(UTC_DateTimeZone, UTC_Offset)
in
  CET_Timezone


## convert duration in Seconds to HH:MM:SS in DAX

VAR Duration = [Change this value to the name of your column that contains your seconds value]
// There are 3,600 seconds in an hour
VAR Hours =
    INT ( Duration / 3600)
// There are 60 seconds in a minute
VAR Minutes =
    INT ( MOD( Duration - ( Hours * 3600 ),3600 ) / 60)
// Remaining seconds are the remainder of the seconds divided by 60 after subtracting out the hours 
VAR Seconds =
    ROUNDUP(MOD ( MOD( Duration - ( Hours * 3600 ),3600 ), 60 ),0) // We round up here to get a whole number
// These intermediate variables ensure that we have leading zero's concatenated onto single digits
// Hours with leading zeros
VAR H =
    IF ( LEN ( Hours ) = 1, 
        CONCATENATE ( "0", Hours ),
        CONCATENATE ( "", Hours )
      )
// Minutes with leading zeros
VAR M =
    IF (
        LEN ( Minutes ) = 1,
        CONCATENATE ( "0", Minutes ),
        CONCATENATE ( "", Minutes )
    )
// Seconds with leading zeros
VAR S =
    IF (
        LEN ( Seconds ) = 1,
        CONCATENATE ( "0", Seconds ),
        CONCATENATE ( "", Seconds )
    )
// Now return hours, minutes and seconds with leading zeros in the proper format "hh:mm:ss"
RETURN
    CONCATENATE (
        H,
        CONCATENATE ( ":", CONCATENATE ( M, CONCATENATE ( ":", S ) ) )
    )
---------------------------------- Other versions-----------
String Duration in Hours Minutes and Seconds = 
var vSeconds=[Duration in Seconds]
var vMinutes=int( vSeconds/60)
var vRemainingSeconds=MOD(vSeconds, 60)
var vHours=INT(vMinutes/60)
var vRemainingMinutes=MOD(vMinutes,60)
return
  vHours&" Hours & "&
  vRemainingMinutes&" Minutes & "& 
  vRemainingSeconds& " Seconds"
