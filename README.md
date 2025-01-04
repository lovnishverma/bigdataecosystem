![image](https://github.com/user-attachments/assets/343cfd7e-73b7-4eb2-a9a4-76c31f5703c8)

# Docker Multi-Container Environment with Hadoop, Spark, and Hive

This guide helps you set up a multi-container environment using Docker for Hadoop (HDFS), Spark, and Hive. The setup is lightweight, without the large memory requirements of a Cloudera sandbox.

## **Prerequisites**

Before you begin, ensure you have the following installed:

- **Docker**: [Install Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

- IMPORTANT:
  ******- Enable the "Expose daemon on tcp://localhost:2375 without TLS" option if you're using Docker Desktop for compatibility.******

 - **Git**: [Download Git](https://git-scm.com/downloads/win)
   - Git is used to download the required files from a repository.
  
   Create a newfolder and open it in terminal or go inside it using CD Command
  
 ![image](https://github.com/user-attachments/assets/28602a4b-52e2-4265-bfb5-a08301fda7b8)


## **Step 1: Clone the Repository**

First, clone the GitHub repository that contains the necessary Docker setup files.

```bash
git clone https://github.com/lovnishverma/bigdataecosystem.git
```
 
[or Directly download zip from my repo](https://github.com/lovnishverma/BigDataecosystem)

Navigate to the directory:

```bash
cd bigdataecosystem
```

![image](https://github.com/user-attachments/assets/e4d6a8ab-3f36-424a-bf13-9402bc1c13a2)

if downloaded zip than cd bigdataecosystem-main

## **Step 2: Start the Cluster**

Use Docker Compose to start the containers in the background.

```bash
docker-compose up -d
```

This command will launch the Hadoop, Spark, and Hive containers.

## **Step 3: Verify Running Containers**

To check if the containers are running, use the following command:

```bash
docker ps
```

## **Step 4: Stop and Remove Containers**

When you are done, stop and remove the containers with:

```bash
docker-compose down
```

## **Step 5: Set Up HDFS**

### **Upload Files to HDFS**

To copy a file (e.g., `police.csv`) to the Hadoop cluster:

1. Copy the file into the namenode container:
    ```bash
    docker cp police.csv namenode:/police.csv
    ```

2. Access the namenode container's bash shell:
    ```bash
    docker exec -it namenode bash
    ```

3. Create a directory in HDFS and upload the file:
    ```bash
    hdfs dfs -mkdir -p /data/crimerecord/police
    hdfs dfs -put /police.csv /data/crimerecord/police/
    ```

## **Step 6: Set Up Spark**

### **Access the Spark Master UI**

- Open `http://localhost:8080` in your web browser to view the Spark Master UI.

### **Start Spark Shell**

To interact with Spark, start the Spark shell in the master container:

```bash
docker exec -it spark-master bash
spark/bin/spark-shell --master spark://spark-master:7077
```

### **Process Data in Spark**

To load and view data from HDFS using Spark:

```scala
val df = spark.read.csv("hdfs://namenode:8020/data/crimerecord/police/police.csv")
df.show()
```

## **Step 7: Set Up Hive**

### **Start Hive Server**

Access the Hive container and start the Hive Server:

```bash
docker exec -it hive-server bash
hiveserver2
```

Check if Hive is listening on port 10000:

```bash
netstat -anp | grep 10000
```

### **Connect to Hive Server**

Use Beeline to connect to the Hive server:

```bash
beeline -u jdbc:hive2://localhost:10000 -n root
```

Alternatively, use the following command for direct connection:

```bash
!connect jdbc:hive2://127.0.0.1:10000 scott tiger
```

### **Create Database and Table in Hive**

1. Create a new Hive database:
    ```sql
    CREATE DATABASE police_analysis;
    USE police_analysis;
    ```

2. Create a table based on the schema of the `police.csv` dataset:
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

3. Load the data into the Hive table:
    ```sql
    LOAD DATA INPATH '/data/crimerecord/police/police.csv' INTO TABLE police_data;
    ```

### **Query the Data in Hive**

Run SQL queries to analyze the data in Hive:

1. **View the top 10 rows:**
    ```sql
    SELECT * FROM police_data LIMIT 10;
    ```

2. **Count total crimes:**
    ```sql
    SELECT COUNT(*) AS Total_Crimes FROM police_data;
    ```

3. **Find most common crime types:**
    ```sql
    SELECT Crime_Type, COUNT(*) AS Occurrences
    FROM police_data
    GROUP BY Crime_Type
    ORDER BY Occurrences DESC;
    ```

4. **Identify locations with the highest crime rates:**
    ```sql
    SELECT Location, COUNT(*) AS Total_Crimes
    FROM police_data
    GROUP BY Location
    ORDER BY Total_Crimes DESC;
    ```

5. **Find unresolved cases:**
    ```sql
    SELECT Status, COUNT(*) AS Count
    FROM police_data
    WHERE Status != 'Closed'
    GROUP BY Status;
    ```

### **Visualize the Data (Optional)**

Export the query results to a CSV file for analysis in visualization tools:

```bash
hive -e "SELECT * FROM police_data;" > police_analysis_results.csv
```

You can use tools like Tableau, Excel, or Python (Matplotlib, Pandas) for data visualization.

## **Step 8: Configure Environment Variables (Optional)**

If you need to customize configurations, you can specify parameters in the `hadoop.env` file or as environmental variables for services (e.g., namenode, datanode, etc.). For example:

```bash
CORE_CONF_fs_defaultFS=hdfs://namenode:8020
```

This will be transformed into the following in the `core-site.xml` file:

```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://namenode:8020</value>
</property>
```

## **Conclusion**

You now have a fully functional Hadoop, Spark, and Hive cluster running in Docker. This environment is great for experimenting with big data processing and analytics in a lightweight, containerized setup.

---
