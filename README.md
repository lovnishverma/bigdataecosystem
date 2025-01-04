![image](https://github.com/user-attachments/assets/2b0a8b29-8287-446a-8a0c-8c1820ea0971) ![image](https://github.com/user-attachments/assets/343cfd7e-73b7-4eb2-a9a4-76c31f5703c8)  ![image](https://github.com/user-attachments/assets/04ad8a37-c3a0-4e62-a5c4-70c023992209)![image](https://github.com/user-attachments/assets/5a5fc24a-bc9d-4cc2-aab4-b651c59197d5)  ![image](https://github.com/user-attachments/assets/10b26b1e-614f-4ad7-966c-505e54825680)



# Docker Multi-Container Environment with Hadoop, Spark, and Hive

This guide helps you set up a multi-container environment using Docker for Hadoop (HDFS), Spark, and Hive. The setup is lightweight, without the large memory requirements of a Cloudera sandbox.

## **Prerequisites**

Before you begin, ensure you have the following installed:

- **Docker**: [Install Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

- IMPORTANT:
  ******- Enable the "Expose daemon on tcp://localhost:2375 without TLS" option if you're using Docker Desktop for compatibility.******

 - **Git**: [Download Git](https://git-scm.com/downloads/win)
   - Git is used to download the required files from a repository.
  
   Create a newfolder and open it in terminal or go inside it using CD Command
  
 ![image](https://github.com/user-attachments/assets/28602a4b-52e2-4265-bfb5-a08301fda7b8)


## **Step 1: Clone the Repository**

First, clone the GitHub repository that contains the necessary Docker setup files.

```bash
git clone https://github.com/lovnishverma/bigdataecosystem.git
```
 
[or Directly download zip from my repo](https://github.com/lovnishverma/BigDataecosystem)

Navigate to the directory:

```bash
cd bigdataecosystem
```

![image](https://github.com/user-attachments/assets/e4d6a8ab-3f36-424a-bf13-9402bc1c13a2)

if downloaded zip than cd bigdataecosystem-main

## **Step 2: Start the Cluster**

Use Docker Compose to start the containers in the background.

```bash
docker-compose up -d
```

This command will launch the Hadoop, Spark, and Hive containers.

![image](https://github.com/user-attachments/assets/8dc3ec44-84af-40f2-8056-92e5f3449919)


## **Step 3: Verify Running Containers**

To check if the containers are running, use the following command:

```bash
docker ps
```
![image](https://github.com/user-attachments/assets/f6897172-d14f-462a-95dd-ba46401b5dd7)


## **Step 4: Stop and Remove Containers**

When you are done, stop and remove the containers with:

```bash
docker-compose down
```
![image](https://github.com/user-attachments/assets/fd1f2298-7d65-4055-a929-12de4d01c428)


## **Step 5: Set Up HDFS**

### **Upload Files to HDFS**

To copy a file (e.g., `police.csv`) to the Hadoop cluster:

1. Copy the file into the namenode container:
    ```bash
    docker cp police.csv namenode:/police.csv
    ```
![image](https://github.com/user-attachments/assets/496c7e6a-41d6-44d2-9557-b6004fe986c4)


2. Access the namenode container's bash shell:
    ```bash
    docker exec -it namenode bash
    ```
![image](https://github.com/user-attachments/assets/d501a9b3-d2d9-4e2d-aecb-8e3eb7ccf678)


3. Create a directory in HDFS and upload the file:
    ```bash
    hdfs dfs -mkdir -p /data/crimerecord/police
    hdfs dfs -put /police.csv /data/crimerecord/police/
    ```
![image](https://github.com/user-attachments/assets/ab68bba9-92f2-4b15-a50e-f3ee1a0f998e)


## **Step 6: Set Up Spark**

### **Access the Spark Master UI**

- Open `http://localhost:8080` in your web browser to view the Spark Master UI.

- ![image](https://github.com/user-attachments/assets/a824047c-f50c-494c-8429-d2bebd58870b)

**You can monitor processes here**

- ![image](https://github.com/user-attachments/assets/8fa7e525-d601-4dad-b5b4-0477d47ec4dd)

### **Start Spark Shell**

To interact with Spark, start the Spark shell in the master container:

```bash
docker exec -it spark-master bash

spark/bin/spark-shell --master spark://spark-master:7077
```

![image](https://github.com/user-attachments/assets/45765d5e-b1e7-4726-a60c-ddd5dd278c93)

![image](https://github.com/user-attachments/assets/b071335b-4928-491a-8bed-321995881d83)


### **Process Data in Spark**

To load and view data from HDFS using Spark:

```scala
val df = spark.read.csv("hdfs://namenode:9000/data/crimerecord/police/police.csv")
df.show()
```
![image](https://github.com/user-attachments/assets/4101596d-da55-4cd2-b4de-02a8f7f0299a)






![image](https://github.com/user-attachments/assets/fada1eec-5349-4382-8d1a-96940c124064)

## **Step 7: Set Up Hive** 

### **Start Hive Server**

Access the Hive container and start the Hive Server:

```bash
docker exec -it hive-server bash
```

```bash
hive
```

Check if Hive is listening on port 10000:
![image](https://github.com/user-attachments/assets/dc1e78d4-d903-4ac5-9eaa-eff0b893d6fb)


```bash
netstat -anp | grep 10000
```
![image](https://github.com/user-attachments/assets/9ac08fd3-f515-448d-83b3-c620fa3b15c2)


### **Connect to Hive Server**

Use Beeline to connect to the Hive server:

```bash
beeline -u jdbc:hive2://localhost:10000 -n root
```
![image](https://github.com/user-attachments/assets/d2dce309-0334-4a64-b8df-8cb6206b1432)


Alternatively, use the following command for direct connection:

```bash
beeline
```

```bash
!connect jdbc:hive2://127.0.0.1:10000 scott tiger
```

![image](https://github.com/user-attachments/assets/77fadb1f-118e-4d15-8a78-e9783baa9690)


### **Create Database and Table in Hive**

1. Create a new Hive database:
    ```sql
    CREATE DATABASE punjab_police;
    USE punjab_police;
    ```
![image](https://github.com/user-attachments/assets/73227817-b2d5-4df0-a392-6927750d7220)


2. Create a table based on the schema of the `police.csv` dataset:
    ```sql
    CREATE TABLE police_data (
        Crime_ID INT,
        Crime_Type STRING,
        Location STRING,
        Reported_Date STRING,
        Status STRING
    )
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ','
    STORED AS TEXTFILE;
    ```
    ![image](https://github.com/user-attachments/assets/13faa21a-5242-4f1e-bd69-4d98dc318400)


3. Load the data into the Hive table:
    ```sql
    LOAD DATA INPATH '/data/crimerecord/police/police.csv' INTO TABLE police_data;
    ```
![image](https://github.com/user-attachments/assets/e0fcbe55-d5fd-4a8c-a17b-df888204915f)


### **Query the Data in Hive**

Run SQL queries to analyze the data in Hive:

1. **View the top 10 rows:**
    ```sql
    SELECT * FROM police_data LIMIT 10;
    ```
![image](https://github.com/user-attachments/assets/6f189765-24f4-47db-ad70-42fbcfb4068e)


2. **Count total crimes:**
    ```sql
    SELECT COUNT(*) AS Total_Crimes FROM police_data;
    ```
![image](https://github.com/user-attachments/assets/8b56a8b5-6b0b-4306-82da-4cce52b50e95)


3. **Find most common crime types:**
    ```sql
    SELECT Crime_Type, COUNT(*) AS Occurrences
    FROM police_data
    GROUP BY Crime_Type
    ORDER BY Occurrences DESC;
    ```

   ![image](https://github.com/user-attachments/assets/54f000f7-36ec-4672-8bc6-996ac7b4004b)


4. **Identify locations with the highest crime rates:**
    ```sql
    SELECT Location, COUNT(*) AS Total_Crimes
    FROM police_data
    GROUP BY Location
    ORDER BY Total_Crimes DESC;
    ```
![image](https://github.com/user-attachments/assets/fb418097-97ff-46aa-941a-4b72a0702d3d)


5. **Find unresolved cases:**
    ```sql
    SELECT Status, COUNT(*) AS Count
    FROM police_data
    WHERE Status != 'Closed'
    GROUP BY Status;
    ```
![image](https://github.com/user-attachments/assets/9b3b32df-38c9-45bd-85dc-c4ac2b16b246)


**********There you go: your private Hive server to play with.**********

show databases;

![image](https://github.com/user-attachments/assets/7e8e65b1-cb98-41e2-b655-ddf941b614d5)

#### **📂 Part 2: Creating a Simple Hive Project**

---

##### **🎯 Objective**
We will:
1. Create a database.
2. Create a table inside the database.
3. Load data into the table.
4. Run queries to retrieve data.

---

##### **💾 Step 1: Create a Database**
In the Beeline CLI:
```sql
CREATE DATABASE mydb;
USE mydb;
```
- 📝 *`mydb` is the name of the database. Replace it with your preferred name.*

---

##### **📋 Step 2: Create a Table**
Still in the Beeline CLI, create a simple table:
```sql
CREATE TABLE employees (
    id INT,
    name STRING,
    age INT
);
```
- This creates a table named `employees` with columns `id`, `name`, and `age`.

---

##### **📥 Step 3: Insert Data into the Table**
Insert sample data into your table:
```sql
INSERT INTO employees VALUES (1, 'Prince', 30);
INSERT INTO employees VALUES (2, 'Ram Singh', 25);
```

---

##### **🔍 Step 4: Query the Table**
Retrieve data from your table:
```sql
SELECT * FROM employees;
```
- Output:

![image](https://github.com/user-attachments/assets/63529cb9-c74d-453e-a4d7-9f176762a8bc)


  ```
  +----+----------+-----+
  | id |   name   | age |
  +----+----------+-----+
  | 2  | Ram Singh |  25 |
  | 1  | Prince     | 30 |
  +----+----------+-----+
  ```

---

#### **🌟 Tips & Knowledge**

1. **What is Hive?**
   - Hive is a data warehouse tool on top of Hadoop.
   - It allows SQL-like querying over large datasets.

2. **Why Docker for Hive?**
   - Simplifies setup by avoiding manual configurations.
   - Provides a pre-configured environment for running Hive.

3. **Beeline CLI**:
   - A lightweight command-line tool for running Hive queries.

4. **Use Cases**:
   - **Data Analysis**: Run analytics on large datasets.
   - **ETL**: Extract, Transform, and Load data into your Hadoop ecosystem.

---

#### **🎉 You're Ready!**
You’ve successfully:
1. Set up Apache Hive.
2. Created and queried a sample project.  🐝

### **🐝 Apache Hive Basic Commands**

Here is a collection of basic Apache Hive commands with explanations that can help you while working with Hive:

---

#### **1. Database Commands**

- **Show Databases:**
  Displays all the databases available in your Hive environment.
  ```sql
  SHOW DATABASES;
  ```

- **Create a Database:**
  Create a new database.
  ```sql
  CREATE DATABASE <database_name>;
  ```
  Example:
  ```sql
  CREATE DATABASE mydb;
  ```

- **Use a Database:**
  Switch to the specified database.
  ```sql
  USE <database_name>;
  ```
  Example:
  ```sql
  USE mydb;
  ```

- **Drop a Database:**
  Deletes a database and its associated data.
  ```sql
  DROP DATABASE <database_name>;
  ```

---

#### **2. Table Commands**

- **Show Tables:**
  List all the tables in the current database.
  ```sql
  SHOW TABLES;
  ```

- **Create a Table:**
  Define a new table with specific columns.
  ```sql
  CREATE TABLE <table_name> (
      column_name column_type,
      ...
  );
  ```
  Example:
  ```sql
  CREATE TABLE employees (
      id INT,
      name STRING,
      age INT
  );
  ```

- **Describe a Table:**
  Get detailed information about a table, including column names and types.
  ```sql
  DESCRIBE <table_name>;
  ```

- **Drop a Table:**
  Deletes a table and its associated data.
  ```sql
  DROP TABLE <table_name>;
  ```

- **Alter a Table:**
  Modify a table structure, like adding new columns.
  ```sql
  ALTER TABLE <table_name> ADD COLUMNS (<new_column> <type>);
  ```
  Example:
  ```sql
  ALTER TABLE employees ADD COLUMNS (salary DOUBLE);
  ```

---

#### **3. Data Manipulation Commands**

- **Insert Data:**
  Insert data into a table.
  ```sql
  INSERT INTO <table_name> VALUES (<value1>, <value2>, ...);
  ```
  Example:
  ```sql
  INSERT INTO employees VALUES (1, 'John Doe', 30);
  ```

- **Select Data:**
  Retrieve data from a table.
  ```sql
  SELECT * FROM <table_name>;
  ```

- **Update Data:**
  Update existing data in a table.
  ```sql
  UPDATE <table_name> SET <column_name> = <new_value> WHERE <condition>;
  ```

- **Delete Data:**
  Delete rows from a table based on a condition.
  ```sql
  DELETE FROM <table_name> WHERE <condition>;
  ```

---

#### **4. Querying Commands**

- **Select Specific Columns:**
  Retrieve specific columns from a table.
  ```sql
  SELECT <column1>, <column2> FROM <table_name>;
  ```

- **Filtering Data:**
  Filter data based on conditions using the `WHERE` clause.
  ```sql
  SELECT * FROM <table_name> WHERE <column_name> <operator> <value>;
  ```
  Example:
  ```sql
  SELECT * FROM employees WHERE age > 25;
  ```

- **Sorting Data:**
  Sort the result by a column in ascending or descending order.
  ```sql
  SELECT * FROM <table_name> ORDER BY <column_name> ASC|DESC;
  ```
  Example:
  ```sql
  SELECT * FROM employees ORDER BY age DESC;
  ```

- **Group By:**
  Group data by one or more columns and aggregate it using functions like `COUNT`, `AVG`, `SUM`, etc.
  ```sql
  SELECT <column_name>, COUNT(*) FROM <table_name> GROUP BY <column_name>;
  ```
  Example:
  ```sql
  SELECT age, COUNT(*) FROM employees GROUP BY age;
  ```

---

#### **5. File Format Commands**

- **Create External Table:**
  Create a table that references data stored externally (e.g., in HDFS).
  ```sql
  CREATE EXTERNAL TABLE <table_name> (<column_name> <data_type>, ...) 
  ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '<delimiter>'
  LOCATION '<file_path>';
  ```
  Example:
  ```sql
  CREATE EXTERNAL TABLE employees (
      id INT,
      name STRING,
      age INT
  ) ROW FORMAT DELIMITED
  FIELDS TERMINATED BY ','
  LOCATION '/user/hive/warehouse/employees';
  ```

- **Load Data into Table:**
  Load data from a file into an existing Hive table.
  ```sql
  LOAD DATA LOCAL INPATH '<file_path>' INTO TABLE <table_name>;
  ```

---

#### **6. Other Useful Commands**

- **Show Current User:**
  Display the current user running the Hive session.
  ```sql
  !whoami;
  ```

- **Exit Hive:**
  Exit from the Hive shell.
  ```sql
  EXIT;
  ```

- **Set Hive Variables:**
  Set Hive session variables.
  ```sql
  SET <variable_name>=<value>;
  ```

- **Show Hive Variables:**
  Display all the set variables.
  ```sql
  SET;
  ```

- **Show the Status of Hive Jobs:**
  Display the status of running queries.
  ```sql
  SHOW JOBS;
  ```

---

#### **🌟 Tips & Best Practices**

- **Partitioning Tables:**
  When dealing with large datasets, partitioning your tables can help improve query performance.
  ```sql
  CREATE TABLE sales (id INT, amount DOUBLE)
  PARTITIONED BY (year INT, month INT);
  ```

- **Bucketing:**
  Bucketing splits your data into a fixed number of files or "buckets."
  ```sql
  CREATE TABLE sales (id INT, amount DOUBLE)
  CLUSTERED BY (id) INTO 4 BUCKETS;
  ```

- **Optimization:**
  Use columnar formats like `ORC` or `Parquet` for efficient storage and performance.
  ```sql
  CREATE TABLE sales (id INT, amount DOUBLE)
  STORED AS ORC;
  ```

These basic commands will help you interact with Hive and perform common operations like creating tables, querying data, and managing your Hive environment efficiently.

While **Hive** and **MySQL** both use SQL-like syntax for querying data, there are some key differences in their commands, especially since Hive is designed for querying large datasets in a Hadoop ecosystem, while MySQL is a relational database management system (RDBMS).

Here’s a comparison of **Hive** and **MySQL** commands in terms of common operations:

### **1. Creating Databases**
- **Hive**:
   ```sql
   CREATE DATABASE mydb;
   ```

- **MySQL**:
   ```sql
   CREATE DATABASE mydb;
   ```

   *Both Hive and MySQL use the same syntax to create a database.*

---

### **2. Switching to a Database**
- **Hive**:
   ```sql
   USE mydb;
   ```

- **MySQL**:
   ```sql
   USE mydb;
   ```

   *The syntax is the same for selecting a database in both systems.*

---

### **3. Creating Tables**
- **Hive**:
   ```sql
   CREATE TABLE employees (
       id INT,
       name STRING,
       age INT
   );
   ```

- **MySQL**:
   ```sql
   CREATE TABLE employees (
       id INT,
       name VARCHAR(255),
       age INT
   );
   ```

   **Differences**:
   - In Hive, **STRING** is used for text data, while in MySQL, **VARCHAR** is used.
   - Hive also has some specialized data types for distributed storage and performance, like `ARRAY`, `MAP`, `STRUCT`, etc.

---

### **4. Inserting Data**
- **Hive**:
   ```sql
   INSERT INTO employees VALUES (1, 'John', 30);
   INSERT INTO employees VALUES (2, 'Alice', 25);
   ```

- **MySQL**:
   ```sql
   INSERT INTO employees (id, name, age) VALUES (1, 'John', 30);
   INSERT INTO employees (id, name, age) VALUES (2, 'Alice', 25);
   ```

   **Differences**:
   - Hive allows direct `INSERT INTO` with values, while MySQL explicitly lists column names in the insert statement (though this is optional in MySQL if the columns match).

---

### **5. Querying Data**
- **Hive**:
   ```sql
   SELECT * FROM employees;
   ```

- **MySQL**:
   ```sql
   SELECT * FROM employees;
   ```

   *Querying data using `SELECT` is identical in both systems.*

---

### **6. Modifying Data**
- **Hive**:
   Hive doesn’t support traditional **UPDATE** or **DELETE** commands directly, as it is optimized for batch processing and is more suited for append operations. However, it does support **INSERT** and **INSERT OVERWRITE** operations.

   Example of replacing data:
   ```sql
   INSERT OVERWRITE TABLE employees SELECT * FROM employees WHERE age > 30;
   ```

- **MySQL**:
   ```sql
   UPDATE employees SET age = 31 WHERE id = 1;
   DELETE FROM employees WHERE id = 2;
   ```

   **Differences**:
   - Hive does not allow direct **UPDATE** or **DELETE**; instead, it uses **INSERT OVERWRITE** to modify data in batch operations.

---

### **7. Dropping Tables**
- **Hive**:
   ```sql
   DROP TABLE IF EXISTS employees;
   ```

- **MySQL**:
   ```sql
   DROP TABLE IF EXISTS employees;
   ```

   *The syntax for dropping tables is the same in both systems.*

---

### **8. Query Performance**
- **Hive**:
   - Hive is designed to run on large datasets using the Hadoop Distributed File System (HDFS), so it focuses more on **batch processing** rather than real-time queries. Query performance in Hive may be slower than MySQL because it’s optimized for scale, not for low-latency transaction processing.

- **MySQL**:
   - MySQL is an RDBMS, designed to handle **transactional workloads** with low-latency queries. It’s better suited for OLTP (Online Transaction Processing) rather than OLAP (Online Analytical Processing) workloads.

---

### **9. Indexing**
- **Hive**:
   - Hive doesn’t support traditional indexing as MySQL does. However, you can create **partitioned** or **bucketed** tables in Hive to improve query performance for certain types of data.

- **MySQL**:
   - MySQL supports **indexes** (e.g., **PRIMARY KEY**, **UNIQUE**, **INDEX**) to speed up query performance on large datasets.

---

### **10. Joins**
- **Hive**:
   ```sql
   SELECT a.id, a.name, b.age
   FROM employees a
   JOIN employee_details b ON a.id = b.id;
   ```

- **MySQL**:
   ```sql
   SELECT a.id, a.name, b.age
   FROM employees a
   JOIN employee_details b ON a.id = b.id;
   ```

   *The syntax for **JOIN** is the same in both systems.*

---

### **Summary of Key Differences**:
- **Data Types**: Hive uses types like `STRING`, `TEXT`, `BOOLEAN`, etc., while MySQL uses types like `VARCHAR`, `CHAR`, `TEXT`, etc.
- **Data Modification**: Hive does not support **UPDATE** or **DELETE** in the traditional way, and is generally used for **batch processing**.
- **Performance**: Hive is designed for querying large-scale datasets in Hadoop, so queries tend to be slower than MySQL.
- **Indexing**: Hive does not natively support indexing but can use partitioning and bucketing for performance optimization. MySQL supports indexing for faster queries.
- **ACID Properties**: MySQL supports full ACID compliance for transactional systems, whereas Hive is not transactional by default (but can support limited ACID features starting from version 0.14 with certain configurations).

In conclusion, while **Hive** and **MySQL** share SQL-like syntax, they are designed for very different use cases, and not all commands work the same way in both systems.

### **Visualize the Data (Optional)**

Export the query results to a CSV file for analysis in visualization tools:

```bash
hive -e "SELECT * FROM police_data;" > police_analysis_results.csv
```

You can use tools like Tableau, Excel, or Python (Matplotlib, Pandas) for data visualization.

## **Step 8: Configure Environment Variables (Optional)**

If you need to customize configurations, you can specify parameters in the `hadoop.env` file or as environmental variables for services (e.g., namenode, datanode, etc.). For example:

```bash
CORE_CONF_fs_defaultFS=hdfs://namenode:8020
```

This will be transformed into the following in the `core-site.xml` file:

```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://namenode:8020</value>
</property>
```

## **Conclusion**

You now have a fully functional Hadoop, Spark, and Hive cluster running in Docker. This environment is great for experimenting with big data processing and analytics in a lightweight, containerized setup.

---

I hope you have fun with this Hadoop-Spark-Hive cluster.



![image](https://github.com/user-attachments/assets/1347d354-a160-4cc6-8547-eb0857a72ba5)

