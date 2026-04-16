 # 🛍️ Vrinda Fashion Platform
 Revenue Intelligence & Operational Optimization, "The 64% Engine: How Revenue is Won in E-commerce”'
 - Instagram: https://www.instagram.com/vrinda_clothingstudio/
 - Website: https://vrindaclothing.in/?srsltid=AfmBOoqdnKz9xn4ufKfuvs8bu8YibNrsUrNSskfFHXszv9OofTfr6-t4

 <img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/a7fa26d4-e837-43e2-bdb5-615fbd8cd160" />

#### ⚡ Executive Summary
"**64% of total revenue** is driven by a **single customer segment**, while **specific regions** show disproportionately **high cancellations** - highlighting both growth opportunities and operational gaps.”

This **project simulates an Ajio/Myntra-style e-commerce platform**, transforming raw transactional data into a decision-making system focused on:

- Revenue growth
- Customer segmentation
- Operational efficiency


---

### 🚩 Business Problem  

The company lacked a centralized analytics layer to answer critical questions:

Key challenges:

1. What is the **true revenue** after cancellations & returns?
2. Which **customers actually drive growth?**
3. Where are **operational leakages** happening?
4. Which products contribute vs drag performance?
5. Showcase clear growth and profitable 

---

### 🎯 Objective

To build a scalable analytics framework that:

- Tracks **true business performance (Gross vs Net Revenue)**
- Identifies **high-value customer segments**
- Highlights **product-level revenue concentration**
- Detects **operational inefficiencies** (returns & cancellations)
- Analyzes **seasonal demand patterns**

---


### 📊 Primary KPI Framework ⭐ 

| Category | KPIs | Insight | 
|----------|------|------|
| **Sales**  | Gross Sales, Net Revenue, Orders, AOV | Financial performance |
| **Customer**  | Revenue by Gender & Age | Targeting strategy |
| **Product** | Category Contribution | Inventory optimization |
| **Logistics/ Operations**  | Delivery %, Cancellation Rate %, Return Rate % | Efficiency and cost control |


---


### 🏗️ Data Preprocessing & Preparation
Kaggle: https://www.kaggle.com/datasets/anshika2301/vrinda-store-data-analysis/data?select=Store+Data.xlsx

- Cleaned and processed **30,000+ transaction records** using Power Query ETL
- Important Columns : Channels, category , Quantity, Amount, Region, Date, Order_status, Age, Gender
- Handled **missing values** , removed **duplicates**
- Standardized categorical fields (Delivered, Cancelled, Returned)
- Built Csv data into structured analytical format  

---

## 🗄️ SQL Deepth Analysis & Business Quering

This section contains SQL queries for KPI validation, segmentation, and trend analysis

#### Key Areas:
- Revenue tracking (Gross vs Net)
- Customer segmentation
- Regional operational inefficiencies
- Product performance ranking
- Advanced techniques: CTEs + Window Functions (RANK, % Contribution)

### 📊 1. Revenue Metrics  

#### Total Revenue  
```sql
SELECT 
    SUM(amount) AS total_revenue
FROM ecommerce_data;

```

#### Net Revenue (Delivered Orders Only)

```sql
SELECT 
    SUM(amount) AS net_revenue
FROM ecommerce_data
WHERE order_status = 'Delivered';

```
#### Total Orders

```sql

SELECT 
    COUNT(DISTINCT order_id) AS total_orders
FROM ecommerce_data;
Average Order Value (AOV)
SELECT 
    SUM(amount) * 1.0 / COUNT(DISTINCT order_id) AS avg_order_value
FROM ecommerce_data
WHERE order_status = 'Delivered';

```

### 📈 2. Seasonality & Customer Insights

#### Monthly Revenue Trend

```sql

SELECT 
    order_month,
    SUM(amount) AS revenue
FROM ecommerce_data
WHERE order_status = 'Delivered'
GROUP BY order_month
ORDER BY order_month;
```

#### Revenue by Age Group & Gender

```sql

SELECT 
    age,
    gender,
    SUM(amount) AS revenue
FROM ecommerce_data
WHERE order_status = 'Delivered'
GROUP BY age, gender
ORDER BY age, revenue DESC;

```

### 🚚 3. Operations & Returns

#### Cancellation Rate by Region
```sql

SELECT 
    ship_state,
    COUNT(CASE WHEN order_status = 'Cancelled' THEN 1 END) * 1.0 
    / COUNT(*) AS cancellation_rate
FROM ecommerce_data
GROUP BY ship_state
ORDER BY cancellation_rate DESC;

```


