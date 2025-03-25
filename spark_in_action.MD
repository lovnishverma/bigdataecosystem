# Spark with Hadoop Usage Guide

## 1. Starting Hadoop and Spark Services

Before using Spark with Hadoop, ensure all required services are running.

### Start Hadoop Services:
```bash
start-dfs.sh  # Start HDFS
start-yarn.sh # Start YARN
```
Verify running services:
```bash
jps
```
Expected output (or similar):
```
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
```

### Start Spark Services (if needed):
```bash
$SPARK_HOME/sbin/start-all.sh
```
or

```bash
$SPARK_HOME/sbin/start-master.sh
$SPARK_HOME/sbin/start-worker.sh spark://localhost:7077
```

### To Stop

```bash
$SPARK_HOME/sbin/stop-all.sh
```

## 2. Running Spark Shell on Hadoop (YARN Mode)
```bash
spark-shell --master yarn
```
Run basic commands in Spark shell:
```scala
val rdd = sc.parallelize(Seq("Spark", "Hadoop", "Big Data"))
rdd.collect().foreach(println)
```

## 3. Running a Spark Job on Hadoop (YARN)
### Submit a Job
```bash
spark-submit --master yarn --deploy-mode client \
  --class org.apache.spark.examples.SparkPi \
  $SPARK_HOME/examples/jars/spark-examples_2.12-3.5.1.jar 10
```

For cluster mode:
```bash
spark-submit --master yarn --deploy-mode cluster \
  --class org.apache.spark.examples.SparkPi \
  $SPARK_HOME/examples/jars/spark-examples_2.12-3.5.1.jar 10
```

## 4. Reading and Writing Data from HDFS

### Upload File to HDFS:
```bash
hdfs dfs -mkdir -p /user/lovnish/test
hdfs dfs -put localfile.txt /user/lovnish/test/
```

### Read File in Spark:
```scala
val file = sc.textFile("hdfs://localhost:9000/user/lovnish/test/localfile.txt")
file.collect().foreach(println)
```

### Write Output to HDFS:
```scala
file.saveAsTextFile("hdfs://localhost:9000/user/lovnish/output")
```

## 5. Using Spark SQL with Hive Metastore

Start Spark with Hive support:
```bash
spark-shell --master yarn --conf spark.sql.catalogImplementation=hive
```

### Create a Table:
```scala
spark.sql("CREATE TABLE students (id INT, name STRING) USING hive")
spark.sql("INSERT INTO students VALUES (1, 'Spark'), (2, 'Hadoop')")
```

### Query Data:
```scala
spark.sql("SELECT * FROM students").show()
```

## 6. Running a Python (PySpark) Job

### Start PySpark:
```bash
pyspark --master yarn
```

### Run a PySpark Job:
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("PySparkExample").getOrCreate()
data = [(1, "Spark"), (2, "Hadoop")]
df = spark.createDataFrame(data, ["id", "name"])
df.show()
```

## 7. Stopping Services

Stop Spark:
```bash
$SPARK_HOME/sbin/stop-all.sh
```

Stop Hadoop:
```bash
stop-dfs.sh
stop-yarn.sh
```

## 8. Monitoring Spark Jobs

View Spark Web UI:
- Standalone Mode: http://localhost:4040
- YARN Mode: Run `yarn application -list` to get the Application ID, then:
  ```bash
  yarn application -status <application_id>
  ```

## 9. Debugging and Logs
Check logs of Spark applications running on YARN:
```bash
yarn logs -applicationId <application_id>
```
For Hadoop logs:
```bash
hdfs dfsadmin -report
```

## 10. Hands-On: Spark SQL

### **Objective**:
To create DataFrames, load data from different sources, and perform transformations and SQL queries.

### **Step 1: Setup Environment**

Start Spark in Master Mode:
```bash
$SPARK_HOME/sbin/start-master.sh
```

Start Spark in Worker Mode:
```bash
$SPARK_HOME/sbin/start-worker.sh spark://localhost:7077
```

Open Spark shell:
```bash
spark-shell
```

### **Step 2: Create DataFrames**
```scala
val data = Seq(
  ("Alice", 30, "HR"),
  ("Bob", 25, "Engineering"),
  ("Charlie", 35, "Finance")
)

val df = data.toDF("Name", "Age", "Department")

df.show()
```

### **Step 3: Perform Transformations Using Spark SQL**
```scala
df.createOrReplaceTempView("employees")
val result = spark.sql("SELECT Department, COUNT(*) as count FROM employees GROUP BY Department")
result.show()
```

### **Step 4: Save Transformed Data**
```scala
result.write.option("header", "true").csv("hdfs://localhost:9000/data/output/output_employees")
```

### **Step 5: Scala WordCount Program**
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

**Stop Session**:
```scala
sc.stop()
```

---

## **11. Key Takeaways**
- Spark SQL simplifies working with structured data.
- DataFrames provide a flexible and powerful API for handling large datasets.
- Apache Spark is a versatile tool for distributed data processing, offering scalability and performance.

