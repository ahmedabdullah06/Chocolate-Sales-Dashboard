# Chocolate Sales Dashboard (Power BI)

# Dataset credit: Saidamin Saidakhmadov

## Overview
This project is a Power BI dashboard built from a Chcoclate Sales on https://www.kaggle.com/datasets/saidaminsaidaxmadov/chocolate-sales, analyzing sales performance across salespeople, products, countries, and time. The goal was to practice building a normalized data model, writing DAX measures, and designing a dashboard that answers real business questions: who is selling well, which products drive revenue, and how performance trends over time.

## Data source & limitations
- Source: Kaggle chocolate sales dataset, originally containing company, salesperson, boxes shipped, date, product, and dollar amount.
- **Known limitation:** the dataset does not include Q4 transactions for any year covered. Year-over-year figures reflect Jan–Sep data only. This is called out directly on the dashboard so the gap isn't misread as a data error or a dip in performance.
- The original "Amount" column imported as text (formatted like "$5,320.00") and required cleanup in Power Query before it could be aggregated numerically.
- The source data only included salesperson names, not a unique identifier. A Salesperson ID (SID) was added via Excel `XLOOKUP` against a separate Salespersons lookup table, and the data was split into two related tables to avoid relying on name-matching (which is fragile and not a reliable key).

## Data model
- **Chocolate Sales** (fact table): transaction-level data — SID, Country, Product, Date, Amount, Boxes Shipped.
- **Salespersons** (dimension table): SID, First Name, Last Name.
- Relationships: Chocolate Sales `SID` → Salespersons `SID` (many-to-one), Chocolate Sales `Date` → Date table `Date` (many-to-one).

This mirrors a basic star schema: one fact table surrounded by dimension tables, rather than a single flat denormalized sheet.

## Key DAX measures

Total Revenue = SUM('Chocolate Sales'[Amount])

Base measure for total dollar revenue, used across cards, charts, and tables.

Total Boxes = SUM('Chocolate Sales'[Boxes Shipped])

Base measure for total volume shipped.


Avg Price per Box = DIVIDE(SUM('Chocolate Sales'[Amount]), SUM('Chocolate Sales'[Boxes Shipped]))

Average price per box. Uses `DIVIDE` instead of `/` to avoid divide-by-zero errors when a filter context has no boxes shipped. Because it's a measure (not a calculated column), it automatically recalculates correctly whether it's shown as a single total or broken out by product, country, or salesperson.

Dynamically ranks salespeople by revenue, used to drive the Top 5 salespeople table regardless of filter context.

## Dashboard pages
- **Page 1 (Summary):** KPI cards (Total Revenue, Boxes Shipped, Avg Price/Box), revenue by country, revenue by product, avg price per box by country, top 3 salespeople by revenue, revenue trend by year.
- **Page 2 (Detail):** Salesperson × Product revenue matrix, broken out to a separate page due to its size (13 salespeople × 16+ products).

## Tools used
Excel (Power Query, XLOOKUP), Power BI Desktop (DAX, data modeling).
