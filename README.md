# Habnawi's Electronic, Inc. - Sales Performance Dashboard

> This project demonstrates an end-to-end **Data Analytics and Business Intelligence workflow**, starting from data preparation and data modeling to visualization and business insight generation.

---

## 1. Business Understanding
### 1.1 Business Background

**Habnawi's Electronic, Inc.** is a fictional electronics retail company specializing in the sale of various electronic products and technology devices for home, office, and entertainment needs. The company offers a wide range of product categories, such as laptops, smartphones, televisions, computers, accessories, and home appliances from various renowned brands.

Over the past few years, the company has experienced an increase in the number of transactions; however, management still lacks a comprehensive overview of the factors that contribute most significantly to the company's revenue and profit.

---

### 1.2 Bussiness Problem

Management seeks to identify top-performing product categories, the effectiveness of individual sales channels, profit trends over time, and the countries contributing the highest profits. This information is essential for supporting strategic decision-making regarding product management, marketing, market expansion, and profitability enhancement.
To address these needs, an Executive Sales Dashboard has been developed; it presents Key Performance Indicators (KPIs) and interactive visualizations, enabling management to monitor business performance rapidly and on a data-driven basis.

---

### 1.3 Project Objectives

This project aims to develop an interactive dashboard that allows stakeholders to monitor business performance and explore sales patterns through data.

The dashboard provides management-level insights into:

- Overall sales performance
- Revenue and profitability
- Product category performance
- Online vs. offline sales contribution
- Profit trends over time
- Geographic profitability
- Performance across different time periods

---

### 1.4 Bussiness Questions

The analysis was designed to answer the following questions:

1. Overall Performance

    - What are the company's total Revenue, Profit, and Orders?
    - How does overall business performance change over time?

2. Product Performance

    - Which product categories generate the highest revenue?
    - Which categories contribute the least revenue?
    - How is revenue distributed across product categories?

3. Sales Channel

    - How much revenue is generated through Online and Offline transactions?
    - What is the contribution of each sales channel to total revenue?

4. Time Analysis

    - How does daily profit change over time?
    - Are there periods with significant increases or decreases in profit?
    - How does the moving average help identify the underlying profit trend?

5. Geographic Performance

    - Which countries contribute the most profit?
    - How is profit distributed across different countries?

---

## 2. Dataset Overview

This project uses an open source datasets that represents an electronic retail company. The datasets contains 3 main part with different file extension, Sales.csv, Product.txt, and Country.txt.

![Dataset Preview](images/dataset_preview.jpg)

---

## 3. Data Preparations

Raw data was prepared using **Power Query** in **Power BI** to ensure data quality and consistency before the analysis and visualization stages.

The data preparation process included:
- Data type validation - ensuring dates, numerical values, and categorical fields assigned appropriate data types.
- Data cleaning - identifying and handling missing, inconsistent, or invalid values.
- Column transformation - formatting and transforming existing fields to make them suitable for analysis.
- Data standardization - ensuring consistent values across categorical fields such as product categories, sales channels, and countries.
- Data validation - checking the transformed dataset to ensure that the resulting data was consistent and ready for modeling.
- Data preperation for modeling - structuring the cleaned dataset as the foundation for the subsequent data modeling and dashboard development stages.

For **Country** dataset, since it is in a .txt file without delimiters, it must first be converted using **Python** before undergoing data cleaning with **Power Query**.
This is the code to convert the **Country** dataset from .txt file to csv file:

```python
import pandas as pd

def preprocess():
    with open(file) as file:
        content = file.readlines()

    content = [line.strip() for line in content]
    content = [line.split() for line in content]

    storekeys = [line[0] for line in content][1:]

    countries = [
        " ".join(line[1:3])
        if line[1]  == "United" else line[1] 
        for line in content
        ][1:]

    states = [
        " ".join(line[3:])
        if line[1]  == "United" else " ".join(line[2:]) 
        for line in content
        ][1:]

    data = {
        "id":storekeys,
        "country": countries,
        "states": states
    }

    df = pd.DataFrame(data)
    df.to_csv("store.csv", index=False)
```
---

