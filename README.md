# 🏬 Kultra Mega Stores (KMS) — SQL Business Intelligence Analysis

![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white)
![Data Analysis](https://img.shields.io/badge/Business%20Intelligence-Analysis-blue?style=flat-square)
![Status](https://img.shields.io/badge/Project%20Stage-Completed-success?style=flat-square)

---

## 📖 Project Overview

**Kultra Mega Stores (KMS)** is a Lagos-based retail and wholesale company specializing in **office supplies and furniture**.  
The organization serves **individual consumers, small businesses, and large corporate clients** across Nigeria.

The two datasets used in this project are:

1. **OrderRecords**

RowID | OrderID | OrderDate | OrderPriority | OrderQuantity | Sales | Discount | ShipMode |
Profit | UnitPrice | ShippingCost | CustomerName | Province | Region | CustomerSegment |
ProductCategory | ProductSubCategory | ProductName | ProductContainer | ProductBaseMargin | ShipDate


2. **OrderStatus**

OrderID | Status

## 🎯 Objective

To analyze sales, profit, and shipping data to uncover key business insights and answer strategic questions posed by management.  
All analysis was conducted using **SQL Server (T-SQL)**.

---

## 🧩 Project Stages

| Stage | Description |
|-------|--------------|
| **1. Data Understanding** | Review of provided tables, column structure, and data relationships. |
| **2. Data Cleaning** | Ensured no duplicate orders, checked for missing values, corrected data types (dates, numeric). |
| **3. Data Modeling** | Joined `OrderRecords` and `OrderStatus` via `OrderID` for full analysis scope. |
| **4. Exploratory Analysis** | Performed sales, profit, and shipping cost aggregations. |
| **5. Business Insights** | Addressed management’s key analytical questions. |
| **6. Recommendations** | Proposed strategies to increase sales, optimize shipping, and enhance customer retention. |

---

## 🧮 Case Scenario I — Sales & Shipping Performance

### **1️⃣ Which product category had the highest sales?**

SELECT TOP 1 
 ProductCategory,
 SUM(Sales) AS TotalSales
FROM OrderRecords
GROUP BY ProductCategory
ORDER BY TotalSales DESC;

### **2️⃣ Top 3 and Bottom 3 regions in terms of sales**

-- Top 3 Regions
SELECT TOP 3 
    Region,
    SUM(Sales) AS TotalSales
FROM OrderRecords
GROUP BY Region
ORDER BY TotalSales DESC;

-- Bottom 3 Regions
SELECT TOP 3 
    Region,
    SUM(Sales) AS TotalSales
FROM OrderRecords
GROUP BY Region
ORDER BY TotalSales ASC;

### **3️⃣ What were the total sales of appliances in Ontario?**

SELECT 
    SUM(Sales) AS TotalAppliancesSales_Ontario
FROM OrderRecords
WHERE ProductSubCategory = 'Appliances'
  AND Province = 'Ontario';

### **4️⃣ Advise the management of KMS on what to do to increase the revenue from the bottom 10 customers**


SELECT TOP 10 
    CustomerName,
    CustomerSegment,
    SUM(Sales) AS TotalSales,
    COUNT(DISTINCT OrderID) AS NumberOfOrders,
    AVG(Sales) AS AvgOrderValue
FROM OrderRecords
GROUP BY CustomerName, CustomerSegment
ORDER BY TotalSales ASC;

**Recommendations**

* Launch loyalty and discount programs for low-spending customers.
* Upsell and cross-sell complementary products.
* Improve follow-up and customer engagement through personalized marketing.
* Offer subscription or contract-based supply options for small businesses.

### **5️⃣ KMS incurred the most shipping cost using which shipping method?**

SELECT TOP 1 
    ShipMode,
    SUM(ShippingCost) AS TotalShippingCost
FROM OrderRecords
GROUP BY ShipMode
ORDER BY TotalShippingCost DESC;

## 📊 Case Scenario II — Customer and Profitability Insights

### **6️⃣ Who are the most valuable customers, and what products or services do they typically purchase?**

WITH TopCustomers AS (
    SELECT TOP 10
        CustomerName,
        CustomerSegment,
        SUM(Sales) AS TotalSales,
        SUM(Profit) AS TotalProfit
    FROM OrderRecords
    GROUP BY CustomerName, CustomerSegment
    ORDER BY TotalSales DESC
)
SELECT 
    t.CustomerName,
    t.CustomerSegment,
    o.ProductCategory,
    SUM(o.Sales) AS CategorySales
FROM TopCustomers t
JOIN OrderRecords o
    ON t.CustomerName = o.CustomerName
GROUP BY t.CustomerName, t.CustomerSegment, o.ProductCategory
ORDER BY t.CustomerName, CategorySales DESC;

### **7️⃣ Which small business customer had the highest sales?**

SELECT TOP 1 
    CustomerName,
    SUM(Sales) AS TotalSales
FROM OrderRecords
WHERE CustomerSegment = 'Small Business'
GROUP BY CustomerName
ORDER BY TotalSales DESC;

### **8️⃣ Which corporate customer placed the most number of orders (2009–2012)?**

SELECT TOP 1 
    CustomerName,
    COUNT(DISTINCT OrderID) AS NumberOfOrders
FROM OrderRecords
WHERE CustomerSegment = 'Corporate'
  AND YEAR(OrderDate) BETWEEN 2009 AND 2012
GROUP BY CustomerName
ORDER BY NumberOfOrders DESC;

### **9️⃣ Which consumer customer was the most profitable one?**

SELECT TOP 1 
    CustomerName,
    SUM(Profit) AS TotalProfit
FROM OrderRecords
WHERE CustomerSegment = 'Consumer'
GROUP BY CustomerName
ORDER BY TotalProfit DESC;

### **🔟 Which customer returned items, and what segment do they belong to?**

SELECT DISTINCT 
    o.CustomerName,
    o.CustomerSegment,
    s.Status
FROM OrderRecords o
JOIN OrderStatus s
    ON o.OrderID = s.OrderID
WHERE s.Status = 'Returned';

### **1️⃣1️⃣ Was shipping cost appropriately spent based on Order Priority?**

SELECT 
    OrderPriority,
    ShipMode,
    COUNT(OrderID) AS NumberOfOrders,
    AVG(ShippingCost) AS AvgShippingCost,
    AVG(Sales) AS AvgOrderValue
FROM OrderRecords
GROUP BY OrderPriority, ShipMode
ORDER BY OrderPriority, AvgShippingCost DESC;

**Interpretation:**

* ✅ *High/Critical* orders should use **Express Air** (fast but costly).
* ✅ *Medium/Low* orders should use **Delivery Truck** (slow but cheaper).
* ❌ If many low-priority orders use **Express Air**, shipping cost is being misallocated.

---

## 📈 Key Insights & Recommendations

| Area                 | Finding                                      | Recommendation                               |
| -------------------- | -------------------------------------------- | -------------------------------------------- |
| **Product Category** | Top category drives major revenue            | Increase inventory, supplier contracts       |
| **Regions**          | Some regions underperform                    | Strengthen marketing, logistics routes       |
| **Customers**        | Few top customers generate majority of sales | Offer loyalty programs & exclusive discounts |
| **Shipping**         | Certain methods costly                       | Align shipping mode with order priority      |
| **Returns**          | Returned items from specific segments        | Review product quality & delivery efficiency |

---

## 🧠 Tools & Technologies

| Tool                 | Purpose                            |
| -------------------- | ---------------------------------- |
| **SQL Server**       | Data extraction and analysis       |
| **GitHub**           | Version control and documentation  |

---

## 📚 Author

**👤 Analyst:** *Emmanuel Philip*  
GitHub: [Damiskill](https://github.com/Damiskill)
📧 [emmanuelphilip685d@gmail.com]
💼 [LinkedIn Profile](https://linkedin.com/in/PhilipEmmanuel)
**📅 Duration:** 2009 – 2012 dataset
**📍 Location:** Lagos, Nigeria

---

⭐ **If you found this project useful, don’t forget to give it a star!**
