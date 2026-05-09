# E-Commerce Sales Analytics Dashboard | Power BI Project

![Power BI](https://img.shields.io/badge/Power%20BI-Desktop-F2C811?style=flat&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures%20%26%20Calculated%20Columns-0078D4?style=flat)
![Pages](https://img.shields.io/badge/Report%20Pages-3-informational?style=flat)
![Dataset](https://img.shields.io/badge/Dataset-Northwind%20Sample-blueviolet?style=flat)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat)
---
**🔗 Live Dashboard:** [View Interactive Report](https://app.powerbi.com/view?r=eyJrIjoiZmMxMTcwOTctNTc4Zi00Y2FiLTkyY2ItZDk0OThkODYwY2MzIiwidCI6ImY0ZTg5MTY4LTU4NGUtNGE3MS04MDAxLWE1OWRlMmQ1MThjYiJ9)
---
## 👤 Author

**Mahmoud Shamoun** | Data Analyst Specialist
---

## 📌 Overview

This project is an end-to-end **E-Commerce Sales Analytics Dashboard** built in **Microsoft Power BI Desktop**. It transforms raw transactional data - orders, products, customers, and categories - into a 3-page interactive report that empowers business stakeholders to monitor revenue performance, analyse customer behaviour, and identify top-performing products, cities, and markets.

**Business Problem Solved:**
E-commerce operations generate high volumes of order and customer data that are difficult to interpret manually. This dashboard consolidates all transactional activity into a unified analytical layer - surfacing revenue trends by time, geography, product, and customer segment - replacing fragmented spreadsheet reporting with a single, interactive decision-making tool.

---

## 🎯 Objectives

- Monitor headline KPIs - net sales, revenue, discounts, orders, and quantity - across filterable dimensions.
- Identify top-performing products, cities, and countries driving the majority of revenue.
- Analyse customer distribution across 21 countries and 69 cities, and understand which business roles generate the most orders.
- Track year-over-year and month-over-month sales and quantity growth trends.
- Rank top companies by purchase volume to support key account management decisions.
- Deliver a clean, navigable multi-page report with a consistent design theme and intuitive slicers.

---

## 📂 Dataset Description

**Source:** Northwind - a classic Microsoft sample OLTP database representing a simulated international food and specialty goods distributor.

**Date Range:** July 1996 – May 1998 &nbsp;|&nbsp; **Compatibility Level:** 1600

| Entity | Count |
|---|---|
| Orders (Line Items) | 2,155 |
| Unique Orders | 830 |
| Customers | 91 |
| Products | 77 |
| Categories | 8 |
| Countries | 21 |
| Cities | 69 |

### Tables & Key Fields

**`OrdersDetails`** *(Fact Table - grain: one order line)*

| Column | Type | Description |
|---|---|---|
| `OrderID` | Integer | Foreign key → Orders |
| `ProductID` | Integer | Foreign key → Product |
| `UnitPrice` | Decimal | Price at time of sale |
| `Quantity` | Integer | Units ordered |
| `Discount` | Float | Discount rate (0–1 scale) |
| `Net Sales` | Decimal | `UnitPrice × Quantity × (1 − Discount)` |
| `Rev Sales` | Decimal | `UnitPrice × Quantity` - pre-discount gross revenue |
| `Num Discount` | Decimal | Monetary discount amount (`Rev Sales − Net Sales`) |
| `Product.UnitPrice` | Decimal | Related list price from Product table |

**`Orders`** *(Dimension / Bridge - grain: one order header)*

| Column | Type | Description |
|---|---|---|
| `OrderID` | Integer | Primary key |
| `CustomerID` | Text | Foreign key → Customers |
| `EmployeeID` | Text | Handling employee |
| `OrderDate` | Date | Order placement date |
| `Freight` | Decimal | Shipping cost |
| `ShipCountry` / `ShipCity` | Text | Delivery destination |
| `Year` / `Month` / `Day` | Text | Extracted date parts for time filtering |
| `Month Number` | Integer | Calculated sort key for Month column |

**`Product`** *(Dimension)*

| Column | Type | Description |
|---|---|---|
| `ProductID` | Integer | Primary key |
| `ProductName` | Text | Product name |
| `CategoryID` | Integer | Foreign key → Categories |
| `UnitPrice` | Decimal | Current list price |
| `UnitsInStock` | Integer | Current inventory level |
| `UnitsOnOrder` | Integer | Pending replenishment units |
| `sales` | Decimal | Product-level total sales (calculated column) |

**`Customers`** *(Dimension)*

| Column | Type | Description |
|---|---|---|
| `CustomerID` | Text | Primary key |
| `CompanyName` | Text | Business / company name |
| `ContactName` / `ContactTitle` | Text | Primary contact and role |
| `City` / `Country` | Text | Customer location |

**`Categories`** *(Dimension)*

| Column | Type | Description |
|---|---|---|
| `CategoryID` | Integer | Primary key |
| `CategoryName` | Text | Category label (e.g., fresh, cheese, sweets) |
| `Description` | Text | Category description |

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| **Power BI Desktop** | Data modeling, report authoring, and dashboard design |
| **Power Query (M)** | Data ingestion, type enforcement, and transformation |
| **DAX** | Calculated columns, measures, and time intelligence |
| **Northwind Database** | Source dataset (Excel / CSV / SQL Server export) |

---

## 🔄 Data Cleaning & Preparation

All transformations were performed in **Power Query** prior to model load:

- **Data type enforcement** - Numeric fields (`UnitPrice`, `Quantity`, `Discount`, `Freight`) were explicitly typed as Decimal or Integer to prevent implicit coercion errors in DAX aggregations.
- **Date decomposition** - `OrderDate` was parsed into `Year`, `Month`, `Day`, and `Month Number` calculated columns in the `Orders` table to enable time-based slicing and correct chronological sorting in visuals.
- **Revenue metric columns** - `Net Sales`, `Rev Sales`, `Num Discount`, and `Product.UnitPrice` were added as calculated columns in `OrdersDetails` to centralise all revenue logic at the row grain.
- **Month sort fix** - A `Month Number` column (`MONTH(Orders[OrderDate])`) was created and assigned as the sort-by column for the `Month` text field, ensuring correct Jan–Dec ordering in all line and bar charts.
- **Referential integrity validation** - All foreign keys (`OrderID`, `ProductID`, `CustomerID`, `CategoryID`) were verified for clean join resolution before relationships were established in the model.
- **Null / blank handling** - Optional numeric fields such as `Discount` and `Freight` were confirmed to have no unexpected nulls to avoid blank-induced aggregation errors.

---

## 🗂️ Data Modeling

The model follows a **Snowflake Schema** - a central fact table (`OrdersDetails`) connected to dimension tables, with one extended chain through `Product → Categories`.

### Schema Diagram
---
<img width="1550" height="706" alt="Schema Diagram" src="https://github.com/user-attachments/assets/8943bf50-530e-4557-a050-b17c1f4c00da" />

---
### Relationships

| From Table | From Column | To Table | To Column | Cardinality | Filter Direction |
|---|---|---|---|---|---|
| `OrdersDetails` | `OrderID` | `Orders` | `OrderID` | Many → One | Single |
| `OrdersDetails` | `ProductID` | `Product` | `ProductID` | Many → One | Single |
| `Orders` | `CustomerID` | `Customers` | `CustomerID` | Many → One | Single |
| `Product` | `CategoryID` | `Categories` | `CategoryID` | Many → One | Single |

All relationships use **single-direction cross-filtering** (fact → dimension) for clean, performant aggregation behaviour.

---

## 📊 KPIs & Metrics

### DAX Measures

| Measure | Expression | Purpose |
|---|---|---|
| `Avg orders per Product` | `AVERAGE(OrdersDetails[ProductID])` | Average order frequency per product |
| `Avg Discount` | `AVERAGE(OrdersDetails[Num Discount])` | Average monetary discount value per line |

### Calculated Columns

| Column | Table | Logic |
|---|---|---|
| `Net Sales` | `OrdersDetails` | `UnitPrice × Quantity × (1 − Discount)` |
| `Rev Sales` | `OrdersDetails` | `UnitPrice × Quantity` |
| `Num Discount` | `OrdersDetails` | `Rev Sales − Net Sales` |
| `Product.UnitPrice` | `OrdersDetails` | `RELATED(Product[UnitPrice])` |
| `Month Number` | `Orders` | `MONTH(Orders[OrderDate])` |
| `sales` | `Product` | Product-level net sales rollup |

### Business KPIs (Actuals From Live Model)

| KPI | Value |
|---|---|
| **Net Sales** | $1.27M |
| **Revenue Sales (Gross)** | $1.35M |
| **Total Discounts Given** | $89K |
| **Total Orders (Line Items)** | 2,155 |
| **Total Quantity Sold** | 51K units |
| **Total Products** | 77 |
| **Average Discount** | 41.1 |
| **Total Customers** | 91 |
| **Countries Served** | 21 |
| **Cities Served** | 69 |

---

## 📋 Dashboard Pages & Features

The report contains **3 pages** with navigation buttons (top-right arrow) for switching between the Sales and Customer dashboards.

---

### Page 1 - 📈 Sales Dashboard

**KPI Cards:** Net Sales · Revenue Sales · Discounts · Orders · Quantity · Products · Avg Discounts

| Visual | Chart Type | Key Finding |
|---|---|---|
| Sales Performance by Months | Line Chart | Monthly revenue trend - March peak ($177K), April trough ($36K) |
| Sales by Cities | Horizontal Bar | Cunewalde ($110K) · Graz ($105K) · Boise ($104K) |
| Sales by Country | Horizontal Bar | USA ($126K) · Germany ($93K) · Austria ($56K) · Brazil ($52K) |
| Sales & Quantity By Year | Dual Line Chart | 1996: $208K / 1997: $441K / 1998: $617K with quantity overlay |
| Sales by Category | Horizontal Bar | Fresh ($268K) · Cheese ($235K) · Sweets ($167K) · Meats ($163K) |
| Top 5 Products | Column Chart | Côte de Blaye ($141K) · Thüringer Rostbratwurst ($80K) · Raclette Courdavault ($71K) |
| Avg Orders per Product | Scrollable Table | Product frequency ranking - Original Frankfurter grüne Soße leads at 77.00 |

**Slicers:** Year · Cities · Month

---

### Page 2 - 👥 Customer Dashboard

**KPI Cards:** Net Sales · Orders · Customers · Country · Cities

| Visual | Chart Type | Key Finding |
|---|---|---|
| Top 10 Companies By Sales | Column Chart | QUICK-Stop ($110K) · Ernst Handel ($105K) · Save-a-lot Markets ($104K) |
| Customer by Country | Area / Line Chart | Germany (120 orders) and Venezuela (122) are the highest-volume countries |
| Top 5 Cities By Sales | Donut Chart | Cunewalde ($110K) · Graz ($105K) · Boise ($104K) · London ($53K) |
| Name and Position Customer | Scrollable Table | Full directory of all 91 customers with `ContactName` and `ContactTitle` |
| Position We Deal by Orders | Horizontal Bar | Sales Representatives (420) · Owners (343) · Sales Managers (342) |

**Slicers:** Year · Cities · Month

---

### Page 3 - 💡 Insights

A dedicated narrative page presenting 8 highlighted business insights in plain language - designed for executive and non-technical audiences. Values are styled in blue for quick scanning alongside supporting context.

---

### Global Features (All Pages)

- **Cross-page navigation** - buttons in the top-right corner allow one-click switching between Sales and Customer dashboards.
- **Consistent slicers** - Year, Cities, and Month slicers are present on every data page, applying filters globally across all visuals on that page.
- **Cross-filtering** - selecting any chart element (bar, slice, point) instantly filters all other visuals on the same page.
- **Consistent design theme** - dark navy background with light blue (`#00B0F0`) accent colour, white typography, and uniform card and chart styling across all 3 pages.

---

## 💡 Key Insights

1. **1997 was the peak revenue year** - generating $617K in net sales, nearly 3× the 1996 baseline of $208K, with order volume doubling from 152 to 408 orders.

2. **March drives the highest monthly sales** at $177K. April drops sharply to $36K - the steepest month-over-month decline in the dataset - revealing a clear seasonal pattern that warrants further investigation.

3. **USA and Germany are the two dominant markets** - contributing $126K and $93K respectively and ranking first and second across both order volume and revenue, making them the highest-priority markets for retention and growth strategy.

4. **Cunewalde is the top city by net sales at $110K** - outperforming major international hubs like London ($53K), identifying it as a strategically critical shipping and customer concentration point.

5. **Fresh and Cheese categories account for ~40% of total revenue** - at $268K and $235K respectively, these two categories should anchor inventory planning, promotional budgets, and supplier negotiations.

6. **Côte de Blaye alone represents ~11% of total net sales ($141K)** - this single SKU creates significant revenue concentration risk; any supply disruption or pricing change would have an outsized impact on the bottom line.

7. **Sales Representatives place the most orders (420)** - nearly 25% more than Owners (343) and Sales Managers (342), making this the most valuable contact role for order generation and account prioritisation.

8. **QUICK-Stop is the highest-value customer at $110K**, followed closely by Ernst Handel ($105K) and Save-a-lot Markets ($104K). The top 3 companies together contribute roughly 25% of total net sales across all 91 customers, indicating high revenue concentration in a small number of accounts.

---
## 🚀 How to Use

1. **Clone or download** this repository to your local machine.
2. **Open Power BI Desktop** (version 2.117 or later recommended).
3. Open `E-Commerce Project.pbix` from the repository root.
4. If prompted for data source credentials, update the source path via:
   `Home → Transform Data → Data Source Settings`
5. Click **Refresh** to reload data if connected to a live source, or explore the pre-loaded in-memory model directly.
6. Use the **slicers** (Year, Cities, Month) on each report page to filter all visuals simultaneously.
7. **Click any chart element** (bar, donut slice, line point) to activate cross-filtering across the page.
8. Use the **navigation arrow button** (top-right) to switch between Sales and Customer dashboards.
9. Visit **Page 3 - Insights** for a plain-language executive summary.

---

## 📁 Repository Structure

```
├── E-Commerce Project.pbix       # Power BI Desktop report file
├── README.md                     # Project documentation (this file)
├── images/
│   ├── sales_dashboard.png       # Screenshot - Sales Dashboard page
│   ├── customer_dashboard.png    # Screenshot - Customer Dashboard page
│   └── insights.png              # Screenshot - Insights page
└── data/                         # Source data files (optional)
```

---

*E-Commerce Sales Analytics Dashboard - Mahmoud Shamoun, Data Analyst Specialist*

---

## 🎯 Final Note

This dashboard was built as part of my data analytics portfolio to demonstrate real-world skills in data modeling, DAX, Power Query, and business intelligence reporting using Power BI Desktop.
The dataset used is the Northwind sample database - a publicly available dataset - adapted and transformed to simulate an e-commerce business context.
If you found this project useful or insightful, feel free to ⭐ star the repository - it helps others discover it.
Feedback, suggestions, and contributions are always welcome.

"Data is only valuable when it tells a story. This dashboard is that story."
