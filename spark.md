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

**To Start Spark shell folllow these instructions**

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
![image](https://github.com/user-attachments/assets/855bf183-bf5f-4bca-a6b4-b381928a25e9)


### To Stop Spark Services

When you're done, you can stop Spark services.

1. **Stop the Spark Worker**:

   ```bash
   $SPARK_HOME/sbin/stop-worker.sh
   ```

2. **Stop the Spark Master**:

   ```bash
   $SPARK_HOME/sbin/stop-master.sh
   
**Monitor the job**:  
   You can monitor the job's progress through the Spark Web UI at `http://localhost:8080`.

#### **Step 2: Create DataFrames**

```scala
import org.apache.spark.sql.SparkSession

// Initialize SparkSession
val spark = SparkSession.builder
  .appName("SimpleDataFrameExample")

  .getOrCreate()

// Sample Data
val data = Seq(
  ("Alice", 30, "HR"),
  ("Bob", 25, "Engineering"),
  ("Charlie", 35, "Finance")
)

// Create DataFrame
val df = data.toDF("Name", "Age", "Department")

// Show the DataFrame
df.show()

// Stop the Spark session
spark.stop()
```

#### **Step 3: Perform Transformations**
```scala
// Filter Data
val filteredDf = df.filter($"Age" > 30)
filteredDf.show()

// Add a Column
val transformedDf = df.withColumn("Experience", $"Age" - 20)
transformedDf.show()
```

#### **Step 4: Use Spark SQL**
```scala
// Create a Temporary View
transformedDf.createOrReplaceTempView("employees")

// Run SQL Queries
val result = spark.sql("SELECT Name, Age FROM employees WHERE Age > 30")
result.show()
```

#### **Step 5: Save Transformed Data**
```scala
// Save as CSV
filteredDf.write
  .option("header", "true")
  .csv("file:///path/to/output.csv")

// Save as Parquet
filteredDf.write.parquet("file:///path/to/output.parquet")
```

#### **Step 6: Load Data from Different Sources**

```scala
# Load CSV for HDFS or Local file system

#**Example to load csv file from HDFS:**
val df = spark.read.option("header", "false").csv("hdfs://localhost:9000/data/crimerecord/police/police.csv")
df.show()

#**or from local file system**

val df = spark.read.option("header", "false").csv"file:///police.csv", header=False, inferSchema=True)

df.show()
```

#### **Step 7: Scala WordCount program.**

```markdown
### Docker Command to Copy File

Use the following command to copy the `data.txt` file from your local system to the Docker container:

```bash
docker cp data.txt nodemanager:/data.txt
```
![image](https://github.com/user-attachments/assets/73a84d9a-af1c-45f0-9504-a24b192e598d)

Use the following command to put the `data.txt` file from your Docker container to HDFS:

```bash
hdfs dfs -put data.txt /data
```
![image](https://github.com/user-attachments/assets/b4d93f36-f1b1-4056-a4af-d4dbb418634e)



##******### Now Let's do WordCount Program in Scala******

The following Scala code performs a WordCount operation using Apache Spark:

```scala
import org.apache.spark.{SparkConf}
val conf = new SparkConf().setAppName("WordCountExample").setMaster("local")
val input = sc.textFile("hdfs://namenode:9000/data/data.txt")
val wordPairs = input.flatMap(line => line.split(" ")).map(word => (word, 1))
val wordCounts = wordPairs.reduceByKey((a, b) => a + b)
wordCounts.collect().foreach { case (word, count) =>
  println(s"$word: $count")
}
```

To Stop Session
```scala
sc.stop()
```

![image](https://github.com/user-attachments/assets/bd16713b-7e01-4c83-88d2-f12afc8a4806)



### Steps:

1. **Copy File**: Use `docker cp` to move or create the file inside the namenode Docker container.
2. **Copy File to HDFS**: Use `hdfs dfs -put` to move the file inside the HDFS filesystem.
3. **WordCount Program**: The program reads the file, splits it into words, and counts the occurrences of each word.
4. **Output**: The word counts will be printed to the console when the program is executed.
```

**##Closing the Spark Session**

Once you are done with your operations, don’t forget to stop the Spark session.

```scala
spark.stop()
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
