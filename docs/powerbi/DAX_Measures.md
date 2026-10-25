# Power BI DAX Measures

This document contains the DAX measures implemented in the Olist E-commerce Analytics project using Microsoft Fabric and Power BI.

## 1. Total Revenue

**Purpose:** Calculates total revenue from the sales price column.

```dax
Total Revenue = SUM(fact_sales[price])
```

## 2. Total Orders

**Purpose:** Counts distinct order IDs.

```dax
Total Orders = DISTINCTCOUNT(fact_sales[order_id])
```

## 3. Average Order Value

**Purpose:** Calculates average revenue per distinct order.

```dax
Avg Order Value = DIVIDE([Total Revenue], [Total Orders])
```

## 4. Total Freight

**Purpose:** Calculates the total freight value.

```dax
Total Freight = SUM(fact_sales[freight_value])
```

## 5. Average Delivery Days

**Purpose:** Calculates the average delivery days.

```dax
Avg Delivery Days = AVERAGE(fact_sales[delivery_days])
```

## 6. Late Delivery Percentage

**Purpose:** Calculates the percentage of rows with a late-delivery flag of 1, among rows with a non-blank late-delivery flag.

```dax
Late Delivery % =
DIVIDE(
    CALCULATE(COUNTROWS(fact_sales), fact_sales[is_late] = 1),
    CALCULATE(COUNTROWS(fact_sales), NOT(ISBLANK(fact_sales[is_late])))
)
```

## Notes
- These measures use the `fact_sales` table.
- `DIVIDE` handles a zero or blank denominator safely.
- Format `Late Delivery %` as **Percentage** in Power BI.
- Format revenue and freight measures as currency if appropriate for the report.


Only include measures that have been implemented and validated in the Power BI report.
