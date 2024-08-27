#------REMOVE EMPTY DIM VALUES__visual level filter to remove empty dim values 
  ## create a measure
    ###  INT(NOT(ISBLANK(TABLE)
     then use this measure to filter other tables

#----------------USING A MEASURE AS SLICER-----------------------------------------
1. Create a temp table using addcol + Summerize
2. use the column with measure on slicer
