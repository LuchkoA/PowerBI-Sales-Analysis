# DAX Формули (Power BI)

Цей файл містить мої основні DAX-міри, згруповані за темами.  

## Зміст
- [Фінансові KPI](#фінансові-kpi)
- [Аналіз часу](#аналіз-часу)
- [Клієнтські метрики](#клієнтські-метрики)
- [Інші корисні міри](#інші-корисні-міри)

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

## OrderOneClient
Коротко: кількість замовлень на одного клієнта

OrderOneClient = DIVIDE(COUNT(Sales[Номер документа]),[ActiveCustomers])