## 4. Data Modeling

The dataset was structured using a **dimensional data model** based on the **Star Schema** approach in Power BI. The model separates transactional data from descriptive attributes, allowing the dashboard to perform analysis across different business dimensions such as products, stores, and time.

The data model consists of:

- **Fact Table:** `Sales`
- **Dimension Tables:** `Products`, `Store`, and `Calendar`
- **Measure Table:** `Measure`

<img src="images/data_model.png" alt="Data Model" width="500">

---

### 4.1 Fact Table - Sales
The **Sales** table serves as the central fact table of the model. It contains transactional-level sales records and the foreign keys required to connect each transaction to the corresponding dimensions.

| Column          | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| `Sales Key`     | Unique identifier for each sales record                      |
| `Order Number`  | Identifier of the customer order                             |
| `Line Item`     | Identifies individual line items within an order             |
| `Order Date`    | Date when the order was placed                               |
| `Delivery Date` | Date when the order was delivered                            |
| `CustomerKey`   | Identifier linking sales transactions to customers           |
| `ProductKey`    | Foreign key linking transactions to the `Products` dimension |
| `StoreKey`      | Foreign key linking transactions to the `Store` dimension    |
| `Quantity`      | Number of products sold                                      |
| `Currency Code` | Currency associated with the transaction                     |

The Sales table acts as the many-side (*) of the relationships with the dimension tables because multiple sales transactions can belong to the same product, store, or date.

---

### 4.2 Dimension Table
#### 4.2.1 Products
The Products table contains descriptive information about the products sold by the company.
Important attributes include:

- ProductKey
- Product Name
- Brand
- Category
- CategoryKey
- Subcategory
- SubcategoryKey
- Color
- Unit Cost USD
- Unit Price USD

This dimension enables product-oriented analysis, such as:

- Revenue by category
- Revenue by product
- Revenue by brand
- Product performance
- Profitability by category or subcategory

Using a separate product dimension also prevents repetitive product descriptions from being stored in every transactional record.

---

#### 4.2.2 Store

The Store table contains descriptive information about the store or sales location associated with each transaction.

The table contains attributes such as:

- id
- country
- states
- IsOnline

These attributes allow the dashboard to analyze sales performance across different geographical and sales-channel dimensions.

For example, the IsOnline attribute can be used to distinguish between online and offline transactions, while country and states support geographical analysis.

---

#### 4.2.3 Calendar

The Calendar table serves as the date dimension of the model. Rather than relying directly on the date column in the Sales fact table for time-based analysis, a dedicated calendar table provides a consistent structure for temporal analysis and Power BI time-intelligence calculations.

The table contains fields such as:

- Date
- Day Name
- Month Name
- Quarter
- Week of Month
- Week of Year
- Year

---

### 4.3 Measure Table

The Measure table is a dedicated table used to organize and store DAX measures separately from the underlying data tables.

The current model contains measures such as:

- Revenue
```DAX
Revenue = SUMX(Sales, Sales[Quantity] * RELATED(Products[Unit Price USD]))
```
- Profit
```DAX
Profit = SUMX(Sales, Sales[Quantity] * (RELATED(Products[Unit Price USD]) - RELATED(Products[Unit Cost USD])))
```
- Moving Average (25 Days)
```DAX
Moving Average (25 Days) = 
AVERAGEX(
    DATESINPERIOD(
        'Calendar'[Date],
        MAX('Calendar'[Date]),
        -25,
        DAY
    ),
    [Profit]
)
```

These measures are not stored as physical columns in the transactional data. Instead, they are calculated dynamically using DAX based on the current filter context.

---

### 4.4 Relationships

