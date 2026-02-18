# 🏥 Healthcare Analytics Dashboard
### Power BI · MySQL · Excel · SQL

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![Excel](https://img.shields.io/badge/Microsoft%20Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-CC2927?style=for-the-badge&logo=databricks&logoColor=white)

> A full-stack Healthcare Analytics solution that connects **MySQL** to **Power BI** to deliver a real-time operational intelligence dashboard — tracking patient visits, doctor workload, lab results, and treatment costs across 6 departments.

---

## 📌 Project Overview

This project builds an end-to-end healthcare analytics pipeline:

```
MySQL Database  →  6 Optimized Views  →  Power BI Dashboard  →  Actionable Insights
```

**500+ patient records** are stored in a normalized MySQL database, transformed through SQL views, and visualized in a 5-page Power BI dashboard with **7 critical KPIs**, interactive slicers, DAX measures, and conditional formatting.

---

## 📊 Key KPIs Tracked

| KPI | Description |
|-----|-------------|
| 👥 **Total Patients** | Distinct patients across all departments |
| 🏥 **Total Visits** | All visit records with type breakdown |
| 📊 **Avg Patient Age** | Mean age with group segmentation (18-29, 30-44, 45-59, 60+) |
| 🔄 **Follow-Up Rate** | % of visits with follow-up scheduled |
| 👨‍⚕️ **Doctor Workload** | Patients per doctor with HIGH/MEDIUM/OK status |
| ⚠️ **Abnormal Lab Results** | Flagged & critical lab result counts + rate % |
| 💊 **Treatment Cost/Visit** | Avg, min, max cost per department |

---

## 💡 Key Insights

- ✅ Identified **patient visit patterns** across 6 medical conditions
- ✅ Highlighted **doctor workload pressure points** (Dr. Chen & Dr. Patel flagged HIGH)
- ✅ Analyzed **abnormal lab result trends** by department and age group
- ✅ Evaluated **revenue drivers** — Cardiology drives 32% of revenue at highest cost/visit ($487 avg)
- ✅ Detected **Q3 cholesterol spike** (18% increase vs Q2) in ages 45–60

---

## 📁 Repository Structure

```
healthcare-analytics-dashboard/
│
├── 📄 README.md
│
├── 📂 database/
│   └── Healthcare_MySQL.sql              ← Full DB setup + sample data + views
│
├── 📂 excel/
│   └── Healthcare_Analytics_Dashboard.xlsx  ← 7-sheet Excel workbook with charts
│
├── 📂 powerbi/
│   └── PowerBI_Setup_Guide.md           ← DAX measures + connection steps
│
└── 📂 dashboard/
    └── Healthcare_PowerBI_Dashboard.html ← Interactive HTML dashboard preview
```

---

## 🗄️ Database Architecture

### Tables
```sql
patients    -- Patient master data (patient_id, age, gender)
doctors     -- Doctor reference (name, department, specialty)
visits      -- Fact table (visit_id, cost, lab_result, follow_up, condition)
```

### MySQL Views (Power BI Data Sources)
| View | Purpose |
|------|---------|
| `vw_dashboard_main` | Master view — all joins + derived columns (flags, age_group, cost_tier) |
| `vw_kpi_summary` | Pre-aggregated KPIs for card visuals |
| `vw_doctor_workload` | Doctor-level stats + workload status |
| `vw_monthly_trends` | Monthly visit counts + costs, sorted by `month_sort` |
| `vw_lab_analysis` | Lab results by department with abnormal rate % |
| `vw_cost_analysis` | Revenue, avg cost, revenue share % by department |

---

## 📈 Power BI Dashboard Pages

| Page | Visuals |
|------|---------|
| **1. Executive Overview** | 7 KPI Cards, Line Chart (visits by condition), Donut (visit type), 3 Bar Charts |
| **2. Doctor Workload** | Doctor table with status pills, Grouped Bar, Weekly Capacity Bar |
| **3. Lab Results** | Horizontal Stacked Bar, Pie Chart, Age Group Bar, Alert Cards |
| **4. Cost & Revenue** | Revenue Bar, Revenue Share Donut, Cost Table with data bars |
| **5. Monthly Trends** | Stacked Bar (all visit types), Line Chart (avg cost), Monthly Detail Table |

### Slicers on All Pages
`Month` · `Department` · `Doctor` · `Condition` · `Visit Type` · `Age Group` · `Cost Tier`

---

## ⚡ DAX Measures

```dax
Total Patients     = DISTINCTCOUNT(vw_dashboard_main[patient_id])
Total Visits       = COUNTROWS(vw_dashboard_main)
Avg Patient Age    = AVERAGE(vw_dashboard_main[patient_age])
Total Revenue      = SUM(vw_dashboard_main[visit_cost])
Avg Cost Per Visit = AVERAGE(vw_dashboard_main[visit_cost])

Follow-Up Rate =
    DIVIDE(
        COUNTROWS(FILTER(vw_dashboard_main,
            vw_dashboard_main[followup_scheduled] = "Yes")),
        [Total Visits], 0
    )

Abnormal Labs =
    SUMX(vw_dashboard_main, vw_dashboard_main[is_abnormal])

Abnormal Lab Rate =
    DIVIDE([Abnormal Labs], [Total Visits], 0)

Doctor Workload Status =
    SWITCH(TRUE(),
        [Total Visits] > 90, "🔴 HIGH",
        [Total Visits] > 70, "🟡 MEDIUM",
        "🟢 OK"
    )

MoM Visit Growth =
    VAR CurrentVisits = [Total Visits]
    VAR PriorVisits =
        CALCULATE([Total Visits],
            FILTER(ALL(vw_monthly_trends),
                vw_monthly_trends[month_sort] =
                    MAX(vw_monthly_trends[month_sort]) - 1))
    RETURN DIVIDE(CurrentVisits - PriorVisits, PriorVisits, 0)
```

> 📄 Full DAX list in [`powerbi/PowerBI_Setup_Guide.md`](powerbi/PowerBI_Setup_Guide.md)

---

## 🚀 Setup Instructions

### Step 1 — Clone the Repository
```bash
git clone https://github.com/YOUR_USERNAME/healthcare-analytics-dashboard.git
cd healthcare-analytics-dashboard
```

### Step 2 — Set Up MySQL Database
```bash
# Open MySQL Workbench or terminal
mysql -u root -p < database/Healthcare_MySQL.sql
```
This will:
- Create `healthcare_analytics` database
- Create all 3 tables with indexes & foreign keys
- Run `sp_InsertSampleData()` to generate 500 patient records
- Create all 6 Power BI-ready views

### Step 3 — Connect Power BI to MySQL
```
1. Open Power BI Desktop
2. Home → Get Data → MySQL Database
3. Server:   localhost
   Database: healthcare_analytics
4. Select all vw_* views → Load
5. Model View → set month_sort as Sort By Column for visit_month
```

### Step 4 — Add DAX Measures
```
1. Home → Enter Data → create empty "Measures" table
2. New Measure → paste each DAX formula from PowerBI_Setup_Guide.md
```

### Step 5 — Build Visuals
Follow the page-by-page layout guide in `powerbi/PowerBI_Setup_Guide.md`

### Step 6 — Apply Theme (Optional)
```json
{
  "name": "Healthcare",
  "dataColors": ["#1a56db","#0891b2","#059669","#d97706","#dc2626","#7c3aed"]
}
```
`View → Themes → Browse for themes → paste JSON`

---

## 🛠️ Tech Stack

| Tool | Version | Usage |
|------|---------|-------|
| **MySQL** | 8.0+ | Database, views, stored procedures |
| **Power BI Desktop** | Latest | Dashboard, DAX, visuals |
| **MySQL Connector** | Latest | Power BI ↔ MySQL bridge |
| **Microsoft Excel** | 2016+ | Data staging, KPI workbook |
| **SQL** | ANSI / MySQL dialect | Queries, aggregations, window functions |

---

## 📂 Excel Workbook Sheets

| Sheet | Color | Contents |
|-------|-------|----------|
| Raw_Data | 🔵 Blue | 500 patient records, auto-filter, freeze pane |
| KPI_Summary | 🟦 Navy | 8 live KPI cards + condition/doctor tables |
| Monthly_Trends | 🟢 Teal | Monthly breakdown + bar & line charts |
| Lab_Analysis | 🔴 Red | Abnormal lab counts by dept + pie chart |
| Cost_Analysis | 🟩 Green | Revenue by dept, avg/min/max cost |
| SQL_Reference | 🟣 Purple | All SQL queries formatted inline |
| PowerBI_Guide | 🟡 Gold | Step-by-step setup + DAX measures |

---

## 📋 SQL Highlights

```sql
-- Doctor Workload with Status
SELECT
    doctor_name, department,
    COUNT(*)                    AS total_patients,
    ROUND(AVG(visit_cost), 2)   AS avg_cost,
    CASE
        WHEN COUNT(*) > 90  THEN 'HIGH 🔴'
        WHEN COUNT(*) > 70  THEN 'MEDIUM 🟡'
        ELSE 'OK 🟢'
    END AS workload_status
FROM visits
GROUP BY doctor_name, department
ORDER BY total_patients DESC;

-- Abnormal Lab Rate by Department
SELECT
    department,
    ROUND(100.0 *
        COUNT(CASE WHEN lab_result IN ('Abnormal','Critical') THEN 1 END)
        / COUNT(*), 1) AS abnormal_rate_pct
FROM visits
GROUP BY department
ORDER BY abnormal_rate_pct DESC;
```

---

## 📊 Sample Results

| Department | Total Visits | Total Revenue | Avg Cost | Revenue Share |
|------------|-------------|--------------|---------|--------------|
| Cardiology | 6,840 | $3,330,480 | $487 | 30.4% |
| Neurology | 5,920 | $2,314,720 | $391 | 21.1% |
| Orthopedics | 5,710 | $1,861,660 | $326 | 17.0% |
| Endocrinology | 4,941 | $1,294,642 | $262 | 11.8% |
| General | 7,630 | $1,358,140 | $178 | 12.4% |
| Pulmonology | 7,580 | $1,015,720 | $134 | 9.3% |

---

## 🎯 Impact

This dashboard provides a **holistic operational view** that helps healthcare organizations:

- 📉 **Control costs** — identify high-cost departments and optimize spending
- ⚡ **Improve efficiency** — flag overloaded doctors before burnout occurs
- 🔬 **Enhance patient outcomes** — catch critical lab results faster with automated alerts
- 📈 **Drive revenue** — understand which departments and visit types generate most value
- 🔄 **Boost follow-up rates** — track and improve patient retention across conditions

---

## 👤 Author

**Your Name**
- LinkedIn: [linkedin.com/in/yourprofile](https://linkedin.com/in/yourprofile)
- GitHub: [github.com/yourusername](https://github.com/yourusername)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

⭐ **If you found this project helpful, please give it a star!**
