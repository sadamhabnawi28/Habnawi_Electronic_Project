# 📊 Habnawi's Electronic, Inc. — Sales Performance Dashboard

> An interactive Business Intelligence dashboard designed to analyze sales, revenue, and profitability performance of a fictional electronics retail company.

![Dashboard Preview](assets/dashboard-preview.png)

---

## 📌 Project Overview

**Habnawi's Electronic, Inc.** is a fictional electronics retail company that sells a variety of electronic products through online and offline sales channels across multiple countries.

This project focuses on transforming raw transactional sales data into an interactive **Executive Sales Performance Dashboard** using **Power BI**.

The dashboard provides management-level insights into:

- Overall sales performance
- Revenue and profitability
- Product category performance
- Online vs. offline sales contribution
- Profit trends over time
- Geographic profitability
- Performance across different time periods

The project demonstrates an end-to-end **Data Analytics and Business Intelligence workflow**, starting from data preparation and data modeling through to visualization and business insight generation.

---

## 🎯 Business Problem

As the business operates across multiple product categories, sales channels, and geographic markets, management needs a centralized way to monitor business performance.

Without an integrated analytical dashboard, several business questions become difficult to answer efficiently:

1. Which product categories generate the most revenue?
2. How much revenue comes from online and offline transactions?
3. How does profit change over time?
4. Which countries contribute the most to overall profit?
5. Are there specific periods with significant changes in profitability?
6. How can management monitor key business performance indicators in a single view?

Therefore, this project aims to develop an interactive dashboard that allows stakeholders to monitor business performance and explore sales patterns through data.

---

## 🔎 Research Questions

The analysis was designed to answer the following questions:

### Overall Performance

- What are the company's total Revenue, Profit, and Orders?
- How does overall business performance change over time?

### Product Performance

- Which product categories generate the highest revenue?
- Which categories contribute the least revenue?
- How is revenue distributed across product categories?

### Sales Channel

- How much revenue is generated through Online and Offline transactions?
- What is the contribution of each sales channel to total revenue?

### Time Analysis

- How does daily profit change over time?
- Are there periods with significant increases or decreases in profit?
- How does the moving average help identify the underlying profit trend?

### Geographic Performance

- Which countries contribute the most profit?
- How is profit distributed across different countries?

---

# 🗂️ Dataset

The project uses a **dummy transactional sales dataset** created for portfolio and analytical purposes.

The dataset represents sales transactions from an electronics retail business and contains information related to:

- Orders
- Products
- Product categories
- Customers
- Sales channels
- Countries
- Order dates
- Revenue
- Cost
- Profit

> **Note:** Habnawi's Electronic, Inc. is a fictional company, and the dataset is intended for educational and portfolio purposes only.

---

# 🧹 Data Preparation

Before developing the dashboard, the raw dataset was prepared to ensure that it could be used effectively for analysis.

The data preparation process included:

### 1. Data Cleaning

- Identified and handled missing values
- Checked data types
- Reviewed duplicate records
- Standardized categorical values
- Validated date fields
- Checked numerical fields for consistency

### 2. Data Transformation

The dataset was transformed to make it suitable for analytical reporting.

Key transformations included:

- Formatting date fields
- Creating calculated fields
- Preparing analytical dimensions
- Structuring transactional data
- Preparing data for time-series analysis

### 3. Data Modeling

A dimensional modeling approach was applied to organize the dataset for analytical purposes.

The model follows the principles of a **Star Schema**, separating transactional data from descriptive dimensions.

Example structure:

```text
                    Dim Date
                       |
                       |
Dim Product ---- Fact Sales ---- Dim Customer
                       |
                       |
                 Dim Geography
                       |
                       |
                 Dim Sales Channel