The model uses one-to-many (1:*) relationships, where dimension tables represent the "one" side and the Sales fact table represents the "many" side.

| Dimension  | Key          | Fact Table Key | Cardinality | Purpose                       |
| ---------- | ------------ | -------------- | ----------- | ----------------------------- |
| `Store`    | `id`         | `StoreKey`     | 1:*         | Store & geographical analysis |
| `Products` | `ProductKey` | `ProductKey`   | 1:*         | Product analysis              |
| `Calendar` | `Date`       | `Order Date`   | 1:*         | Time-based analysis           |

---

## 5. Dashboard Overview

![Dashboard Preview](images/dashboard_preview.png)

## 6. Key Findings

### Finding 1 - Profitability reached a major peak around early 2020

![Daily Profit Review](images/prof_daily.png)

**Insight:**  
The company experienced a clear improvement in its underlying profitability from **2016** through **2019**, with the **20-day moving average** indicating a progressively higher profit baseline. However, daily profit remained highly volatile, with several significant spikes suggesting that profitability was influenced by short-term business events or changes in sales mix. Profitability reached its highest observed level around early **2020**, followed by a sustained decline in the underlying profit trend throughout much of **2020**. A modest recovery became visible entering 2021, although profitability had not returned to its previous peak.

**Why It Matters:**  
This pattern indicates that the company's profitability has not been constant over time and that the period around the **2020** peak represents an important performance inflection point. For management, the key issue is not simply identifying high- or low-profit days, but understanding the business drivers behind changes in the underlying profitability trend. Further analysis should connect profit movements with revenue, product mix, sales channels, geography, transaction volume, and promotional activity to determine whether changes in profitability were driven by sales growth, category mix, channel performance, or other operational factors.

---

### Finding 2 - Revenue is strongly concentrated in a small number of product categories

![Revenue By Category](images/rev_cat.png)

| Category                      | Revenue  | Profit Margin |
| ------------------------------| ---------| --------------|
| Computers                     | $19.30 M | 58.4%        |
| Home Appliances               | $10.80 M | 58.3%        |
| Cameras and camcorders        | $6.52 M  | 60.1%        |
| Cell phones                   | $6.18 M  | 56.6%        |
| TV and Video                  | $5.93 M  | 59.7%        |
| Audio                         | $3.17 M  | 57.7%        |
| Music, Movies and Audio Books | $3.13 M  | 61.0%        |
| Games and Toys                | $0.72 M  | 54.7%        |


**Insight:**   
`Computers` is the largest revenue contributor at **$19.30M (34.6%)**, followed by `Home Appliances` at **$10.80M (19.4%)**. Together, these two categories account for approximately **54%** of total revenue, indicating that overall sales performance is highly influenced by their performance. Meanwhile, `Cameras and camcorders`, `Cell phones`, and `TV and Video` each contribute approximately **10–12%**, providing additional but smaller revenue streams. At the lower end, Games and Toys contributes only **1.3%**, making it the smallest revenue-generating category.

Profit margins across product categories range from **54.7% to 61.0%**, indicating relatively consistent profitability across the portfolio. `Music, Movies and Audio Books` records the highest margin at **61.0%**, followed by `Cameras and camcorders` at **60.1%** and `TV and Video` at **59.7%**. Meanwhile, `Games and Toys` has the lowest margin at **54.7%**.


**Why it matters**:   
`Computers` and `Home Appliances` represents significant source of both revenue and profit. Together these categories contributes a substantial portion of the company's overall financial performance. Because a substantial portion of company revenue and estimated profit comes from these categories, changes in its sales performance can have a meaningful impact on overall business results. From a business perspective, management needs to monitor these categories not only in terms of sales growth but also margin stability, inventory availability, product mix, and demand trends to ensure that growth does not come at the expense of profitability.

On the other hand, the revenue distribution suggests that management should simultaneously protect the performance of the company's major revenue drivers while investigating growth opportunities and underlying performance factors in lower-contributing categories.

