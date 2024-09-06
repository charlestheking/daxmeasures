### formating using Calculation groups 
  #### ---------------formats result of a measure in seconds to HHMMSS------------------------------------
code 
{
Duration Format = 
IF (
    ISBLANK ( SELECTEDMEASURE() ),
    BLANK (),
    IF (
        CONTAINSSTRING ( SELECTEDMEASURENAME(), "Dur" ),
        IF (
            SELECTEDMEASURE() < 0,
            "-" & FORMAT (
                INT ( CEILING ( ABS ( SELECTEDMEASURE() ), 1 ) / 3600 ), "00"
            ) & ":" &
            FORMAT (
                INT ( MOD ( CEILING ( ABS ( SELECTEDMEASURE() ), 1 ), 3600 ) / 60 ), "00"
            ) & ":" &
            FORMAT (
                MOD ( CEILING ( ABS ( SELECTEDMEASURE() ), 1 ), 60 ), "00"
            ),
            FORMAT (
                INT ( CEILING ( ABS ( SELECTEDMEASURE() ), 1 ) / 3600 ), "00"
            ) & ":" &
            FORMAT (
                INT ( MOD ( CEILING ( ABS ( SELECTEDMEASURE() ), 1 ), 3600 ) / 60 ), "00"
            ) & ":" &
            FORMAT (
                MOD ( CEILING ( ABS ( SELECTEDMEASURE() ), 1 ), 60 ), "00"
            )
        ),
        SELECTEDMEASURE()
    )
)
}
/code
###  --- Cascading filters --- 
    INT(ISFILTERED(slicer[COL]) ---returns boolean use to filter the slicer

    
#### --- add extracolumn to check if date is today-----
    DAX =>   IF(TD_DIM_DATE[D_DATE]= TODAY(), " TODAY ",TD_DIM_DATE[D_DATE]&"")
    PQ  =>   Date.From([D_MNRG]) <= Date.From(DateTime.LocalNow())
