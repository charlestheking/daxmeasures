## VALUES  -------------------------------
#----------------------------------------------------------------------------------**VALUES-----------------------do not use in variables--**-----------------

3 months rolling average=
    var startdate = Lastdate(date[date])
    get a list of months from date table do not use values in variable
    return
calculate (
    averagex(
        values(date(month),totalsales),
        datesinperiod(date([date],startdate, -3,MONTH))

## ADDCOLUMNS     -----------------------------
#--------------------------------------------------------------------------------ADDCOLUMNS--Add columns 
**-- does not preserve data lineage----**
ADDCOLUMNS
---table
--column to add 
Eg
ADDColumns (
'Customer',
"total Transact", calculate(countrows(sales)), ----------------------adds total count of customer transactions
)

**--Generate and row function-----------------generate a table----------**
var custtbl = values(customer[Names])
return
    GENERATE 
    custtble,
var totaltrans = calculate(countrows(sales)
var futtrans =totaltans+10
return 
ROW (
"total transact", totaltrans,
"future trans", futtrans))
------------------example
EVALUATE
	CALCULATETABLE(
		ADDCOLUMNS(
			SUMMARIZE(
				FactInternetSales,
				DimShipDate[Year],
				DimSalesTerritory[SalesTerritoryCountry]
			),
			"Total Quantity", [Total Quantity],
			"Minimum Price", [Min Price]
		),
		DimSalesTerritory[SalesTerritoryCountry] = "United States",
		DimProduct[Color] IN {
			"Red",
			"Blue"
		}
	)
	ORDER BY DimShipDate[Year]

## SELECTCOLUMNS---------------select columns iterator-------------maintains data lineage



SLCTcol =
SELECTCOLUMNs
    product,
    "Product name", product[product name],
    "Product color", product[color]
    "# of Sales", calculate(countrows(sales)),
    "sales", [Measure]
----filters 
    'customer' [Marital] ="s"

 SELECTCOLUMNS
  ---allows additional filters
Calculatetable(
    selectcolumns(
        product
    "Product Name", product[name],
    "Product color", product[Color]
    "Customer Name", RELATED(Customer[FirstName]),
    "# Of Sales ", calculate(countrows(sales)),
----------additional filters-------------------
sales, [totalsalses]
customer [maritalstatus]="s"


## SUMMERIZE-----------addcol + summerize ------------better use summerizecolumns only for tables 
sum
 SUMMERIZE(
sales, 
product[color],
"Sales" total sales /// not recommmended use summerizecolumns   

------------------------------------------------------------------------------------------------------------
EVALUATE
	CALCULATETABLE(
		SUMMARIZE(
			FactInternetSales,
			DimShipDate[Year],
			DimSalesTerritory[SalesTerritoryCountry],
			"Total Quantity", [Total Quantity],
			"Minimum Price", [Min Price]
		),
		DimSalesTerritory[SalesTerritoryCountry] IN {
			"United States"
		},
		DimProduct[Color] IN {
			"Red",
			"Blue"
		}
	)
	ORDER BY DimShipDate[Year]


## SUMMERIZECOLUMNS--------------------< is only for queries and not to be used in a MEASURE---------------------
--ability to group by --Rollup totals---
--ability to add columns--like addcol
--ability to add filters--like calculate table
--automatically remove row with blanks
--summerizecolumns will not work if the cntxt translation has occoured---------------------------**do not use in a measure use addcol + summerize**------------------------------------------------------<<<<<<<<<<<<
    SUMMERIZECOLUMNS 
    product[class],
    'date'[year],
"Amount", IGNORE([Totalsales])---this returns zero values 
-----------------example
EVALUATE
SUMMARIZECOLUMNS(
	DimShipDate[Year],
	DimSalesTerritory[SalesTerritoryCountry],
	FILTER(ALL(DimSalesTerritory[SalesTerritoryCountry]),DimSalesTerritory[SalesTerritoryCountry]="United States"),
	FILTER(ALL(DimProduct[Color]),DimProduct[Color] IN {"Red","Blue"}),
	"Total Quantity", [Total Quantity],
	"Minimum Price",[Min Price]
)
ORDER by DimShipDate[Year]



## GROUPCROSSAPLY-------------------
EVALUATE
GROUPCROSSAPPLY(
	DimShipDate[Year],
	DimSalesTerritory[SalesTerritoryCountry],
	FILTER(ALL(DimSalesTerritory[SalesTerritoryCountry]),DimSalesTerritory[SalesTerritoryCountry]="United States"),
	FILTER(ALL(DimProduct[Color]),DimProduct[Color] IN {"Red","Blue"}),
	VALUES(DimShipDate[Year]),
	"Total Quantity", [Total Quantity],
	"Minimum Price",[Min Price]
)
ORDER by DimShipDate[Year]


## GROUPBY---------------------------------------------
    EVALUATE
GROUPBY(
	FILTER(
		FactInternetSales,
		RELATED(DimSalesTerritory[SalesTerritoryCountry])="United States"
		&& RELATED(DimProduct[Color]) in {"Red","Blue"}
		),
		DimShipDate[Year],
		DimSalesTerritory[SalesTerritoryCountry],
		"Total Quantity", SUMX(CURRENTGROUP(),[OrderQuantity]),
		"Minimum Price", MINX(CURRENTGROUP(),FactInternetSales[UnitPrice])
)
ORDER by DimShipDate[Year]
		--group by does not support explicit measures 




    


