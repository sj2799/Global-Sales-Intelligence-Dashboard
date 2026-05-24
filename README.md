# Global-Sales-Intelligence-Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Data Modeling](https://img.shields.io/badge/Data%20Modeling-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)

An interactive Power BI dashboard analysing global internet sales across 6 countries. The report demonstrates dynamic measure switching (Number vs. Percentage) using a slicer, country-level sales analysis, an alphabetically sorted bar chart, and a context-aware dynamic text box.

## 🎯 Project Goal
Develop an interactive Power BI dashboard with a dynamic bar chart, slicer-driven measure toggle (Number/Percentage), alphabetically sorted country visuals, and context-sensitive dynamic text based on user selections.

---

## 📊 Features / Highlights

| Feature | Description |
|---|---|
| **Measures Table** | A dedicated `Measures` table created on top of all data tables to keep DAX organised |
| **Total US Sales (Number)** | DAX measure filtering sales to United States territory and returning the raw dollar value |
| **Total US Sales (%)** | DAX measure expressing US sales as a percentage of global total sales |
| **Slicer Toggle** | A manually created `SwitchTable` used with a Tile slicer to switch between Number and Percentage measures — no bookmarks needed |
| **Sales by Country Bar Chart** | Bar chart visualising `SalesAmount` per country, sorted alphabetically (A → Z) using a `SalesTerritoryCountry` column |
| **Dynamic Text Box** | A DAX-powered card that reads the active slicer selection — shows the selected country name, or falls back to *"Please, Choose a Country"* when nothing is chosen |

---

## 🗂️ Data Model

| Table | Description |
|---|---|
| `fact_InternetSales` | Transaction-level sales (SalesAmount, OrderDate, keys) |
| `dim_SalesTerritory` | Country & region lookup |
| `dim_Product` | Product details |
| `dim_Customer` | Customer details |
| `dim_Currency` | Currency reference |
| `SwitchTable` | Manually created table (Enter Data) with Number/Percentage values to drive the slicer toggle |

---

## 🔢 DAX Formulas Used

```dax
-- Total Sales (All Countries)
TotalSalesAmount = SUM(fact_InternetSales[SalesAmount])

-- US Sales in Absolute Value
SalesAmountUnitedStates = CALCULATE(
    SUM(fact_InternetSales[SalesAmount]), 
    dim_SalesTeritory[SalesTerritoryCountry] = "United States")

-- US Sales as % of Global Total
%SalesAmountUnitedStates = DIVIDE([SalesAmountUnitedStates],
    CALCULATE(SUM(fact_InternetSales[SalesAmount]), 
    ALL(dim_SalesTeritory[SalesTerritoryCountry]))) * 100

-- Total Sales as % (used in bar chart toggle)
%TotalSalesAmount = 
    DIVIDE([TotalSalesAmount], CALCULATE(SUM(fact_InternetSales[SalesAmount]),
    ALL(dim_SalesTeritory[SalesTerritoryCountry]), 
    ALL(dim_SalesTeritory[CountryOrder]))) * 100

-- Slicer-Driven Toggle (core dynamic measure)
NumberVsPercentage = 
    IF(SELECTEDVALUE(SwitchTable[DataType]) = "Number", [TotalSalesAmount],
    IF(SELECTEDVALUE(SwitchTable[DataType]) = "Percentage", [%TotalSalesAmount],
    BLANK()))

-- Dynamic Text Box (Context-Aware Label)
SelectedCountry = 
    VAR COUNTRYSELECTED = ISFILTERED(dim_SalesTeritory[SalesTerritoryCountry])
    RETURN
    IF(COUNTRYSELECTED, 
        "Selected Country is " & SELECTEDVALUE(dim_SalesTeritory[SalesTerritoryCountry]),
        "Please, Choose a Country")

-- Alphabetical Sort Key (Calculated Column in dim_SalesTerritory)
CountryOrder = 
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Australia", 1, 
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Canada", 2,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "France", 3,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Germany", 4,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "United Kingdom", 5,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Central", 6,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Northeast", 6,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Northwest", 6,
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Southeast", 6, 
    IF(dim_SalesTeritory[SalesTerritoryRegion] = "Southwest", 6,
    BLANK()))))))))))
```

---

## 🛠️ Tech Stack

- **Power BI Desktop** — Report authoring, slicers, visual formatting
- **DAX (Data Analysis Expressions)** — Measures, CALCULATE, DIVIDE, SELECTEDVALUE, ISFILTERED, ALL
- **Power Query (M)** — Data ingestion & transformation from Excel
- **Excel (.xlsx)** — Raw data source (5 tables)

---

## 🔍 Data Insights

> 💡 *Based on the sample dataset from the 365 Data Science course — not real-world business data.*

| Country | Sales Amount | Share % |
|---|---|---|
| 🇺🇸 United States | 9.4M | 32.0% |
| 🇦🇺 Australia | 9.1M | 30.9% |
| 🇬🇧 United Kingdom | 3.4M | 11.6% |
| 🇩🇪 Germany | 2.9M | 9.9% |
| 🇫🇷 France | 2.6M | 9.0% |
| 🇨🇦 Canada | 2.0M | 6.7% |

- **US and Australia dominate**, together accounting for ~63% of total internet sales (32% + 30.9%), despite the dataset spanning 6 countries across 3 continents
- **Australia is a near-equal competitor to the US** — separated by only 0.3M (~3%), which is surprisingly close given the population difference
- **A clear two-tier split exists** — US and Australia (9M+ each) vs. UK, Germany, France, and Canada (2–3.4M each), suggesting the business is heavily concentrated in two markets
- **Canada is the weakest market** at 6.7%, roughly one-third of Australia's share, pointing to either lower market penetration or fewer customers in the dataset
- **European markets combined** (UK + Germany + France = 30.5%) nearly match the US alone, but no single European country breaks into the top tier

---

## 💡 Key Learning Outcomes
- Creating a dedicated measures table in Power BI using `GENERATESERIES`
- Writing CALCULATE-based filter measures in DAX
- Building a manual SwitchTable with a Tile slicer to toggle between measures
- Creating a calculated column with nested IF statements for custom sort order
- Building context-aware dynamic text using `ISFILTERED` + `SELECTEDVALUE`

---

## 🎓 Course
✅ Built as part of a mini project series from the 365 Data Science Power BI course, which covers:
- Data modelling & table relationships
- DAX measures and calculated columns
- Interactive visuals and slicers
- Dynamic reporting techniques
- Power Query for data transformation

---

## 📬 Connect
Feel free to reach out or open an issue if you have questions or suggestions!