The high profit margin of `Music, Movies and Audio Books` indicates that the category generates a relatively large amount of profit from each dollar of revenue. However, its relatively small revenue contribution limits its impact on the company's total profit. From a business perspective, this creates a potential growth opportunity. If the company can increase sales in this category while maintaining its current margin level, the category could make a larger contribution to overall profitability. Management could therefore investigate whether the category's relatively low revenue is driven by limited product assortment, lower customer demand, distribution reach, or sales volume.

The low contribution of `Games and Toys` to both revenue and profit margin creates a need to understand the underlying causes of the category's performance before deciding how it should be managed. If the performance is caused by limited demand, the company may need to reconsider its product strategy. If it is caused by limited assortment, distribution, or promotional exposure, there may be opportunities to improve performance. The key business consideration is therefore whether the category represents a growth opportunity or a relatively low-priority segment based on its potential and underlying economics.

---

### Finding 3 - The channel mix indicates different roles within the revenue portfolio

![Revenue By Channel](images/rev_chan.png)

**Insight:**   
The `Offline` sales is the company's dominant revenue channel, generating approximately **$44.35M** or **79.5%** of total revenue, compared with **$11.40M** or **20.5%** from `online` transactions. This means the company currently relies heavily on its `offline` channel as its primary revenue engine, with `offline` revenue approximately 3.9 times larger than `online` revenue.

**Why It Matters**:   
The strong concentration of revenue in the `offline` channel means that `offline` performance has a substantially greater impact on the company's overall financial performance. A **10%** change in `offline` revenue would represent approximately **$4.44M**, compared with **$1.14M** for an equivalent change in `online` revenue. At the same time, the **$11.40M** contribution from `online` transactions indicates that digital sales already represent a meaningful component of the company's revenue portfolio. Therefore, channel performance should be evaluated not only based on revenue contribution, but also in terms of profitability, customer behavior, transaction volume, and operating economics to understand the role and business value of each channel.

---

### Finding 4 - The United States is the dominant profit market

![Profit By Country Preview](images/prof_count.png)

**Insight:**   
The company generated approximately **$25.99M** in profit across eight countries, with `the United States` contributing **$13.92M** or **53.6%** of total profit. This makes the `US` the company's dominant geographic profit engine and indicates a significant concentration of profitability in a single market. The `United Kingdom`, `Germany`, and `Canada` form a meaningful secondary profit base, collectively contributing approximately **30.6%** of total profit. Meanwhile, `Australia`, `Italy`, `the Netherlands`, and `France` each contribute less than **5%** individually.

Profit margins across the eight markets are remarkably consistent, ranging from **58.26%** in `Canada` to **59.24%** in `Australia`, representing a relatively narrow spread of approximately **0.98** percentage points. `Australia` records the highest observed profit margin at **59.24%**, followed by France at **58.98%** and `the Netherlands` at **58.93%**. Meanwhile, `Canada` records the lowest margin at **58.26%**. Despite these differences, the relatively narrow margin range indicates that geographic differences in absolute profit are not primarily explained by substantial variations in margin. This becomes particularly evident when comparing `the United States` and `Australia`. `The United States` generates approximately **$13.92M** in profit, compared with **$1.24M** in `Australia`, despite their margins being relatively close at **58.58%** and **59.24%**, respectively. This suggests that business scale and revenue volume play a much larger role in determining absolute profit contribution than small differences in margin.

**Why It Matters:**   
The concentration of profit in the `United States` means that its performance has a substantial impact on overall company profitability. At the same time, absolute profit alone does not indicate market efficiency or growth potential. Further analysis combining country, revenue, profit margin, product category, channel, and time trends is required to understand the underlying drivers of geographic profitability.

---

## 7. Strategic Recommendations

### 7.1 Product Category Strategy

1. Protect and Strengthen the Core Revenue Engines   

