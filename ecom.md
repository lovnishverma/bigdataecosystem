# 🚀 E-commerce Sales Data Analysis Using Hive

## 📊 Project Overview
This project demonstrates how to perform **E-commerce Sales Data Analysis** using **Apache Hive** on a Hadoop ecosystem. The goal of this project is to analyze sales data, generate business insights, and understand trends in e-commerce sales.

The project uses a **CSV file containing real-world simulated sales data**, which is imported into **HDFS (Hadoop Distributed File System)** and processed using **HiveQL (Hive Query Language)**.

✅ **Project Objectives:**
- Import large-scale e-commerce sales data into **HDFS**.
- Create Hive tables (Managed & External).
- Analyze data to extract business insights like:
  - 💰 **Total Revenue.**
  - 🛒 **Best-selling products.**
  - 👥 **Most active customers.**
  - 📅 **Monthly/Yearly sales trends.**
  - 💵 **Most used payment methods.**
- Generate useful business insights for decision-making.

---

## 📁 Dataset Information
The dataset used in this project is a simulated **E-commerce Sales Data CSV file** containing the following columns:

| Column Name      | Description                            |
|-----------------|------------------------------------------|
| **order_date**   | Date of the order                      |
| **customer_id**  | Unique ID of the customer               |
| **product_name** | Name of the product purchased           |
| **category**     | Product category                        |
| **quantity**     | Number of units sold                    |
| **price**        | Price per unit                          |
| **total_amount** | Total amount for the order              |
| **payment_type** | Payment method used                     |
| **city**         | Customer's city                         |
| **state**        | Customer's state                        |
| **country**      | Customer's country                      |

👉 **Sample Size:** 10,000 records of e-commerce transactions.
👉 **File Type:** CSV
👉 **File Name:** `ecommerce_sales_data.csv`

You can download the dataset from here: [Download E-commerce Sales Data](https://drive.google.com/file/d/1MYN0AdX6uD9kNR6UdqlCZuZCxlfmK6T6/view)

---

## 📥 Step 1: Upload Data to HDFS
### ✅ Create Directory in HDFS
Run the following commands to create a directory in **HDFS**:
```bash
hadoop fs -mkdir -p /user/hdfs/ecommerce_data
```

### ✅ Upload the CSV File to HDFS
```bash
hadoop fs -put /mnt/data/ecommerce_sales_data.csv /user/hdfs/ecommerce_data/
```

Verify the upload:
```bash
hadoop fs -ls /user/hdfs/ecommerce_data/
```
You should see the file listed there.

---

## 🗄 Step 2: Create Hive Tables
Now, open the **Hive shell**:
```bash
hive
```

### ✅ Create Database
```sql
CREATE DATABASE ecommerce;
USE ecommerce;
```

### ✅ Create External Table
We will create an **External Table** linked to our HDFS file.
```sql
CREATE EXTERNAL TABLE IF NOT EXISTS sales_data (
    order_date STRING,
    customer_id INT,
    product_name STRING,
    category STRING,
    quantity INT,
    price FLOAT,
    total_amount FLOAT,
    payment_type STRING,
    city STRING,
    state STRING,
    country STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hdfs/ecommerce_data/';
```

✅ **Verify the data:**
```sql
SELECT * FROM sales_data LIMIT 10;
```

---

## 💻 Step 3: Hive Queries (Data Analysis)
### 💰 1. Calculate Total Revenue
```sql
SELECT SUM(total_amount) AS total_revenue
FROM sales_data;
```
👉 This query shows the **total revenue generated** by the business.

---

### 🛍 2. Find Best-Selling Products
```sql
SELECT product_name, SUM(quantity) AS total_sold
FROM sales_data
GROUP BY product_name
ORDER BY total_sold DESC
LIMIT 10;
```
👉 This query shows the **top 10 best-selling products**.

---

### 👥 3. Identify Most Active Customers
```sql
SELECT customer_id, COUNT(*) AS total_orders
FROM sales_data
GROUP BY customer_id
ORDER BY total_orders DESC
LIMIT 10;
```
👉 This query identifies the **top 10 most active customers**.

---

### 📅 4. Monthly Sales Trend
```sql
SELECT substr(order_date, 1, 7) AS month, SUM(total_amount) AS monthly_revenue
FROM sales_data
GROUP BY substr(order_date, 1, 7)
ORDER BY month;
```
👉 This query shows the **monthly revenue trend**.

---

### 🏢 5. Top Revenue-Generating Cities
```sql
SELECT city, SUM(total_amount) AS revenue
FROM sales_data
GROUP BY city
ORDER BY revenue DESC
LIMIT 5;
```
👉 This query identifies the **top 5 revenue-generating cities**.

---

### 💵 6. Most Used Payment Type
```sql
SELECT payment_type, COUNT(*) AS usage_count
FROM sales_data
GROUP BY payment_type
ORDER BY usage_count DESC;
```
👉 This query shows the **most preferred payment methods**.

---

## 📊 Step 4: Visualization (Optional)
You can visualize the data using:
- 📊 **Apache Zeppelin**.
- 📊 **Power BI / Tableau**.
- 💻 **Python (Matplotlib/Seaborn)**.

Example visualization in **Zeppelin:**
```sql
%sql
SELECT substr(order_date, 1, 7) AS month, SUM(total_amount) AS monthly_revenue
FROM sales_data
GROUP BY substr(order_date, 1, 7)
ORDER BY month;
```
👉 Convert it into a **Line Chart** to see monthly revenue.

---

## 📜 Step 5: Business Insights
| Insight | Description |
|---------|-------------|
| 💰 Total Revenue | Understand the overall revenue generated. |
| 🛍 Best-Selling Products | Identify which products are most popular. |
| 👥 Most Active Customers | Track the most loyal customers. |
| 📅 Monthly Revenue Trend | Understand peak seasons and off-seasons. |
| 🏢 Revenue by City | Focus on cities generating maximum revenue. |
| 💵 Payment Preference | Identify the most used payment method. |

---

## 📊 Future Scope
1. ✅ **Integrate Apache Kafka** for real-time streaming data.
2. ✅ Use **Apache Spark** to process data faster.
3. ✅ Build a **Tableau/Power BI dashboard** for live business insights.
4. ✅ Connect Hive data to **Flask/Django web app**.

---

## 💎 Conclusion
This project provides a practical demonstration of:
- ✅ **Big Data Processing** using Hive.
- ✅ Importing data into HDFS.
- ✅ Performing data analysis using HiveQL.
- ✅ Generating business insights from e-commerce sales data.

👉 **Next Step:**:
- ✅ Create a real-time dashboard using Zeppelin/Power BI?
- ✅ Automate PDF Report Generation using Python?
- ✅ Deploy this project on a web application using Flask?
