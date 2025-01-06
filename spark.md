# **Working with Apache Spark (7 hrs)**

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
2. Start the Spark shell or a Scala-based notebook (such as Zeppelin or Jupyter with Spark integration).

#### **Step 2: Create DataFrames**
```scala
import org.apache.spark.sql.SparkSession

// Initialize SparkSession
val spark = SparkSession.builder
  .appName("SparkSQLDemo")
  .getOrCreate()

// Sample DataFrame
val data = Seq(("Alice", 30, "HR"), ("Bob", 25, "Engineering"), ("Charlie", 35, "Finance"))
val columns = Seq("Name", "Age", "Department")
val df = spark.createDataFrame(data).toDF(columns: _*)
df.show()
```

#### **Step 3: Load Data from Different Sources**

```scala
// Load CSV
val dfCsv = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("file:///path/to/data.csv")

// Load JSON
val dfJson = spark.read.json("file:///path/to/data.json")

// Load Parquet
val dfParquet = spark.read.parquet("file:///path/to/data.parquet")
```

For **HDFS** example:
```scala
// Read data from HDFS
val df = spark.read
  .option("header", "true")
  .csv("hdfs://localhost:9000/data/crimerecord/police/police.csv")

df.show() // Display the loaded data
```

For **local file system** example:
```scala
// Read data from local file system
val dfCsv = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("file:///path/to/data.csv")

dfCsv.show() // Display the loaded data
```

#### **Step 4: Perform Transformations**
```scala
// Filter Data
val filteredDf = df.filter($"Age" > 30)
filteredDf.show()

// Add a Column
val transformedDf = df.withColumn("Experience", $"Age" - 20)
transformedDf.show()
```

#### **Step 5: Use Spark SQL**
```scala
// Create a Temporary View
transformedDf.createOrReplaceTempView("employees")

// Run SQL Queries
val result = spark.sql("SELECT Name, Age FROM employees WHERE Age > 30")
result.show()
```

#### **Step 6: Save Transformed Data**
```scala
// Save as CSV
filteredDf.write
  .option("header", "true")
  .csv("file:///path/to/output.csv")

// Save as Parquet
filteredDf.write.parquet("file:///path/to/output.parquet")
```

---

## **6. Key Takeaways**
- Spark SQL simplifies working with structured data.
- DataFrames provide a flexible and powerful API for handling large datasets.
- Apache Spark is a versatile tool for distributed data processing, offering scalability and performance.

---

## **Estimated Time Allocation**
| **Topic**                        | **Duration** |
|-----------------------------------|--------------|
| Introduction to Spark             | 1 hour       |
| DataFrames and Scala Overview     | 2 hours      |
| Spark SQL                         | 1 hour       |
| Hands-On Practice                 | 3 hours      |

---

This complete document now provides both the theoretical explanations and the Scala-based examples for your hands-on Apache Spark session. It ensures a seamless learning experience and covers loading data from various sources, transforming data, and querying it using Spark SQL.
