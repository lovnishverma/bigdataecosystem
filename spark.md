# **Working with Apache Spark (7 hrs)**

## **1. Introduction to Apache Spark**

- **Overview**: Apache Spark is an open-source distributed computing system known for its speed, ease of use, and general-purpose capabilities for big data processing.

- **Key Features**:
  - Fast processing using in-memory computation.
  - Supports multiple languages: Scala, Python, Java, and R.
  - Unified framework for batch and streaming data processing.

---

## **2. Introduction to DataFrames**

- **What are DataFrames?**
  - Distributed collections of data organized into named columns, similar to a table in a database or a DataFrame in Python's pandas.
  - Optimized for processing large datasets using Spark SQL.

- **Key Operations**:
  - Creating DataFrames from structured data sources (CSV, JSON, Parquet, etc.).
  - Performing transformations and actions on the data.

---

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

---

## **4. Spark SQL**

- **Need for Spark SQL**:
  - Provides a declarative interface to query structured data using SQL-like syntax.
  - Supports seamless integration with other Spark modules.
  - Allows for optimization through Catalyst Optimizer.

- **Key Components**:
  - SQL Queries on DataFrames and temporary views.
  - Hive integration for legacy SQL workflows.
  - Support for structured data sources.

---

## **5. Hands-On: Spark SQL**

### **Objective**:
To create DataFrames, load data from different sources, and perform transformations and SQL queries.

### **Steps**:

#### **Step 1: Setup Environment**

1. **Install Apache Spark and configure the environment.**
2. **Start the Spark shell or a Scala-based notebook (such as Zeppelin or Jupyter with Spark integration).**

**Start Spark in Master Mode**:

```bash
$SPARK_HOME/sbin/start-master.sh
```
**Start Spark in Worker Mode**:

```bash
$SPARK_HOME/sbin/start-worker.sh spark://localhost:7077
```

**Open Spark shell**:

```bash
spark-shell
```

**Monitor the job**:

Visit `http://localhost:8080` to monitor the Spark Web UI.

**Stop Spark Services**:

```bash
$SPARK_HOME/sbin/stop-worker.sh
$SPARK_HOME/sbin/stop-master.sh
```

#### **Step 2: Create DataFrames**

```scala
val data = Seq(
  ("Alice", 30, "HR"),
  ("Bob", 25, "Engineering"),
  ("Charlie", 35, "Finance")
)

val df = data.toDF("Name", "Age", "Department")

df.show()
```

#### **Step 3: Perform Transformations Using Spark SQL**

```scala
df.createOrReplaceTempView("employees")
val result = spark.sql("SELECT Department, COUNT(*) as count FROM employees GROUP BY Department")
result.show()
```

#### **Step 4: Save Transformed Data**

result.write.option("header", "true").csv("hdfs://localhost:9000/data/output/employees")


#### **Step 5: Load Data from Different Sources**

```scala
// Load CSV from HDFS
val df = spark.read.option("header", "false").csv("hdfs://localhost:9000/data/crimerecord/police/police.csv")
df.show()

// Load CSV from local filesystem
val dfLocal = spark.read.option("header", "false").csv("file:///police.csv")
dfLocal.show()
```

#### **Step 6: Scala WordCount Program**

```scala
import org.apache.spark.{SparkConf}
val conf = new SparkConf().setAppName("WordCountExample").setMaster("local")
val input = sc.textFile("hdfs://localhost:9000/data/data.txt")
val wordPairs = input.flatMap(line => line.split(" ")).map(word => (word, 1))
val wordCounts = wordPairs.reduceByKey((a, b) => a + b)
wordCounts.collect().foreach { case (word, count) =>
  println(s"$word: $count")
}
```

**Stop Session**:

```scala
sc.stop()
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

