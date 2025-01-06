# Working with Apache Spark (7 hrs)

## **1. Introduction to Apache Spark**
- **Overview**: Apache Spark is an open-source distributed computing system known for its speed, ease of use, and general-purpose capabilities for big data processing.
- **Key Features**:
  - Fast processing using in-memory computation.
  - Supports multiple languages: Scala, Python, Java, and R.
  - Unified framework for batch and streaming data processing.

## **2. Introduction to DataFrames**
- **What are DataFrames?**
  - Distributed collections of data organized into named columns, similar to a table in a database or a DataFrame in Python's pandas.
  - Optimized for processing large datasets using Spark SQL.
- **Key Operations**:
  - Creating DataFrames from structured data sources (CSV, JSON, Parquet, etc.).
  - Performing transformations and actions on the data.

## **3. Introduction to Scala for Apache Spark**
- **Why Scala?**
  - Apache Spark is written in Scala, offering the best compatibility and performance.
  - Concise syntax and functional programming support.
- **Basic Syntax**:
  ```scala
  val numbers = List(1, 2, 3, 4, 5)
  val doubled = numbers.map(_ * 2)
  println(doubled)
  ```

## **4. Spark SQL**
- **Need for Spark SQL**:
  - Provides a declarative interface to query structured data using SQL-like syntax.
  - Supports seamless integration with other Spark modules.
  - Allows for optimization through Catalyst Optimizer.

- **Key Components**:
  - SQL Queries on DataFrames and temporary views.
  - Hive integration for legacy SQL workflows.
  - Support for structured data sources.

## **5. Hands-On: Spark SQL**
### **Objective**:
To create DataFrames, load data from different sources, and perform transformations and SQL queries.

### **Steps**:
#### **Step 1: Setup Environment**
1. Install Apache Spark and configure the environment.
2. Start the Spark shell or a Jupyter Notebook with PySpark.

#### **Step 2: Create DataFrames**
```python
import org.apache.spark.sql.SparkSession

# Initialize SparkSession
spark = SparkSession.builder \
    .appName("SparkSQLDemo") \
    .getOrCreate()

# Sample DataFrame
val data = Seq(("Alice", 30, "HR"), ("Bob", 25, "Engineering"), ("Charlie", 35, "Finance"))
val columns = Seq("Name", "Age", "Department")
val df = spark.createDataFrame(data).toDF(columns: _*)
df.show()
```

#### **Step 3: Load Data from Different Sources**
```python
# Load CSV
df_csv = spark.read.csv("file:///path/to/data.csv", header=True, inferSchema=True)

#**example from HDFS:**

val df = spark.read.csv("hdfs://localhost:9000/data/crimerecord/police/police.csv")

#**or from local file system**

val dfCsv = spark.read .option("header", "true") .option("inferSchema", "true") .csv("file:///police.csv")

df.show() #TO SHOW DATA


# Load JSON
df_json = spark.read.json("file:///path/to/data.json")

# Load Parquet
df_parquet = spark.read.parquet("file:///path/to/data.parquet")
```

#### **Step 4: Perform Transformations**
```python
# Filter Data
filtered_df = df.filter(df.Age > 30)
filtered_df.show()

# Add a Column
transformed_df = df.withColumn("Experience", df.Age - 20)
transformed_df.show()
```

#### **Step 5: Use Spark SQL**
```python
# Create a Temporary View
transformed_df.createOrReplaceTempView("employees")

# Run SQL Queries
result = spark.sql("SELECT Name, Age FROM employees WHERE Age > 30")
result.show()
```

#### **Step 6: Save Transformed Data**
```python
# Save as CSV
filtered_df.write.csv("file:///path/to/output.csv", header=True)

# Save as Parquet
filtered_df.write.parquet("file:///path/to/output.parquet")
```

---

## **6. Key Takeaways**
- Spark SQL simplifies working with structured data.
- DataFrames provide a flexible and powerful API for handling large datasets.
- Apache Spark is a versatile tool for distributed data processing, offering scalability and performance.

---

## **Estimated Time Allocation**
| **Topic**                        | **Duration** |
|----------------------------------|--------------|
| Introduction to Spark            | 1 hour       |
| DataFrames and Scala Overview    | 2 hours      |
| Spark SQL                        | 1 hour       |
| Hands-On Practice                | 3 hours      |