`Computers` and `Home Appliances` should remain key priorities because they collectively generate approximately **54% of total revenue** and represent a substantial share of estimated profit. Management should focus on maintaining product availability, optimizing inventory, monitoring product-level profitability, and developing targeted promotions. Because of their large revenue base, relatively small improvements in these categories can have a meaningful impact on overall business performance. For example, a **10%** increase in Computers revenue at the current margin would represent approximately **$1.93M in additional revenue** and around **$1.13M in additional profit**.

2. Scale High-Margin Categories   

`Cameras and camcorders`, `TV and Video`, and `Music, Movies and Audio Books` demonstrate relatively strong profit margins. The company should explore opportunities to increase their revenue contribution through broader product assortment, targeted marketing, cross-selling, product bundling, and improved channel exposure while maintaining margin discipline. The objective is to convert strong category-level profitability into greater absolute profit contribution.

3. Improve Margin in High-Revenue Categories   

`Cell Phones` generates approximately **$6.18M in revenue** but has a comparatively lower profit margin of **56.58%**. Rather than focusing exclusively on increasing sales volume, management should investigate pricing, discounting, product mix, and brand-level profitability. Cross-selling accessories and complementary products can also increase revenue and profit per transaction. A 1 percentage-point improvement in margin on the current revenue base would represent approximately **$61.8K in additional profit**, assuming revenue remains constant.

4. Investigate Underperforming Categories   

`Games and Toys` has the lowest revenue and lowest profit margin in the sales portfolio. Before making major portfolio decisions, management should investigate the underlying drivers of its performance, including sales volume, SKU availability, pricing, promotional exposure, inventory turnover, and seasonality. The objective is to determine whether the category represents an opportunity for improvement or should receive a lower level of strategic investment.

5. Develop Cross-Selling and Bundling Opportunities   

The company can increase customer basket value by creating complementary product bundles across categories.

Examples include:

- Computers + accessories
- Smartphones + accessories
- TVs + audio equipment
- Cameras + memory cards and accessories

This strategy can increase revenue per transaction while reducing reliance on customer acquisition as the sole driver of revenue growth.

---

### 7.2 Sales Channel Strategy

The company's revenue is currently highly concentrated in the offline channel, which contributes approximately 79.5% of total revenue, while the online channel contributes 20.5%. Therefore, the strategic priority should not be to replace the offline channel with online, but to protect the existing offline revenue base while developing online as a scalable growth channel.

1. The company should protect and optimize its offline revenue engine because changes in offline performance have a substantially larger impact on total revenue. Operational initiatives should focus on maintaining store productivity, product availability, customer experience, and performance across locations.

2. The company should develop the online channel as a growth engine. With approximately $11.40M in revenue, online sales already represent a meaningful part of the business and provide a foundation for further digital growth. However, online expansion should be evaluated based on profitability and customer economics rather than revenue growth alone.

3. The company should adopt an omnichannel strategy that connects online and offline customer journeys. Initiatives such as Click & Collect, Ship From Store, unified loyalty programs, online-to-offline engagement, and offline-to-online customer acquisition can allow both channels to complement rather than compete with each other.

4. Management should establish channel-level performance monitoring covering revenue, profit, margin, AOV, customer acquisition cost, conversion rate, repeat purchase, and customer lifetime value. This would enable the company to distinguish genuine incremental online growth from revenue that is simply shifting from offline to online.

---

### 7.3 Geographic Strategy

Geographic strategy should focus on protecting the United States as the company's core profit engine while developing secondary markets through sustainable, margin-conscious growth. Given the relatively narrow 58–59% profit margin range across countries, differences in absolute profit appear to be driven more by business scale than by major margin variations. Therefore, management should prioritize profitable revenue growth, maintain margin discipline, investigate the drivers behind high-margin markets such as Australia, France, and the Netherlands, and strengthen the performance of meaningful secondary markets such as the UK, Germany, and Canada.

---
