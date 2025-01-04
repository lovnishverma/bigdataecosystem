![image](https://github.com/user-attachments/assets/2b0a8b29-8287-446a-8a0c-8c1820ea0971) ![image](https://github.com/user-attachments/assets/343cfd7e-73b7-4eb2-a9a4-76c31f5703c8)  ![image](https://github.com/user-attachments/assets/04ad8a37-c3a0-4e62-a5c4-70c023992209)![image](https://github.com/user-attachments/assets/5a5fc24a-bc9d-4cc2-aab4-b651c59197d5)  ![image](https://github.com/user-attachments/assets/10b26b1e-614f-4ad7-966c-505e54825680)



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

![image](https://github.com/user-attachments/assets/8dc3ec44-84af-40f2-8056-92e5f3449919)


## **Step 3: Verify Running Containers**

To check if the containers are running, use the following command:

```bash
docker ps
```
![image](https://github.com/user-attachments/assets/f6897172-d14f-462a-95dd-ba46401b5dd7)


## **Step 4: Stop and Remove Containers**

When you are done, stop and remove the containers with:

```bash
docker-compose down
```
![image](https://github.com/user-attachments/assets/fd1f2298-7d65-4055-a929-12de4d01c428)


## **Step 5: Set Up HDFS**

### **Upload Files to HDFS**

To copy a file (e.g., `police.csv`) to the Hadoop cluster:

1. Copy the file into the namenode container:
    ```bash
    docker cp police.csv namenode:/police.csv
    ```
![image](https://github.com/user-attachments/assets/496c7e6a-41d6-44d2-9557-b6004fe986c4)


2. Access the namenode container's bash shell:
    ```bash
    docker exec -it namenode bash
    ```
![image](https://github.com/user-attachments/assets/d501a9b3-d2d9-4e2d-aecb-8e3eb7ccf678)


3. Create a directory in HDFS and upload the file:
    ```bash
    hdfs dfs -mkdir -p /data/crimerecord/police
    hdfs dfs -put /police.csv /data/crimerecord/police/
    ```
![image](https://github.com/user-attachments/assets/ab68bba9-92f2-4b15-a50e-f3ee1a0f998e)


## **Step 6: Set Up Spark**

### **Access the Spark Master UI**

- Open `http://localhost:8080` in your web browser to view the Spark Master UI.

- ![image](https://github.com/user-attachments/assets/a824047c-f50c-494c-8429-d2bebd58870b)


### **Start Spark Shell**

To interact with Spark, start the Spark shell in the master container:

```bash
docker exec -it spark-master bash

spark/bin/spark-shell --master spark://spark-master:7077
```

![image](https://github.com/user-attachments/assets/45765d5e-b1e7-4726-a60c-ddd5dd278c93)

![image](https://github.com/user-attachments/assets/b071335b-4928-491a-8bed-321995881d83)



### **Process Data in Spark**

To load and view data from HDFS using Spark:

```scala
val df = spark.read.csv("hdfs://namenode:9000/data/crimerecord/police/police.csv")
df.show()
```
![image](https://github.com/user-attachments/assets/4101596d-da55-4cd2-b4de-02a8f7f0299a)

## **Step 7: Set Up Hive**

### **Start Hive Server**

Access the Hive container and start the Hive Server:

```bash
docker exec -it hive-server bash
```

```bash
hive
```

Check if Hive is listening on port 10000:
![image](https://github.com/user-attachments/assets/dc1e78d4-d903-4ac5-9eaa-eff0b893d6fb)


```bash
netstat -anp | grep 10000
```
![image](https://github.com/user-attachments/assets/9ac08fd3-f515-448d-83b3-c620fa3b15c2)


### **Connect to Hive Server**

Use Beeline to connect to the Hive server:

```bash
beeline -u jdbc:hive2://localhost:10000 -n root
```
![image](https://github.com/user-attachments/assets/d2dce309-0334-4a64-b8df-8cb6206b1432)


Alternatively, use the following command for direct connection:

```bash
beeline
```

```bash
!connect jdbc:hive2://127.0.0.1:10000 scott tiger
```

![image](https://github.com/user-attachments/assets/77fadb1f-118e-4d15-8a78-e9783baa9690)


### **Create Database and Table in Hive**

1. Create a new Hive database:
    ```sql
    CREATE DATABASE punjab_police;
    USE punjab_police;
    ```
![image](https://github.com/user-attachments/assets/73227817-b2d5-4df0-a392-6927750d7220)


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
    ![image](https://github.com/user-attachments/assets/13faa21a-5242-4f1e-bd69-4d98dc318400)


3. Load the data into the Hive table:
    ```sql
    LOAD DATA INPATH '/data/crimerecord/police/police.csv' INTO TABLE police_data;
    ```
![image](https://github.com/user-attachments/assets/e0fcbe55-d5fd-4a8c-a17b-df888204915f)


### **Query the Data in Hive**

Run SQL queries to analyze the data in Hive:

1. **View the top 10 rows:**
    ```sql
    SELECT * FROM police_data LIMIT 10;
    ```
![image](https://github.com/user-attachments/assets/6f189765-24f4-47db-ad70-42fbcfb4068e)


2. **Count total crimes:**
    ```sql
    SELECT COUNT(*) AS Total_Crimes FROM police_data;
    ```
![image](https://github.com/user-attachments/assets/8b56a8b5-6b0b-4306-82da-4cce52b50e95)


3. **Find most common crime types:**
    ```sql
    SELECT Crime_Type, COUNT(*) AS Occurrences
    FROM police_data
    GROUP BY Crime_Type
    ORDER BY Occurrences DESC;
    ```

   ![image](https://github.com/user-attachments/assets/54f000f7-36ec-4672-8bc6-996ac7b4004b)


4. **Identify locations with the highest crime rates:**
    ```sql
    SELECT Location, COUNT(*) AS Total_Crimes
    FROM police_data
    GROUP BY Location
    ORDER BY Total_Crimes DESC;
    ```
![image](https://github.com/user-attachments/assets/fb418097-97ff-46aa-941a-4b72a0702d3d)


5. **Find unresolved cases:**
    ```sql
    SELECT Status, COUNT(*) AS Count
    FROM police_data
    WHERE Status != 'Closed'
    GROUP BY Status;
    ```
![image](https://github.com/user-attachments/assets/9b3b32df-38c9-45bd-85dc-c4ac2b16b246)


**********There you go: your private Hive server to play with.**********

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
