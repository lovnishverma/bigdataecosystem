Here is a corrected and comprehensive document with proper code examples for your **Apache Spark session**:

---

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

**To Start Spark shell:**

1. **Start Spark in Master Mode**:  
   You can start Spark in master mode from the command line:

   ```bash
   $SPARK_HOME/sbin/start-master.sh
   ```
2. **Open Spark shell** (Optional, if you want to run Spark workers as well):

   ```bash
   spark-shell
   ```

   This will start Spark in master mode and expose the Spark Web UI at `http://localhost:8080`.

3. **Start Spark in Worker Mode** (Optional, if you want to run Spark workers as well):

   ```bash
   $SPARK_HOME/sbin/start-worker.sh spark://localhost:7077
   ```

   This starts the worker node and connects it to the master node.
   
**To Stop Spark Services:**

When you're done, you can stop Spark services.

1. **Stop the Spark Worker**:

   ```bash
   $SPARK_HOME/sbin/stop-worker.sh
   ```

2. **Stop the Spark Master**:

   ```bash
   $SPARK_HOME/sbin/stop-master.sh
   ```

**Monitor the job**:  
You can monitor the job's progress through the Spark Web UI at `http://localhost:8080`.

#### **Step 2: Create DataFrames**

Here’s a simple example to create and display a DataFrame in Scala using Apache Spark:

```scala
import org.apache.spark.sql.SparkSession

// Initialize SparkSession
val spark = SparkSession.builder
  .appName("SimpleDataFrameExample")
  .getOrCreate()

// Sample Data
val data = Seq(
  ("Chandan", 30, "HR"),
  ("Rahul", 25, "Engineering"),
  ("Pranav", 35, "Finance")
)

// Create DataFrame
val df = data.toDF("Name", "Age", "Department")

// Show the DataFrame
df.show()
```

### Output:

```
+-------+---+------------+
|   Name|Age|Department |
+-------+---+------------+
| Chandan| 30|         HR |
|  Rahul| 25| Engineering|
| Pranav| 35|     Finance|
+-------+---+------------+
```

#### **Step 3: Load Data from Different Sources**

To load data from different file formats (CSV, JSON, Parquet), you can use the following methods:

```scala
import org.apache.spark.sql.SparkSession

// Initialize SparkSession
val spark = SparkSession.builder
  .appName("SimpleDataFrameExample")
  .getOrCreate()

// Load CSV
val dfCsv = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("file:///path/to/police.csv")  // Change path to your actual file location

// Load JSON
val dfJson = spark.read
  .json("file:///path/to/data.json")  // Replace with actual path

// Load Parquet
val dfParquet = spark.read
  .parquet("file:///path/to/data.parquet")  // Replace with actual path
```

For **HDFS**:

```scala
// Read data from HDFS
val dfHDFS = spark.read
  .option("header", "true")
  .csv("hdfs://localhost:9000/data/crimerecord/police/police.csv")

dfHDFS.show() // Display the loaded data
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

// Add a new column (Experience)
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
transformedDf.write
  .option("header", "true")
  .csv("file:///path/to/output.csv")

// Save as Parquet
transformedDf.write
  .parquet("file:///path/to/output.parquet")
```

#### **Step 7: Scala WordCount Program**

Here is a simple **WordCount** program in Scala:

```scala
import org.apache.spark.{SparkConf, SparkContext}

// Set up SparkConf and SparkContext
val conf = new SparkConf().setAppName("WordCountExample").setMaster("local")
val sc = new SparkContext(conf)

// Load input data
val input = sc.textFile("hdfs://localhost:9000/data/data.txt")

// Word Count operation
val wordPairs = input.flatMap(line => line.split(" ")).map(word => (word, 1))
val wordCounts = wordPairs.reduceByKey((a, b) => a + b)

// Output the result
wordCounts.collect().foreach { case (word, count) =>
  println(s"$word: $count")
}

// Stop SparkContext
sc.stop()
```

### Docker Command to Copy File

To copy the `data.txt` file from your local system to the Docker container, use the following command:

```bash
docker cp data.txt nodemanager:/data.txt
```

To put the `data.txt` file from your Docker container to HDFS:

```bash
hdfs dfs -put data.txt /data
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

This document provides both theoretical explanations and Scala-based examples for your hands-on Apache Spark session. It covers loading data from various sources, transforming data, and querying it using Spark SQL.
