[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)

# Docker multi-container environment with Hadoop, Spark and Hive

This is it: a Docker multi-container environment with Hadoop (HDFS), Spark and Hive. But without the large memory requirements of a Cloudera sandbox. (On my Windows 10 laptop (with WSL2) it seems to consume a mere 3 GB.)

The only thing lacking, is that Hive server doesn't start automatically. To be added when I understand how to do that in docker-compose.


## Quick Start

To deploy an the HDFS-Spark-Hive cluster, run:
```
  docker-compose up
```

`docker-compose` creates a docker network that can be found by running `docker network list`, e.g. `docker-hadoop-spark-hive_default`.

Run `docker network inspect` on the network (e.g. `docker-hadoop-spark-hive_default`) to find the IP the hadoop interfaces are published on. Access these interfaces with the following URLs:

* Namenode: http://<dockerhadoop_IP_address>:9870/dfshealth.html#tab-overview
* History server: http://<dockerhadoop_IP_address>:8188/applicationhistory
* Datanode: http://<dockerhadoop_IP_address>:9864/
* Nodemanager: http://<dockerhadoop_IP_address>:8042/node
* Resource manager: http://<dockerhadoop_IP_address>:8088/
* Spark master: http://<dockerhadoop_IP_address>:8080/
* Spark worker: http://<dockerhadoop_IP_address>:8081/
* Hive: http://<dockerhadoop_IP_address>:10000

And you can break it all down again by going to that same directory and running this:

**docker-compose down**

All the containers will then be stopped and removed. But: the images and volumes stay! So don’t be surprised that the csv file you uploaded to HDFS will still be there.

## Important note regarding Docker Desktop
Since Docker Desktop turned “Expose daemon on tcp://localhost:2375 without TLS” off by default there have been all kinds of connection problems running the complete docker-compose. Turning this option on again (Settings > General > Expose daemon on tcp://localhost:2375 without TLS) makes it all work. I’m still looking for a more secure solution to this.


## Quick Start HDFS

Copy police.csv to the namenode.
```
  docker cp police.csv namenode:police.csv
```

Go to the bash shell on the namenode with that same Container ID of the namenode.
```
  docker exec -it namenode bash
```


Create a HDFS directory /data//crimerecord/police.

```
  hdfs dfs -mkdir -p /data/crimerecord/police
```

Copy police.csv to HDFS:
```
  hdfs dfs -put police.csv /data/crimerecord/police/police.csv
```

Now that you’ve successfully uploaded the `police.csv` file into HDFS, the next step is to process and analyze the data. Let’s walk through the steps to perform data analysis using Hadoop and its ecosystem:

---

### **1. Verify the File in HDFS**
Run the following command to verify that the file has been uploaded successfully:
```bash
hdfs dfs -ls /data/crimerecord/police
```

You should see the file `police.csv` listed.

---

### **2. Check the File Content**
Use the following command to confirm the contents of the file:
```bash
hdfs dfs -cat /data/crimerecord/police/police.csv
```

