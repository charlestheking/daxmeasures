### formating using Calculation groups 
  #### ---------------formats result of a measure in seconds to HHMMSS------------------------------------

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

##
