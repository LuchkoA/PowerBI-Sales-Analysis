# DAX Формули (Power BI)

Цей файл містить мої основні DAX-міри, згруповані за темами.  

## Зміст
- [Фінансові KPI](#фінансові-kpi)
- [Клієнтські метрики](#клієнтські-метрики)
- [ABC-analysis](#ABC-analysis)
- [XYZ-analysis](#XYZ-analysis)

---

## Фінансові KPI

### Income
Коротко: сума виручки

Income = CALCULATE(SUM(Sales[Сума реалізації]))

### Profit
Коротко: загальний прибуток

Profit = SUM(Sales[Сума реалізації])-SUM(Sales[Собівартість])


### Profit Ratio
Коротко: коефіцієнт прибутковості

Profit Ratio = DIVIDE([Profit],[Income])

---

## Клієнтські метрики

## Average_Check
Коротко: середній чек

Average_Check = DIVIDE([Income], SUM(Sales[Кількість]))

## IncomOneClient
Коротко: виручка на одного клієнта

IncomOneClient = DIVIDE([Income],[ActiveCustomers])

## ActiveCustomers
Коротко: загальна кількість клієнтів

ActiveCustomers = DISTINCTCOUNT(Sales[Клієнт])

## OrderOneClient
Коротко: кількість замовлень на одного клієнта

OrderOneClient = DIVIDE(COUNT(Sales[Номер документа]),[ActiveCustomers])

## NewClients
Коротко: кількість нових клієнтів

NewClients = CALCULATE([ActiveCustomers], Sales, Sales[orderIDSerial] = 1)

## RegularCustomers
Коротко: кількість постійних клієнтів

RegularCustomers = CALCULATE([ActiveCustomers],Sales[orderIDSerial]>1)

## NumberOrdersNewCustomers
Коротко: кількість замовлень, які зробили нові клієнти в обраному періоді

NumberOrdersNewCustomers = CALCULATE(COUNT(Sales[Номер документа]), Sales[orderIDSerial] = 1)

## NumberOrdersNewCustomers
Коротко: кількість замовлень, які зробили постійні клієнти в обраному періоді

NumberOrdersRegularCustomers = CALCULATE(COUNT(Sales[Номер документа]), Sales[orderIDSerial] > 1)

# ABC-analysis

## ABC_Class
Коротко: визначення товарів, які приносять найбільше виручки

ABC_Class = 
IF(HASONEVALUE('Product'[Товар]),

VAR SalesByProduct = ADDCOLUMNS(ALLSELECTED('Product'), "@ProdSales", [Income])

VAR Allsales = CALCULATE([Income], ALLSELECTED('Product'))

VAR CurrentSalesA = [Income]

VAR CumSales = FILTER(SalesByProduct, [@ProdSales]>=CurrentSalesA)

VAR CumSalesA = SUMX(CumSales,[@ProdSales])

VAR CurrentCumP = DIVIDE(CumSalesA, Allsales)

VAR Result = SWITCH(TRUE(),
    ISBLANK([Income]), BLANK(),
    CurrentCumP<=0.8, "A",
    CurrentCumP<=0.95, "B", "C")

RETURN Result)

# ABC-analysis

## XYZ_Cat
Коротко: категорії стабільності продажів по місяцях

XYZ_Cat = 
VAR SummaryTable =
    SUMMARIZE(
        'Sales',
        'Sales'[Товар],
        'Calendar'[YearMonth],
        "TotalSales", SUM(Sales[Сума реалізації]))

VAR AvgSales =
    AVERAGEX(
        FILTER(SummaryTable, [TotalSales] > 0),
        [TotalSales])

VAR StdDevSales =
    STDEVX.P(
        FILTER(SummaryTable, [TotalSales] > 0),
        [TotalSales])

VAR CV = DIVIDE(StdDevSales, AvgSales)

RETURN
    IF(ISBLANK(CV), BLANK(),
        SWITCH(
            TRUE(),
            CV <= 0.25, "X",
            CV <= 0.5, "Y",
            "Z"))
