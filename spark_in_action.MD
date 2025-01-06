# Learn Spark with exercises from Spark in Action, 2nd Edition

These are practical assignments from [Spark in Action, 2nd Edition by Jean-Georges Perrin](https://www.manning.com/books/spark-in-action-second-edition), which you can complete on the CDEP Hadoop Spark-Hive environment.
The data does not need to be put on HDFS. We do that in these assignments.

Once you've done that, you can copy the Spark commands one after the other, run them and see how it works.

I facing you're facing any problem you can see in this (Dutch) video in English Subtitles: [WATCH VIDEO](https://youtu.be/ajo2CYz_GSg).

To learn more about Spark visit https://github.com/needmukesh/Hadoop-Books/blob/master/book/Spark%20in%20Action.pdf

## Start PySpark
Start the Docker environment according to [the manual](https://github.com/jgperrin/net.jgp.books.spark.ch01/blob/master/README.md). Follow the Quick Start Spark (PySpark) to log in to the Spark master.



Start PySpark:
```
spark/bin/spark-shell --master spark://spark-master:7077
```

## Prepare files
Copy the directory of data on the namode and copy it from there to HDFS
```
Inside data folder is books.csv e.g: C:\Users\princ\Desktop\bigdata\bigdataecosystem\data>docker cp books.csv namenode:/books.csv

docker cp books.csv namenode:/books.csv

docker exec -it namenode bash

hdfs dfs -mkdir /data
hdfs dfs -mkdir /data/sparkinaction

hdfs dfs -put books.csv /data/sparkinaction
```


## Chapter 1
[Chapter 1 on Github](https://github.com/jgperrin/net.jgp.books.spark.ch01)

```
val df = spark.read.csv("hdfs://namenode:9000/data/sparkinaction/books.csv")


# Shows at most 5 rows from the dataframe
df.show(5)

# Good to stop SparkSession at the end of the application
session.stop()
```


## Chapter 2
[Chapter 2 on Github](https://github.com/jgperrin/net.jgp.books.spark.ch02)
In this chapter we make a small transformation: we concatenate the lastname and firstname.
We skip the database step here. Feel free to add a PostgreSQL database to the hadoop-spark-hive docker-compose yourself.

COPY authors.csv to namenode container using this command:
docker cp authors.csv namenode:/authors.csv

execute bash terminal of namenode container using this command:
docker exec -it namenode bash

put authors.csv to hdfs inside /data/sparkinaction directory using this command:
hdfs dfs -put authors.csv /data/sparkinaction

execute spark-master container in interactive bash terminal using this command:
docker exec -it spark-master bash

Run Scala Shell  using this command:
spark/bin/spark-shell --master spark://spark-master:7077
 

Now Inside Scala Terminal 
```

import org.apache.spark.sql.functions._

val df = spark.read.csv("hdfs://namenode:9000/data/sparkinaction/authors.csv")

# Step 2: Transform
# ---------
// Assuming _c0 is lname and _c1 is fname

var dfUpdated = df.withColumn("name", concat(col("_c0"), lit(", "), col("_c1")))

dfUpdated.show()


# Good to stop SparkSession at the end of the application
spark.stop()
```

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
![image](https://github.com/user-attachments/assets/06c2c14f-cf8e-4b38-8944-7844e75ee5d6)


#### **Step 3: Perform Transformations Using Spark SQL**

```scala
df.createOrReplaceTempView("employees")
val result = spark.sql("SELECT Department, COUNT(*) as count FROM employees GROUP BY Department")
result.show()
```
![image](https://github.com/user-attachments/assets/c9125138-63dd-4c29-82c4-6d04bc531508)


#### **Step 4: Save Transformed Data**

```scala
result.write.option("header", "true").csv("hdfs://localhost:9000/data/output/output_employees")
```
Reading from HDFS:
Once the data is written to HDFS, you can read it back into Spark using:

```scala
val outputDF = spark.read.option("header", "true").csv("hdfs://localhost:9000/output_employees.csv")
 ```

View output_employees.csv from HDFS

```scala
outputDF.show()
 ```

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
val input = sc.textFile("hdfs://localhost:9000/data.txt")
val wordPairs = input.flatMap(line => line.split(" ")).map(word => (word, 1))
val wordCounts = wordPairs.reduceByKey((a, b) => a + b)
wordCounts.collect().foreach { case (word, count) =>
  println(s"$word: $count")
}
```
![image](https://github.com/user-attachments/assets/428e0d99-f0e0-4edd-8f3c-4543130c8a47)


**Stop Session**:

```scala
sc.stop()
```

---

## **6. Key Takeaways**

- Spark SQL simplifies working with structured data.
- DataFrames provide a flexible and powerful API for handling large datasets.
- Apache Spark is a versatile tool for distributed data processing, offering scalability and performance.