### 🔄 4. Returns Analysis

#### Return Rate by Category

```sql

SELECT 
    category,
    COUNT(CASE WHEN order_status = 'Returned' THEN 1 END) * 1.0
    / COUNT(CASE WHEN order_status = 'Delivered' THEN 1 END) AS return_rate
FROM ecommerce_data
GROUP BY category
ORDER BY return_rate DESC;

```


#### Return Rate by State


```sql

SELECT 
    ship_state,
    COUNT(CASE WHEN order_status = 'Returned' THEN 1 END) * 1.0
    / COUNT(CASE WHEN order_status = 'Delivered' THEN 1 END) AS return_rate
FROM ecommerce_data
GROUP BY ship_state
ORDER BY return_rate DESC;

```
### 🧠 5. Advanced Analysis - Inluded CTEs and Window Function

#### Category Revenue Ranking

```sql

WITH category_revenue AS (
    SELECT 
        category,
        SUM(amount) AS revenue
    FROM ecommerce_data
    WHERE order_status = 'Delivered'
    GROUP BY category )

SELECT 
    category,
    revenue,
    RANK() OVER (ORDER BY revenue DESC) AS rank_
FROM category_revenue;

```

#### Revenue Share by Category

```sql

WITH category_revenue AS (
    SELECT 
        category,
        SUM(amount) AS revenue
    FROM ecommerce_data
    WHERE order_status = 'Delivered'
    GROUP BY category )

SELECT 
    category,
    revenue,
    revenue * 1.0 / SUM(revenue) OVER() AS revenue_pct
FROM category_revenue
ORDER BY revenue DESC;

```


---

## 🔥 What I improved 

- Fixed **SQL formatting (indentation + readability)**
- Standardized **case ('Delivered', 'Cancelled')**
- Corrected **AOV precision issue**
- Fixed **wrong ORDER BY (month vs revenue)**


> “SQL was used to perform KPI validation, trend analysis, and uncover operational inefficiencies across regions and product categories.”


---

## ✨ Power Bi Implementation

#### 📊 Data Modeling (Star Schema)

- Fact Table: Sales Transactions
- Dimension Tables: Customers, Products, Date, Region

- ✅ Reduced redundancy
- ✅ Improved DAX performance
- ✅ Scalable architecture


## Dax Measures Utilized

```

----------------------------------------------------- Average Order Value ----------------------------------------------------- 

AOV = 
DIVIDE(
    [Net Revenue],
    [Delivered Orders] )


----------------------------------------------------- No of Orders ----------------------------------------------------- 


Total Orders = 
DISTINCTCOUNT('Ecommerce_data'[Order ID])


----------------------------------------------------- Total Revenue ----------------------------------------------------- 


Total Revenue = SUM(ecommerce_data[Amount])



----------------------------------------------------- Ranking by Category ----------------------------------------------------- 

Category Rank = 
RANKX(
    ALL(ecommerce_data[category]),
    [Category Revenue],
    ,
    DESC )


----------------------------------------------------- Revenue by Category ----------------------------------------------------- 


Category Revenue = 
CALCULATE(
    SUM(ecommerce_data[amount]),
    ecommerce_data[order_status] = "delivered" )



----------------------------------------------------- Orders Delivered ----------------------------------------------------- 


Delivered Orders = 
CALCULATE(
    DISTINCTCOUNT(Ecommerce_data[Order ID]),
    ecommerce_data[order_status] = "delivered" )



----------------------------------------------------- Net Revenue ----------------------------------------------------- 



Net Revenue = 
CALCULATE(
    SUM(ecommerce_data[Amount]),
    ecommerce_data[order_status] = "delivered" )



----------------------------------------------------- Products Returned ----------------------------------------------------- 


Return Rate = 
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(Ecommerce_data[Order ID]),
        ecommerce_data[order_status] = "returned"
    ),
    CALCULATE(
        DISTINCTCOUNT(Ecommerce_data[Order ID]),
        ecommerce_data[order_status] = "delivered" ) )


----------------------------------------------------- Cancellation rate ----------------------------------------------------- 

Cancellation Rate = 
DIVIDE(
    CALCULATE(
        COUNTROWS(Ecommerce_data),
        Ecommerce_data[order_status] = "cancelled"
    ),
    COUNTROWS(Ecommerce_data) )



----------------------------------------------------- Return % ----------------------------------------------------- 

Return_Rate_value = 
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(ecommerce_data[Order ID]),
        Ecommerce_data[Order_Status] = "Returned"
    ),
    DISTINCTCOUNT(ecommerce_data[Order ID]) )



----------------------------------------------------- Revenue % ----------------------------------------------------- 


Revenue % = 
DIVIDE(
    [Category Revenue],
    CALCULATE(
        [Category Revenue],
        ALL(ecommerce_data[category] ) ) )



```

