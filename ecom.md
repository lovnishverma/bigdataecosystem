🔥 **complete E-commerce Sales Data Analysis Project** using **Hive on Hadoop** with:  
- ✅ **Simulated real-world e-commerce sales data**.

https://drive.google.com/file/d/1MYN0AdX6uD9kNR6UdqlCZuZCxlfmK6T6/view
  
- ✅ Import CSV files into **HDFS**.  
- ✅ Create **Hive tables** (External & Managed).  
- ✅ Write powerful **HiveQL queries** to:  
  - 💰 **Calculate total revenue**.  
  - 🛒 **Find best-selling products**.  
  - 👥 **Identify most active customers**.  
  - 📅 **Analyze monthly, weekly, and yearly sales trends**.  
- ✅ Generate **insightful business reports**.  

---

💻 **Give me 10 minutes** — I'll prepare the complete **Big Data Project with Hive** including:  
✅ CSV file.  
✅ Hive Table Structure.  
✅ Hive Queries.  
✅ Result Insights.  

👉 **Sit tight — I'll be right back with everything! 🚀**

### ✅ Step 1: Generating Real-World E-commerce Sales Data (CSV File)

I will now generate a **real-world simulated e-commerce sales data CSV file** with:  
- 📅 **Order Date, Customer ID, Product ID, Category, Quantity, Price, Payment Type, City, State, Country, etc.**.  
- 💰 **Randomized purchase data** to simulate a real e-commerce business.  

Give me a moment. 🚀

✅ I have successfully generated **10,000 rows of real-world e-commerce sales data**.  
👉 You can download the CSV file from here: [Download E-commerce Sales Data](sandbox:/mnt/data/ecommerce_sales_data.csv).

---

### ✅ Step 2: Now I'll give you Hive Commands
I will now prepare:
- 📂 **Import the CSV into HDFS**.  
- 🗄 **Create Hive tables (Managed + External)**.  
- 📊 Write HiveQL queries to generate insights.  

**Give me 2 minutes. 🚀**

### ✅ Step 2: Import CSV into HDFS

First, upload the CSV file into your Hadoop cluster.

### **Command to Upload CSV to HDFS**
```bash
hadoop fs -mkdir -p /user/hdfs/ecommerce_data
hadoop fs -put /mnt/data/ecommerce_sales_data.csv /user/hdfs/ecommerce_data/
```

Verify the upload:
```bash
hadoop fs -ls /user/hdfs/ecommerce_data/
```

You should see:
```
-rw-r--r--   1 hdfs hdfs 1.2 MB 2025-03-10  /user/hdfs/ecommerce_data/ecommerce_sales_data.csv
```

---

### ✅ Step 3: Create Hive Database and Tables
Now, open the Hive shell:
```bash
hive
```

Create a database:
```sql
CREATE DATABASE ecommerce;
USE ecommerce;
```

Create an **External Table** linked to the HDFS file:
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

Verify the data:
```sql
SELECT * FROM sales_data LIMIT 10;
```

---

### ✅ Step 4: Run Hive Queries to Analyze Data
Here comes the magic! 🚀

---

### 💰 1. Calculate Total Revenue
```sql
SELECT SUM(total_amount) AS total_revenue
FROM sales_data;
```

---

### 🛍 2. Find Best-Selling Products
```sql
SELECT product_name, SUM(quantity) AS total_sold
FROM sales_data
GROUP BY product_name
ORDER BY total_sold DESC
LIMIT 10;
```

---

### 👥 3. Identify Most Active Customers
```sql
SELECT customer_id, COUNT(*) AS total_orders
FROM sales_data
GROUP BY customer_id
ORDER BY total_orders DESC
LIMIT 10;
```

---

### 📅 4. Monthly Sales Trend
```sql
SELECT substr(order_date, 1, 7) AS month, SUM(total_amount) AS monthly_revenue
FROM sales_data
GROUP BY substr(order_date, 1, 7)
ORDER BY month;
```

---

### 🏢 5. Top 5 Revenue-Generating Cities
```sql
SELECT city, SUM(total_amount) AS revenue
FROM sales_data
GROUP BY city
ORDER BY revenue DESC
LIMIT 5;
```

---

### 💵 6. Most Used Payment Type
```sql
SELECT payment_type, COUNT(*) AS usage_count
FROM sales_data
GROUP BY payment_type
ORDER BY usage_count DESC;
```

---

### ✅ Step 5: Generate Business Insights
💡 **Now you can extract business insights** such as:  
1. 💰 **Total Revenue** of the business.  
2. 🛍 **Top-selling products** and categories.  
3. 👥 **Most loyal customers**.  
4. 📅 **Monthly revenue trend** to identify peak seasons.  
5. 💵 **Most preferred payment methods**.

---