![image](https://github.com/user-attachments/assets/ed7ef025-dfd1-4be8-881a-8af0cc941eb8)

### ** Set Up Hive for Querying the Data**
Go to the command line of the Hive server and start hiveserver2

```
  docker exec -it hive-server bash

  hiveserver2
```

Maybe a little check that something is listening on port 10000 now
```
  netstat -anp | grep 10000
tcp        0      0 0.0.0.0:10000           0.0.0.0:*               LISTEN      446/java

```

Okay. Beeline is the command line interface with Hive. Let's connect to hiveserver2 now.

```
  beeline -u jdbc:hive2://localhost:10000 -n root
  
  !connect jdbc:hive2://127.0.0.1:10000 scott tiger
```

Didn't expect to encounter scott/tiger again after my Oracle days. But there you have it. Definitely not a good idea to keep that user on production.

Not a lot of databases here yet.
```
  show databases;
  
+----------------+
| database_name  |
+----------------+
| default        |
+----------------+
1 row selected (0.335 seconds)
```

Since you have Hive installed, let's create a Hive table and load the data into it for analysis.

**Step 1**: Access the Hive CLI.
```bash
hive
```

**Step 2**: Create a database for your project.
```sql
CREATE DATABASE police_analysis;
USE police_analysis;
```

**Step 3**: Create a table for the data in `police.csv`. Assume the dataset has the following schema:
- `Crime_ID` (INT)
- `Crime_Type` (STRING)
- `Location` (STRING)
- `Reported_Date` (STRING)
- `Status` (STRING)

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

**Step 4**: Load the data into the Hive table.
```sql
LOAD DATA INPATH '/data/crimerecord/police/police.csv' INTO TABLE police_data;
```

---

### **4. Analyze the Data**
Now you can use SQL queries in Hive to perform data analysis. Here are some example queries:

```sql
select * from police_data limit 10;
```

**Query 1: Count total crimes in the dataset.**
```sql
SELECT COUNT(*) AS Total_Crimes FROM police_data;
```

**Query 2: Find the most common types of crimes.**
```sql
SELECT Crime_Type, COUNT(*) AS Occurrences
FROM police_data
GROUP BY Crime_Type
ORDER BY Occurrences DESC;
```

**Query 3: Identify the top locations with the highest crime rates.**
```sql
SELECT Location, COUNT(*) AS Total_Crimes
FROM police_data
GROUP BY Location
ORDER BY Total_Crimes DESC;
```

**Query 4: Find the number of unresolved cases.**
```sql
SELECT Status, COUNT(*) AS Count
FROM police_data
WHERE Status != 'Closed'
GROUP BY Status;
```

**Query 5: Analyze crimes over time (if dates are formatted properly).**
```sql
SELECT SUBSTR(Reported_Date, 1, 10) AS Crime_Date, COUNT(*) AS Crimes_On_That_Day
FROM police_data
GROUP BY SUBSTR(Reported_Date, 1, 10)
ORDER BY Crime_Date;
```

---


There you go: your private Hive server to play with.

### **5. Visualize the Results (Optional)**
Export the results of the queries for visualization:
```bash
hive -e "SELECT * FROM police_data;" > police_analysis_results.csv
```

You can then use tools like Tableau, Excel, or Python (e.g., Matplotlib, Pandas) to create graphs, charts, or dashboards.

---



![image](https://github.com/user-attachments/assets/bdf19b8e-2dd9-43ab-a029-d8c546ffe68b)




## Quick Start Spark (PySpark)

Go to http://<dockerhadoop_IP_address>:8080 or http://localhost:8080/ on your Docker host (laptop) to see the status of the Spark master.

Go to the command line of the Spark master and start PySpark.
```
  docker exec -it spark-master bash

  /spark/bin/pyspark --master spark://spark-master:7077
```

Load breweries.csv from HDFS.
```
  brewfile = spark.read.csv("hdfs://namenode:9000/data/crimerecord/police/police.csv")
  
  brewfile.show()

**OUTPUT HERE**

only showing top 20 rows

```


## Quick Start Spark (Scala)

Go to http://<dockerhadoop_IP_address>:8080 or http://localhost:8080/ on your Docker host (laptop) to see the status of the Spark master.

Go to the command line of the Spark master and start spark-shell.
```
  docker exec -it spark-master bash
  
  spark/bin/spark-shell --master spark://spark-master:7077
```

Load breweries.csv from HDFS.
```
  val df = spark.read.csv("hdfs://namenode:9000/data/crimerecord/police/police.csv")
  
  df.show()

**OUTPUT HERE**

only showing top 20 rows

```

How cool is that? Your own Spark cluster to play with.


## Configure Environment Variables

The configuration parameters can be specified in the hadoop.env file or as environmental variables for specific services (e.g. namenode, datanode etc.):
```
  CORE_CONF_fs_defaultFS=hdfs://namenode:8020
```

CORE_CONF corresponds to core-site.xml. fs_defaultFS=hdfs://namenode:8020 will be transformed into:
```
  <property><name>fs.defaultFS</name><value>hdfs://namenode:8020</value></property>
```
To define dash inside a configuration parameter, use triple underscore, such as YARN_CONF_yarn_log___aggregation___enable=true (yarn-site.xml):
```
  <property><name>yarn.log-aggregation-enable</name><value>true</value></property>
```

The available configurations are:
* /etc/hadoop/core-site.xml CORE_CONF
* /etc/hadoop/hdfs-site.xml HDFS_CONF
* /etc/hadoop/yarn-site.xml YARN_CONF
* /etc/hadoop/httpfs-site.xml HTTPFS_CONF
* /etc/hadoop/kms-site.xml KMS_CONF
* /etc/hadoop/mapred-site.xml  MAPRED_CONF

If you need to extend some other configuration file, refer to base/entrypoint.sh bash script.
