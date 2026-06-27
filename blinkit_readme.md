# 📊 Blinkit Sales Data Analysis

Comprehensive analysis of Blinkit (India's fastest grocery delivery app) sales performance using SQL and Power BI. This project demonstrates data cleaning, aggregation, segmentation, and business intelligence best practices.

**Live Dashboard:** [Power BI Public Link](#) | **Data:** 8,523 items across multiple outlets | **Time Period:** 2011-2022

---

## 🎯 Problem Statement

Blinkit's leadership needed insights to optimize:
- Which product categories drive the most revenue?
- How do outlet location and size impact sales?
- What's the impact of product type (Low Fat vs Regular) on customer purchases?
- Which outlet establishment year shows peak performance?
- How do outlet tiers perform across different regions?

**Goal:** Derive actionable insights from transaction data to improve inventory planning, pricing strategy, and outlet expansion decisions.

---

## 📊 Key Findings

### **Overall Metrics**
| Metric | Value | Insight |
|--------|-------|---------|
| **Total Sales** | **$1.20M** | Strong baseline across all outlets |
| **Average Order Value** | **$140.99** | Consistent transaction value |
| **Average Rating** | **3.96/5** | High customer satisfaction |
| **Items Analyzed** | **8,523** | Diverse product portfolio |

### **Product Performance**
**By Fat Content:**
- **Low Fat Products:** $776,320 (64.7% of sales) ⭐ **Clear winner**
- **Regular Products:** $425,362 (35.3% of sales)

**Top 5 Product Categories by Revenue:**
1. **Fruits & Vegetables** — $178,124 (highest volume)
2. **Snack Foods** — $175,434 (close second)
3. **Household** — $135,977 (highest avg order: $149)
4. **Frozen Foods** — $118,559
5. **Dairy** — $101,276

### **Outlet Location Performance**
**Sales by Tier (Low Fat vs Regular):**
| Location | Low Fat | Regular | Total |
|----------|---------|---------|-------|
| **Tier 3** ⭐ | $306,807 | $165,326 | **$472,133** |
| **Tier 2** | $254,465 | $138,686 | $393,151 |
| **Tier 1** | $215,048 | $121,350 | $336,398 |

**Insight:** Tier 3 locations (tier-1 cities/metro areas) generate 40% more revenue than Tier 1.

### **Outlet Size Effectiveness**
| Size | Sales | % of Total | Avg Order Value |
|------|-------|-----------|-----------------|
| **Medium** ⭐ | $507,896 | **42.27%** | Optimal efficiency |
| **Small** | $444,794 | 37.01% | Strong compact performance |
| **High** | $248,992 | 20.72% | Underperforming (high costs?) |

**Insight:** Medium outlets are most profitable. High-sized outlets may have operational inefficiencies.

### **Outlet Performance by Year**
- **2018 Peak:** $204,522 (highest sales year)
- **Stable Period:** 2012-2017 (consistent ~$130K/year)
- **2011 Baseline:** $78,132 (early operations)
- **Recent Trend:** Slight decline in 2020, recovery in 2022

---

## 🛠️ Methodology

### **Data Cleaning**

**Problem Identified:** Inconsistent values in `item_fat_content` column
```
Input Values: "LF", "low fat", "Low Fat", "reg", "Regular"
Issue: Same categories recorded in different formats
```

**Solution Applied:** SQL CASE WHEN logic
```sql
UPDATE BLINKIT_DATA
SET ITEM_FAT_CONTENT = 
CASE
  WHEN ITEM_FAT_CONTENT IN ("low fat","LF") THEN "Low Fat"
  WHEN ITEM_FAT_CONTENT = "reg" THEN "Regular"
  ELSE ITEM_FAT_CONTENT 
END;
```

**Result:** Standardized to 2 consistent categories (Low Fat, Regular) ✅

---

## 📝 SQL Analysis Examples

### **1. Total Sales by Fat Content (GROUP BY)**
```sql
SELECT 
    ITEM_FAT_CONTENT,
    CAST(SUM(SALES) AS DECIMAL(10,2)) AS TOTAL_SALES,
    CAST(AVG(SALES) AS DECIMAL(10,0)) AS AVERAGE_SALES,
    CAST(COUNT(*) AS DECIMAL(10,0)) AS TOTAL_ORDERS,
    CAST(AVG(RATING) AS DECIMAL(10,2)) AS AVERAGE_RATING
FROM blinkit_data
GROUP BY ITEM_FAT_CONTENT
ORDER BY TOTAL_SALES DESC;

-- Output:
-- Low Fat:    $776,319.69 | Avg: $141 | Orders: 5,510 | Rating: 3.97
-- Regular:    $425,361.80 | Avg: $140 | Orders: 3,013 | Rating: 3.94
```

### **2. Top 5 Products by Sales (ORDER BY + LIMIT)**
```sql
SELECT TOP 5
    ITEM_TYPE,
    CAST(SUM(SALES) AS DECIMAL(10,0)) AS TOTAL_SALES,
    CAST(AVG(SALES) AS DECIMAL(10,2)) AS AVERAGE_SALES,
    CAST(COUNT(*) AS DECIMAL(10,0)) AS TOTAL_ORDERS,
    CAST(AVG(RATING) AS DECIMAL(10,2)) AS AVERAGE_RATINGS
FROM BLINKIT_DATA
GROUP BY ITEM_TYPE
ORDER BY TOTAL_SALES DESC;
```

### **3. Sales by Location & Fat Content (CASE WHEN + GROUP BY)**
```sql
SELECT 
    OUTLET_LOCATION_TYPE,
    CAST(SUM(CASE WHEN ITEM_FAT_CONTENT='Low Fat' THEN SALES ELSE 0 END) 
         AS DECIMAL(10,2)) AS LOW_FAT_SALES,
    CAST(SUM(CASE WHEN ITEM_FAT_CONTENT='Regular' THEN SALES ELSE 0 END) 
         AS DECIMAL(10,2)) AS REGULAR_SALES
FROM BLINKIT_DATA
GROUP BY OUTLET_LOCATION_TYPE;

-- Shows clear tier-wise breakdown
-- Tier 3 dominates with $472K total sales
```

### **4. Outlet Establishment Year Analysis (Aggregation)**
```sql
SELECT 
    OUTLET_ESTABLISHMENT_YEAR,
    CAST(SUM(SALES) AS DECIMAL(10,2)) AS TOTAL_SALES
FROM BLINKIT_DATA
GROUP BY OUTLET_ESTABLISHMENT_YEAR
ORDER BY OUTLET_ESTABLISHMENT_YEAR;

-- Identifies 2018 as peak performance year ($204,522)
```

### **5. Outlet Size Contribution (Window Function - Percentage)**
```sql
SELECT 
    OUTLET_SIZE,
    CAST(SUM(SALES) AS DECIMAL(10,0)) AS TOTAL_SALES,
    CAST(SUM(SALES)*100/SUM(SUM(SALES)) OVER() AS DECIMAL(10,2)) 
        AS PERCENTAGE_SALES
FROM BLINKIT_DATA
GROUP BY OUTLET_SIZE;

-- Medium: 42.27% | Small: 37.01% | High: 20.72%
```

---

## 📊 Power BI Dashboard

**Dashboard Features:**
- ✅ KPI cards (Total Sales, Avg Rating, Avg Order Value, Item Count)
- ✅ Fat Content breakdown (pie chart + sales comparison)
- ✅ Item Type performance (top 16 categories by revenue)
- ✅ Outlet Location heatmap (Tier-wise sales distribution)
- ✅ Outlet Size effectiveness (pie chart % contribution)
- ✅ Sales trend by outlet establishment year (line chart)
- ✅ Interactive filters (Location, Outlet Type, Item Type)

**Dashboard Filters Allow:**
- Filter by outlet location tier (Tier 1, 2, 3)
- Filter by outlet type (Grocery Store, Supermarket Type1-3)
- Filter by item category (all 16 categories)
- Dynamic KPI updates based on selections

---

## 💻 Tools & Technologies

| Tool | Purpose | Usage |
|------|---------|-------|
| **SQL Server** | Data cleaning, aggregation, analysis | GROUP BY, CASE WHEN, aggregation functions |
| **SQL (Queries)** | Data extraction & transformation | CAST, SUM, AVG, COUNT, window functions |
| **Power BI** | Data visualization & dashboards | Interactive KPIs, charts, filters |
| **Excel** | Data validation & supplementary analysis | Pivot tables, formatting |
| **CSV Files** | Raw data storage & reproducibility | 8,523 transaction records |

---

## 📁 Project Structure

```
Blinkit-Sales-Data-Analysis/
├── README.md (this file)
├── sql/
│   ├── blinkit_sql_analysis.sql        (all SQL queries)
│   ├── 01_data_cleaning.sql            (standardize fat_content)
│   ├── 02_kpi_analysis.sql             (total/avg sales, ratings)
│   ├── 03_product_performance.sql      (sales by item type)
│   └── 04_outlet_analysis.sql          (location, size, year analysis)
├── data/
│   ├── binkit_sql_file.csv             (raw transaction data - 8,523 items)
│   └── data_dictionary.md              (column definitions)
├── dashboards/
│   ├── Blinkit_Dashboard_Screenshot.pdf (Power BI visual export)
│   └── instructions.md                  (how to view/interact)
└── .gitignore
```

---

## 🚀 How to Use This Project

### **Option 1: Run SQL Queries (Reproduce Analysis)**
1. Import `binkit_sql_file.csv` into SQL Server/MySQL
2. Execute queries from `sql/blinkit_sql_analysis.sql`
3. View results in your SQL IDE
4. Compare with findings in this README

### **Option 2: View Power BI Dashboard**
1. Open `Blinkit_Dashboard_Screenshot.pdf` for static view
2. For interactive dashboard: [Power BI Public Link](#)

### **Option 3: Analyze the Data Yourself**
```bash
# Clone repo
git clone https://github.com/suriyaempire04072002-bit/Blinkit-Sales-data-analysis.git
cd Blinkit-Sales-data-analysis

# Open CSV in Excel/Python/SQL
# Run SQL queries against the data
# Build your own dashboards
```

---

## 🔍 Key Technical Skills Demonstrated

✅ **SQL Mastery:**
- Data cleaning (CASE WHEN standardization)
- Aggregation functions (SUM, AVG, COUNT)
- Grouping & filtering (GROUP BY, WHERE)
- Advanced filtering (window functions for percentages)
- Multi-table joins (outlet + sales data)

✅ **Data Analysis:**
- Exploratory data analysis (EDA)
- Trend identification (yearly patterns)
- Segmentation analysis (by location, size, product)
- KPI calculation & interpretation

✅ **Business Intelligence:**
- Dashboard design & interactivity
- Visual storytelling (actionable insights)
- Filter implementation for drill-down analysis

✅ **Problem Solving:**
- Data quality issues identified & resolved
- Business problems translated to SQL queries
- Insights that drive decision-making

---

## 📈 Business Impact & Recommendations

### **Immediate Actions**
1. **Prioritize Low Fat Products:** 64.7% of sales vs. 35.3% — Expand low fat inventory
2. **Focus on Tier 3 Expansion:** 40% higher sales — Accelerate metro-area outlet growth
3. **Optimize Outlet Size:** Medium outlets most profitable — Target medium-sized locations for new expansion
4. **Investigate High-Size Outlets:** Only 20.72% contribution — Analyze operational costs and efficiency

### **Strategic Decisions**
- Expand Fruits & Vegetables and Snack Foods categories (top 2 by revenue)
- Re-evaluate pricing/positioning of Regular products (lower performing)
- Plan next expansion wave focusing on Tier 2 → Tier 3 locations
- Consider downsizing or restructuring high-outlet stores

---

## 📊 Data Quality Notes

- **Data Completeness:** 8,523 unique items across all fields
- **Data Period:** 2011-2022 (11 years)
- **Outlet Tiers:** 3 categories (Tier 1, 2, 3)
- **Product Categories:** 16 distinct item types
- **Outlet Types:** 4 categories (Grocery Store, Supermarket Type1-3)
- **Ratings:** On 5-point scale (avg 3.96)

---

## 🔗 Related Projects

- **Customer Behavior Analysis** (Python + SQL) — Coming soon
- **Healthcare to Retail Analytics Bridge** — Transition case study

---

## ✍️ Author

**Suriya S** | Data Analyst  
📧 suriyaempire2002@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/igsuri03/) | [GitHub](https://github.com/suriyaempire04072002-bit)

**Key Expertise:** 3.5+ years healthcare analytics → transitioning to retail/consumer analytics. Strong SQL, Python, and Power BI fundamentals.

---

## 📝 Changelog

| Version | Date | Changes |
|---------|------|---------|
| v1.0 | June 2026 | Initial project upload + complete SQL analysis |

---

**Found this analysis useful? Drop a ⭐ on GitHub!**

---

## 📞 Questions?

For questions about:
- **SQL Queries:** Check `sql/` folder and see inline comments
- **Dashboard:** Open `Blinkit_Dashboard_Screenshot.pdf` or Power BI link
- **Data:** Refer to `data_dictionary.md` for column definitions
- **Methodology:** Review SQL Implementation sections above
