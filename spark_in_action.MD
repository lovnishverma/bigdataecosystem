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
