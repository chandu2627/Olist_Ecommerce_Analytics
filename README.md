🛒 Olist E-commerce Analytics on Microsoft Fabric
Understanding sales performance and delivery reliability across Brazilian e-commerce

Microsoft Fabric · PySpark · Power BI · DAX · SQL
🧩 Business Problem
E-commerce teams need to understand what drives revenue and where delivery performance falls short. This project uses the public Olist Brazilian e-commerce dataset to answer two practical business questions:
- What drives sales? Which product categories and states generate the most revenue, and how does revenue change over time?
- How reliable is delivery? What proportion of delivered order items arrive late, and which states, categories, or periods have slower delivery?
🔍 Key Findings
Metric	Finding
Total revenue	R$13.59M
Total orders	98,666
Average order value	R$137.75
Late delivery rate	7.9% of delivered items
Average delivery time	About 12 days
Top revenue category	health_beauty — about R$1.26M
Highest late-delivery rate among states with 1,000+ orders	Ceará (CE) — 15.3%
Lowest late-delivery rate among states with 1,000+ orders	Paraná (PR) — 4.8%


Sales insights
- Monthly revenue peaked at approximately R$1.0M in November 2017, around the Black Friday period.
- Revenue remained around R$0.85M–R$1.0M per month through much of 2018.
- health_beauty led category revenue, followed by watches_gifts and bed_bath_table.
- São Paulo (SP) generated the most revenue, well ahead of Rio de Janeiro (RJ) and Minas Gerais (MG).
Delivery insights
- Approximately 7.9% of delivered order items arrived later than the estimated date.
- Among states with at least 1,000 orders, late-delivery rates ranged from 4.8% in PR to 15.3% in CE. Bahia (BA), Rio de Janeiro (RJ), and Espírito Santo (ES) also had comparatively high rates.
- Average delivery time rose to around 14–16 days between November 2017 and March 2018, then fell to about 7 days by August 2018. The latest months may be understated because orders that were still undelivered at the end of the dataset are excluded.
- Late-delivery rates varied only modestly by category; electronics had the highest rate at about 9.7%. office_furniture had a longer average delivery time of about 20 days, compared with roughly 12 days for most categories.
🛠️ Tools & Tech Stack
Layer	Tools / Technologies
Data source	Olist Brazilian E-commerce Public Dataset (Kaggle)
Storage	Microsoft Fabric Lakehouse, Delta tables
Ingestion and cleaning	Dataflow Gen2
Data transformation	PySpark notebook
Data modeling	Star schema, semantic model relationships
Analytics and reporting	Power BI, DAX
Query language	SQL


🏗️ Data Pipeline & Architecture
Layer	Fabric item	Purpose
Storage	Lakehouse lh_olist	Stores raw CSV files in Files/raw and curated Delta tables
Ingestion & cleaning	Dataflow Gen2 df_olist_orders	Loads five source files into staging tables, applies data types, removes duplicate orders, and adds delivery_days and is_late
Transformation	Notebook nb_olist_star_schema	Builds the star schema as Delta tables using PySpark
Semantic layer	Semantic model sm_olist_sales	Defines relationships and DAX measures
Reporting	Power BI report rpt_olist_sales	Presents sales overview and delivery performance


🧱 Data Model — Star Schema
- fact_sales — 112,650 rows; one row per order item. Includes order, product, seller, customer, order date, status, price, freight, delivery days, and late flag.
- dim_customers — 99,441 rows; includes customer city and state.
- dim_products — 32,951 rows; includes English product category and product weight. Missing categories are labeled unknown.
- dim_date — 730 days; includes year, quarter, month, and weekday.
Each dimension table is on the one side of its relationship with fact_sales on the many side. Relationships use single-direction filtering.
📈 Dashboard Pages
Page 1 — Sales Overview
- Total revenue, total orders, and average order value
- Monthly revenue trend
- Revenue by product category
- Revenue by customer state
Page 2 — Delivery Performance
- Late-delivery percentage
- Average delivery days
- Delivery performance by state and product category
- Delivery-time trends over time
⚙️ DAX Measures Used
Total Revenue = SUM(fact_sales[price])

Total Orders = DISTINCTCOUNT(fact_sales[order_id])

Avg Order Value = DIVIDE([Total Revenue], [Total Orders])

Total Freight = SUM(fact_sales[freight_value])

Avg Delivery Days = AVERAGE(fact_sales[delivery_days])

Late Delivery % =
DIVIDE(
    CALCULATE(COUNTROWS(fact_sales), fact_sales[is_late] = 1),
    CALCULATE(COUNTROWS(fact_sales), NOT(ISBLANK(fact_sales[is_late])))
)
📁 Project Structure
The project uses the following main artifacts and report screenshots:
Olist-Ecommerce-Analytics/
├── nb_olist_star_schema.ipynb
├── screenshots/
│   ├── 01_workspace.png
│   ├── 02_lakehouse_tables.png
│   ├── 03_dataflow_steps.png
│   ├── 05_model_view.png
│   ├── 06_report_sales_overview.png
│   └── 07_report_delivery_performance.png
└── rpt_olist_sales.pdf
The source README identifies these notebook, screenshot, and PDF paths. The raw dataset is not included in the repository.
🚀 How to Run
1. Download the Olist Brazilian E-commerce Public Dataset on Kaggle.
2. In Microsoft Fabric, create a workspace and a Lakehouse named lh_olist.
3. Upload the five required CSV files — orders, customers, order items, products, and category translation — to Files/raw.
4. Create a Dataflow Gen2 named df_olist_orders to load the source files into staging tables and apply the documented cleaning steps.
5. Attach the Lakehouse to nb_olist_star_schema.ipynb and run the notebook to build the star schema.
6. Create the semantic model using fact_sales and the three dimension tables. Configure relationships and add the DAX measures above.
7. Build the two report pages: Sales Overview and Delivery Performance.
You can also preview both report pages in [`rpt_olist_sales.pdf`](rpt_olist_sales.pdf), without opening Power BI.
📝 Data Notes & Assumptions
- Revenue is calculated from item price and excludes freight. Currency is Brazilian reais (R$).
- The dataset contains little data for 2016. The last order is dated 3 September 2018, so trend charts focus on January 2017 through August 2018.
- The category chart includes categories with more than 500 orders; the state chart includes states with more than 1,000 orders. Smaller samples are excluded because their percentages may be less reliable.
- 610 products have no category and are labeled unknown.
- Delivery metrics are calculated per order item, which is the grain of fact_sales, and only for items with a delivery date. Orders that remained undelivered when the dataset ended are excluded, so delivery times in the latest months may be understated.
🖼️ Screenshots
Microsoft Fabric workspace

Lakehouse tables — staging and star schema

Dataflow Gen2 cleaning steps

Semantic model relationships

Power BI — Sales Overview

Power BI — Delivery Performance

🔮 Next Steps
- Add the reviews table to investigate how late deliveries relate to review scores.
- Add payments and sellers as additional dimensions.
- Schedule the Dataflow Gen2 and notebook using a Fabric pipeline.
📚 Dataset
Brazilian E-Commerce Public Dataset by Olist — Kaggle
Project: Olist E-commerce Analytics on Microsoft Fabric
Focus: E-commerce Sales Analytics · Delivery Performance · Data Engineering · Business Intelligence
