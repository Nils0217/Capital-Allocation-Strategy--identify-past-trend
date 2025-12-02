# 📊 Renewable Energy SQL Analytics

A complete SQL workflow for analyzing global renewable energy data, including investment efficiency, technology comparison, time evolution, and value ranking.

This repository provides a structured MySQL-based analytical pipeline that uses views, common table expressions (CTEs), window functions, and aggregation logic to extract insights from a renewable energy dataset.

✨ **Note:**
You can simply upload only the columns you need — in this project, I select the specific column directly through MySQL Workbench.

---

## 🚀 Project Overview

This project demonstrates how to:

* Clean and standardize raw renewable energy data
* Create analytical views for reusability
* Generate KPIs such as **Cost per MW**, **Production Efficiency**, **Value Score**, and **Market Share**
* Compare technologies, countries, and year-over-year cost changes
* Build a complete dashboard-style aggregated table for BI tools

All queries are written for **MySQL**, but can be adapted to Snowflake, PostgreSQL, or BigQuery.

---

## 📁 File Structure

```
📦 renewable-energy-sql-analytics
│
├── README.md                 # Documentation
├── queries.sql               # All SQL queries and views
└── sample_dataset.csv        # Example dataset (optional)
```

---

## 🛠️ Requirements

* MySQL 8+
* MySQL Workbench (optional but recommended)
* Clean dataset containing at least the following fields:

  * Country
  * Year
  * Energy Type
  * Production (GWh)
  * Installed Capacity (MW)
  * Investments (USD)

---

## 📥 Dataset Setup

```sql
USE clean_energy;

SELECT * FROM complete_renewable_energy_dataset;
```

---

## 🧱 Step 1 – Create a Clean, Standardized View

```sql
CREATE VIEW ENERGY1 AS (
    SELECT 
        `Country` AS `COUNTRY`,
        `Year` AS `YEAR`,
        `Energy Type` AS `ENERGY_TYPE`,
        `Production (GWh)` AS `PRODUCTION_GWH`,
        `Installed Capacity (MW)` AS `INSTALLED_CAPACITY_MW`,
        `Investments (USD)` AS `INVESTMENTS`
    FROM complete_renewable_energy_dataset
);
```

---

## 🧱 Step 2 – Build the Main Aggregated Dataset

```sql
CREATE OR REPLACE VIEW aggregated_data AS (
    SELECT 
        COUNTRY,
        ENERGY_TYPE,
        YEAR,
        SUM(INSTALLED_CAPACITY_MW) AS TOTAL_CAPACITY_MW,
        SUM(PRODUCTION_GWH) * 1000 AS TOTAL_PRODUCTION_MWH,
        SUM(INVESTMENTS) AS TOTAL_INVESTMENTS_USD
    FROM ENERGY1
    WHERE INSTALLED_CAPACITY_MW > 0 
      AND INVESTMENTS > 0
    GROUP BY COUNTRY, ENERGY_TYPE, YEAR
);
```

---

# 📚 Analysis Queries

Below are the core analytical queries included in this repository.

---

## 🔍 **Query 1 — Core Metrics: Cost per MW + Production Efficiency**

This query computes:

* Total capacity
* Total production
* Total investments
* $ per MW cost
* Production efficiency per MW

```sql
SELECT 
    COUNTRY,
    ENERGY_TYPE,
    YEAR,
    TOTAL_CAPACITY_MW,
    TOTAL_PRODUCTION_MWH,
    TOTAL_INVESTMENTS_USD,
    ROUND(TOTAL_INVESTMENTS_USD / NULLIF(TOTAL_CAPACITY_MW, 0), 2) AS COST_PER_MW_USD,
    ROUND(TOTAL_PRODUCTION_MWH / NULLIF(TOTAL_CAPACITY_MW, 0), 2) AS PRODUCTION_EFFICIENCY
FROM aggregated_data
ORDER BY YEAR DESC, COUNTRY, ENERGY_TYPE;
```

---

## ⚙️ **Query 2 — Technology Comparison: Which Renewable Tech Is Cheapest?**

Aggregates cost per MW across all countries and years.

---

## 🌍 **Query 3 — Regional Comparison: Which Country Invests Most Efficiently?**

Includes:

* % of global investments
* Cost per MW ranking

---

## 📈 **Query 4 — Time Evolution: Cost Trends Year-over-Year**

Uses window functions (`LAG()`) to compute:

* Cost change
* Cost change percentage

---

## 🧮 **Query 5 — Dashboard View**

Creates a BI-ready summary table with:

* Cost per MW
* Equivalent production hours
* Market share
* Within-technology ranking

---

## 🏆 **Query 6 — Best Value Analysis (ROI-Style)**

Produces a **VALUE SCORE**:

```
(production per MW) ÷ (cost per MW)
```

Top 20 global best-value energy assets.


# 🤝 Contributing

Pull requests and issue reports are welcome — especially improvements, optimizations, or adapting the SQL for other databases.
