# Learn Spark with exercises from Spark in Action, 2nd Edition

These are practical assignments from [Spark in Action, 2nd Edition by Jean-Georges Perrin](https://www.manning.com/books/spark-in-action-second-edition), which you can complete on the CDEP Hadoop Spark-Hive environment.
The data does not need to be put on HDFS. We do that in these assignments.

Once you've done that, you can copy the Spark commands one after the other, run them and see how it works.

I facing you're facing any problem you can see in this (Dutch) video in English Subtitles: [WATCH VIDEO](https://youtu.be/ajo2CYz_GSg).

## Start PySpark
Start the Docker environment according to [the manual](https://github.com/DIKW/CDEP-Docker/blob/master/hadoop/docker-hadoop-spark-hive/README.md). Follow the Quick Start Spark (PySpark) to log in to the Spark master.

Start PySpark:
```
/spark/bin/pyspark --master spark://69280b13519d:7077
```

## Prepare files
Copy the directory of data on the namode and copy it from there to HDFS
```
docker ps |grep namenode  # achterhaal Container ID van namenode

docker cp Spark_in_Action 2ffec0140800:sparkinaction

docker exec -it 2ffec0140800 bash

hdfs dfs -mkdir /data
hdfs dfs -mkdir /data/sparkinaction

hdfs dfs -put sparkinaction/* /data/sparkinaction
```


## Chapter 1
[Chapter 1 on Github](https://github.com/jgperrin/net.jgp.books.spark.ch01)

```
# Creates a session on a local master
absolute_file_path = "hdfs://namenode:8020/data/sparkinaction/books.csv"

session = SparkSession.builder.appName("CSV to Dataset").master("spark://69280b13519d:7077").getOrCreate()

# Reads a CSV file with header, called books.csv, stores it in a dataframe
df = session.read.csv(header=True, inferSchema=True, path=absolute_file_path)

# Shows at most 5 rows from the dataframe
df.show(5)

# Good to stop SparkSession at the end of the application
session.stop()
```


## Chapter 2
[Chapter 2 on Github](https://github.com/jgperrin/net.jgp.books.spark.ch02)
In this chapter we make a small transformation: we concatenate the lastname and firstname.
We skip the database step here. Feel free to add a PostgreSQL database to the hadoop-spark-hive docker-compose yourself.

```
from pyspark.sql import SparkSession
from pyspark.sql import functions as F

absolute_file_path = "hdfs://namenode:8020/data/sparkinaction/authors.csv"

#  Step 1: Ingestion
#  ---------
#
#  Reads a CSV file with header, called authors.csv, stores it in a dataframe
df = spark.read.csv(header=True, inferSchema=True, path=absolute_file_path)

# Step 2: Transform
# ---------
# Creates a new column called "name" as the concatenation of lname, a
# virtual column containing ", " and the fname column
df = df.withColumn("name", F.concat(F.col("lname"), F.lit(", "), F.col("fname")))

df.printSchema()

# Good to stop SparkSession at the end of the application
spark.stop()
```


## Chapter 3
[Chapter 3 on Github](https://github.com/jgperrin/net.jgp.books.spark.ch03)
In this chapter we rename some columns from a CSV. We remove a few columns from the result and add an id column, based on state, county and datasetId.

### Rename, delete and add columns

```
from pyspark.sql import SparkSession
from pyspark.sql.functions import lit,col,concat
import json

absolute_file_path = "hdfs://namenode:8020/data/sparkinaction/Restaurants_in_Wake_County_NC.csv"

spark = SparkSession.builder.appName("Restaurants in Wake County, NC") \
    .master("spark://69280b13519d:7077").getOrCreate()

df = spark.read.csv(header=True, inferSchema=True,path=absolute_file_path)

print("*** Right after ingestion")
df.show(5)

print("*** Schema as a tree:")
df.printSchema()
```

Renaming columns and removing the OBJECTID, PERMITID and GEOCODESTATUS columns from our data frame.
```
# Let's transform our dataframe
df =  df.withColumn("county", lit("Wake")) \
        .withColumnRenamed("HSISID", "datasetId") \
        .withColumnRenamed("NAME", "name") \
        .withColumnRenamed("ADDRESS1", "address1") \
        .withColumnRenamed("ADDRESS2", "address2") \
        .withColumnRenamed("CITY", "city") \
        .withColumnRenamed("STATE", "state") \
        .withColumnRenamed("POSTALCODE", "zip") \
        .withColumnRenamed("PHONENUMBER", "tel") \
        .withColumnRenamed("RESTAURANTOPENDATE", "dateStart") \
        .withColumnRenamed("FACILITYTYPE", "type") \
        .withColumnRenamed("X", "geoX") \
        .withColumnRenamed("Y", "geoY") \
        .drop("OBJECTID", "PERMITID", "GEOCODESTATUS")
```

Creating a new column concatenated from state, county and datasetId.
```
df = df.withColumn("id",
        concat(col("state"), lit("_"), col("county"), lit("_"), col("datasetId")))

# Shows at most 5 rows from the dataframe
print("*** Dataframe transformed")
df.show(5)

print("*** Schema as a tree:")
df.printSchema()
```

We can save the data frame as json.
```
print("*** Schema as string: {}".format(df.schema))
schemaAsJson = df.schema.json()
parsedSchemaAsJson = json.loads(schemaAsJson)

print("*** Schema as JSON: {}".format(json.dumps(parsedSchemaAsJson, indent=2)))

# Good to stop SparkSession at the end of the application
spark.stop()
```

### Union of two data frames.

```
from pyspark.sql.functions import (lit,col,concat,split)
from pyspark.sql import SparkSession

absolute_file_path1 = "hdfs://namenode:8020/data/sparkinaction/Restaurants_in_Wake_County_NC.csv"
absolute_file_path2 = "hdfs://namenode:8020/data/sparkinaction/Restaurants_in_Durham_County_NC.json"

spark = SparkSession.builder.appName("Union of two dataframes") \
    .master("local[*]").getOrCreate()
```

Reading data from Wake County (csv format).
The columns are adjusted so that a union is possible.
```
df1 = spark.read.csv(path=absolute_file_path1,header=True,inferSchema=True)

df1 = df1.withColumn("county", lit("Wake")) \
    .withColumnRenamed("HSISID", "datasetId") \
    .withColumnRenamed("NAME", "name") \
    .withColumnRenamed("ADDRESS1", "address1") \
    .withColumnRenamed("ADDRESS2", "address2") \
    .withColumnRenamed("CITY", "city") \
    .withColumnRenamed("STATE", "state") \
    .withColumnRenamed("POSTALCODE", "zip") \
    .withColumnRenamed("PHONENUMBER", "tel") \
    .withColumnRenamed("RESTAURANTOPENDATE", "dateStart") \
    .withColumn("dateEnd", lit(None)) \
    .withColumnRenamed("FACILITYTYPE", "type") \
    .withColumnRenamed("X", "geoX") \
    .withColumnRenamed("Y", "geoY") \
    .drop("OBJECTID", "GEOCODESTATUS", "PERMITID")

df1 = df1.withColumn("id", concat(col("state"), lit("_"), col("county"), lit("_"), col("datasetId")))
df1 = df1.repartition(4);
```


Reading data from Durham County (json format)
Adjust column names and delete columns so that union is possible.
```
df2 = spark.read.json(absolute_file_path2)

drop_cols = ["fields", "geometry", "record_timestamp", "recordid"]
df2 =  df2.withColumn("county", lit("Durham")) \
    .withColumn("datasetId", col("fields.id")) \
    .withColumn("name", col("fields.premise_name")) \
    .withColumn("address1", col("fields.premise_address1")) \
    .withColumn("address2", col("fields.premise_address2")) \
    .withColumn("city", col("fields.premise_city")) \
    .withColumn("state", col("fields.premise_state")) \
    .withColumn("zip", col("fields.premise_zip")) \
    .withColumn("tel", col("fields.premise_phone")) \
    .withColumn("dateStart", col("fields.opening_date")) \
    .withColumn("dateEnd", col("fields.closing_date")) \
    .withColumn("type", split(col("fields.type_description"), " - ").getItem(1)) \
    .withColumn("geoX", col("fields.geolocation").getItem(0)) \
    .withColumn("geoY", col("fields.geolocation").getItem(1)) \
    .drop(*drop_cols)

df2 = df2.withColumn("id", concat(col("state"), lit("_"), col("county"), lit("_"), col("datasetId")))
# I left the following line if you want to play with repartitioning
df = df.repartition(4);
```

Union uitvoeren
```
df = df1.unionByName(df2)
df.show(5)
df.printSchema()
print("We have {} records.".format(df.count()))
partition_count = df.rdd.getNumPartitions()
print("Partition count: {}".format(partition_count))


spark.stop()
```



### array to dataframe

```
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType,StructField,StringType

# Creates a session on a local master
spark = SparkSession.builder.appName("Array to Dataframe") \
    .master("spark://69280b13519d:7077").getOrCreate()

data = [['Jean'], ['Liz'], ['Pierre'], ['Lauric']]

"""
* data:    parameter list1, data to create a dataset
* encoder: parameter list2, implicit encoder
"""
schema = StructType([StructField('name', StringType(), True)])

df = spark.createDataFrame(data, schema)
df.show()
df.printSchema()

spark.stop()
```



## Chapter 4
[Chapter 4 on Github](https://github.com/jgperrin/net.jgp.books.spark.ch04)

This first practice is about Spark being "fundamentally lazy". You can perform transformations on your data, but as long as you don't query the result, Spark won't work yet.

```
import time
from pyspark.sql import SparkSession
from pyspark.sql.functions import (lit,col,concat,expr)

absolute_file_path = "hdfs://namenode:8020/data/sparkinaction/NCHS_-_Teen_Birth_Rates_for_Age_Group_15-19_in_the_United_States_by_County.csv"

mode=""
t0 = int(round(time.time() * 1000))

# Step 1 - Creates a session on a local master
spark = SparkSession.builder.appName("Analysing Catalyst's behavior") \
    .master("spark://69280b13519d:7077").getOrCreate()

t1 = int(round(time.time() * 1000))

print("1. Creating a session ........... {}".format(t1 - t0))

# Step 2 - Reads a CSV file with header, stores it in a dataframe
df = spark.read.csv(header=True, inferSchema=True,path=absolute_file_path)

initalDf = df
t2 = int(round(time.time() * 1000))
print("2. Loading initial dataset ...... {}".format(t2 - t1))

# Step 3 - Build a bigger dataset
for x in range(60):
    df = df.union(initalDf)

t3 = int(round(time.time() * 1000))
print("3. Building full dataset ........ {}".format(t3 - t2))

# Step 4 - Cleanup. preparation
df = df.withColumnRenamed("Lower Confidence Limit", "lcl") \
       .withColumnRenamed("Upper Confidence Limit", "ucl")

t4 = int(round(time.time() * 1000))
print("4. Clean-up ..................... {}".format(t4 - t3))

# Step 5 - Transformation
if mode.lower != "noop":
    df =  df.withColumn("avg", expr("(lcl+ucl)/2")) \
            .withColumn("lcl2", col("lcl")) \
            .withColumn("ucl2", col("ucl"))
    if mode.lower == "full":
        df = df.drop("avg","lcl2","ucl2")


t5 = int(round(time.time() * 1000))
print("5. Transformations  ............. {}".format(t5 - t4))

# Step 6 - Action
df.collect()
t6 = int(round(time.time() * 1000))
print("6. Final action ................. {}".format(t6 - t5))

print("")
print("# of records .................... {}".format(df.count))

spark.stop()
```


### Explaining Spark transformations
```
from pyspark.sql import SparkSession
from pyspark.sql.functions import (lit,col,concat,expr)

absolute_file_path = "hdfs://namenode:8020/data/sparkinaction/NCHS_-_Teen_Birth_Rates_for_Age_Group_15-19_in_the_United_States_by_County.csv"

# Step 1 - Creates a session on a local master
spark = SparkSession.builder.appName("Analysing Catalyst's behavior") \
                    .master("local[*]").getOrCreate()

# Step 2 - Reads a CSV file with header, stores it in a dataframe
df = spark.read.csv(header=True, inferSchema=True,path=absolute_file_path)

df0 = df

# Step 3 - Build a bigger dataset
df = df.union(df0)

# Step 4 - Cleanup. preparation
df = df.withColumnRenamed("Lower Confidence Limit", "lcl") \
       .withColumnRenamed("Upper Confidence Limit", "ucl")

# Step 5 - Transformation
df =  df.withColumn("avg", expr("(lcl+ucl)/2")) \
        .withColumn("lcl2", col("lcl")) \
        .withColumn("ucl2", col("ucl"))
```
With explain you can see how Spark worked. This can be useful for debugging your Spark code.
```
# Step 6 - explain
df.explain()

spark.stop()
```