---


## 📈 Business Performance Snapshot

- **Gross Sales:** ₹21M  
- **Net Revenue:** ₹20M  
- **Total Orders:** 28K  
- **Average Order Value (AOV):** ₹747  
- **Return Rate:** 3.56%  



---


## 📊 Decisions Executive Dashboard

 <img width="990" height="573" alt="dash" src="https://github.com/user-attachments/assets/8c8bbe22-2a17-4ea8-93c4-cc3bea4aca24" />



---

# 📊 Key Insights  

### 👥 Customer Intelligence
- Women contribute **64% of revenue**  
- Top segments: **Age 36–50 & 18–25**

> "👉 Revenue is highly concentrated → strong targeting opportunity"

---

### 🛍️ Product Performance  
- **Top Categories:** Set → ₹10.5M, Kurta → ₹5.0M, Western Dress → ₹3.1M  

- **Under Performers:** Blouse, Bottom

 > "👉 Clear product concentration → optimize inventory & marketing"

---

### ⚙️ Operational 
- **92.25% delivery success rate**  
- **low returns (3.56%)**  

⚠️ **High Cancellation Regions:**  
- Puducherry → 7.69%  
- Andaman & Nicobar → 6.94%  

>"👉 Localized logistics inefficiency"
---

### 📉 Seasonal Trends  
- Revenue declines after **Month 8 (August)**  

👉 Missed festive season leverage (Sep–Dec)

---

# 💡 Business Recommendations  

1. **🚚 Fix Operational Leakage**  
   - Improve delivery timelines in high-cancellation regions  
   - Introduce better fulfillment options (e.g., faster shipping / COD)

2. **📦 Product Optimization**  
   - Focus inventory on **Sets & Kurtas (high revenue drivers)**  
   - Bundle low-performing items via cross-selling  

3. **🎯 Target High-Value Customers**  
   - Focus on **women (36–50)** segment  
   - Launch campaigns during **festive months (Sep–Dec)**
   - Personalize offers marketing

4. **📈 Revenue Expansion**  
   - Use top categories to drive **Launch festive campaigns (Sep–Dec)**  
   - Increase average order value through bundling  

## 📌 Business Impact (Simulated)
- Potential **+8–12% revenue uplift** via targeting women segment
- Reduced cancellations by **2–3% with regional optimization**
- Improved AOV via bundling strategy

---

# 📂 Repository Structure

```

vrinda-ecommerce-analytics/
│
├── 📁 data/
│   ├── raw/
│   │   └── ecommerce_raw_data.csv
│   ├── processed/
│   │   └── ecommerce_cleaned_data.xlsx
│
├── 📁 sql/
│   ├── 01_data_cleaning.sql
│   ├── 02_kpi_calculations.sql
│   ├── 03_customer_analysis.sql
│   ├── 04_product_analysis.sql
│   ├── 05_operations_analysis.sql
│
├── 📁 powerbi/
│   ├── vrinda_dashboard.pbix
│   └── dax_measures.md
│
├── 📁 dashboard/
│   ├── overview.png
│   ├── sales_analysis.png
│   ├── customer_insights.png
│   ├── product_performance.png
│   └── operations_analysis.png
│
├── 📁 docs/
│   ├── data_dictionary.md
│   ├── business_problem.md
│   └── kpi_definitions.md
│
├── README.md


```
---


### ⚙️ Tech Stack  

| Tool | Purpose ⭐ |
|------|-----------|
| **Excel** | ETL, Power Query, Data Cleaning, Transformation |
| **SQL** | Deep analysis, Aggregations, calculations & segmentation |
| **Power BI**  | Data modeling, Dax measures, Dashboards, Charts & visualization |

---

# 👨‍💻 About Me  

Hi, I’m **A. Sai Arvind**, a Data Analyst Enthusiasd focused on building scalable, performance-driven analytical solutions.  

📧 saiarvind5081@gmail.com  
- 🔗 LinkedIn: www.linkedin.com/in/saiarvindofficial
- 🔗 GitHub: https://github.com/Sai-Arvind/

---

⭐ If you found this project useful, consider giving it a star!
