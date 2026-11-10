# New Zealand Port | Maritime Trade Control Tower

![Power BI](https://img.shields.io/badge/Platform-Power%20BI-orange?style=for-the-badge&logo=powerbi)
![DAX](https://img.shields.io/badge/Language-DAX-blue?style=for-the-badge)
![Tabular Editor](https://img.shields.io/badge/Tool-Tabular%20Editor-green?style=for-the-badge)
![Data Architecture](https://img.shields.io/badge/Architecture-Star%20Schema-purple?style=for-the-badge)

An enterprise-grade, dark-themed executive control tower built in Power BI to analyze container throughput, wharfside productivity, and year-over-year operational growth across New Zealand ports using Ministry of Transport FIGS data.

---

## 📊 Executive Summary

The **New Zealand Port & Maritime Trade Control Tower** provides stakeholders and supply chain leaders with real-time visibility into national container movements, operational bottlenecks, and port efficiency. 

* **Theme:** High-contrast Dark Executive Mode (`#121212`) designed for executive operations rooms and portfolio showcases.
* **Core Visuals:** Multi-metric pivot matrix with isolated column-specific conditional formatting, historical quarterly trend line charts, regional island splitters, pie-chart throughput distribution, and real-time health status alert tooltips.

---

## 📊 Dashboard Preview

### 1. Main Dashboard Overview
<br>
<p align="center">
  <img src="screenshots/Mai
n_Dashboard.png" alt="Main Dashboard Overview" width="100%">
</p>

### 2. Operational Tooltips & Health Status Alerts
<br>
<p align="center">
  <img src="screenshots/Tooltip 1.png" alt="Tooltip View 1" width="48%">
  <img src="screenshots/Tooltip 2.png" alt="Tooltip View 2" width="48%">
</p>



---

## 🏛️ Data Architecture & Modeling

This project adheres to strict dimensional modeling principles using a clean **Star Schema**:

* **Fact Table (`Fact_Port_Operations`):** Contains granular operational events, container counts, crane rates, and ship rates.
* **Dimension Table (`Dim_Port`):** Contains port master data with North Island vs. South Island regional attribute hierarchies ($1 : *$ relationship with single-direction filtering).
* **Calendar Table (`Dim_Date`):** A continuous, unbroken date dimension generated via Power Query M-code to ensure seamless time intelligence evaluation without interval gaps.

---

## ⚡ Advanced Engineering Highlights

### 1. Tabular Editor Calculation Groups (Time Intelligence)
To avoid model bloat and prevent redundant measure creation, a centralized **Time Intelligence Calculation Group** was implemented using Tabular Editor. This dynamically evaluates any base measure across four dimensions using `SELECTEDMEASURE()`:
* **Current** (`SELECTEDMEASURE()`)
* **YTD** (`TOTALYTD(...)`)
* **SPLY** (`SAMEPERIODLASTYEAR(...)`)
* **YoY Growth %** (`DIVIDE(Current - Prev, Prev)`)

Dynamic format string expressions were injected directly into Tabular Editor to enforce clean percentage rendering:
```dax
"0.0%;-0.0%;0.0%"
```

### 2. Isolated Rules-Based Conditional Formatting
Conditional formatting was isolated exclusively to the **YoY %** column inside the matrix visual using dynamic rules mapping positive growth to soft green (`#2ECC71`) and negative growth to soft red (`#FF4D4D`), keeping the rest of the matrix clean and readable.

### 3. Operational Health Status Alerts
A DAX-driven alert engine tracks hourly crane productivity thresholds in real-time, feeding visual indicator cards and tooltips:
```dax
Port Health Status = 
VAR CurrentCraneRate = [Avg Crane Rate]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(CurrentCraneRate), "No Data",
    CurrentCraneRate < 25, "⚠️ Bottleneck Detected",
    CurrentCraneRate < 30, "🟡 Moderate Flow",
    "🟢 Optimal Operations"
)
```

---

## 📈 Core Measures & DAX Snippets

| Measure Name | DAX Formula / Logic | Description |
| :--- | :--- | :--- |
| **Total Containers** | `SUM('Fact_Port_Operations'[Containers])` | Total container volume handled. |
| **Avg Crane Rate** | `AVERAGE('Fact_Port_Operations'[Crane Rate])` | Hourly container lifts per crane (productivity KPI). |
| **Avg Ship Rate** | `AVERAGE('Fact_Port_Operations'[Ship Rate])` | Total wharfside container throughput per hour. |
| **Network YoY Growth %** | `DIVIDE([Total Network Containers] - CALCULATE([Total Network Containers], SAMEPERIODLASTYEAR('Date'[Date])), CALCULATE([Total Network Containers], SAMEPERIODLASTYEAR('Date'[Date])))` | National year-over-year volume trajectory. |

---

## 🚀 Key Business Insights

1. **Volume Concentration:** Throughput is heavily concentrated in major hub ports like **Tauranga** and **Auckland**, handling the vast majority of national container traffic.
2. **Productivity Variance:** Crane rates fluctuate seasonally during peak shipping periods, directly triggering operational bottleneck alerts in secondary regional ports.
3. **Regional Resilience:** South Island ports demonstrate stable baseline throughput with lower quarter-over-quarter volatility compared to North Island import hubs.

---

## 📂 Repository Structure

```text
📦 NEW ZEALAND PORT & MARITIME TRADE  EXECUTIVE CONTROL TOWER
 ┣ 📂 FIGS port container handling.csv     # Cleaned source CSVs
 ┣ 📂 Port Operations                      # .pbix source file
 ┣ 📂 screenshots                          # High-resolution snapshots
 ┗ 📜 README.md                            # Project documentation
```

---

## 👤 Author
* **Satnam Singh** — *Data Analyst & Business Intelligence Developer*
* Master of Arts (Economics) Candidate — Massey University
* **GitHub:** [@Satnam-Singh-Analyst](https://github.com/Satnam-Singh-Analyst)
* **LinkedIn:** [Satnam Singh](https://linkedin.com/in/satnam-singh-3b124a230)
