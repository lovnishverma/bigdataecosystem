**If you've already done all the steps and want to start existing container and HDFS and YARN use the following commands**  
   If the container `nielit-container` is stopped and you want to use it, restart it:
   ```bash
   docker start nielit-container
   ```
   Then, attach to it using:

   ```bash
   docker exec -it nielit-container /bin/bash
   ```
   ```bash
   service ssh start
   ```
   ```bash
   start-dfs.sh
   ```
   ```bash
   start-yarn.sh
   ```
   ```bash
   jps
   ```
   You should see processes like `NameNode`, `ResourceManager`, `DataNode`, and `SecondaryNameNode` if everything is working correctly.
![image](https://github.com/user-attachments/assets/9390861d-5ae3-4b40-9519-894196d5a4ee)

**To Start MySQL Service:**

   ```bash
   service mysql start
   ```
   ---

And if you're done working **always stop all Hadoop nodes and the Docker container**, to do so follow these steps:

---

### **1. Stop Hadoop Nodes**

Run the following commands to stop the Hadoop services gracefully:

```bash
stop-yarn.sh
stop-dfs.sh
```
Or

```bash
stop-all.sh
```
then run jps command ensure only jps is listed and all the nodes are stoped properlly....
![image](https://github.com/user-attachments/assets/18cdfcfb-692e-47c5-b9b9-a4cb971b3668)

This will stop the YARN ResourceManager, NodeManager, NameNode, and DataNode.

---

### **2. Stop the Docker Container**

To stop the running Docker container, first, list all running containers:

```bash
docker ps
```

This will display a list of running containers with their **CONTAINER ID**.

Stop the container using the following command:

```bash
docker stop <CONTAINER_ID_OR_NAME>
```

For example, if your container is named `nielit-container`, use:

```bash
docker stop nielit-container
```
![image](https://github.com/user-attachments/assets/06fb48ab-b8aa-4c41-8926-a867f03490a4)

# **Complete Guide to Install Ubuntu and JAVA and then Configure Hadoop, MySQL, HIVE, Sqoop, Flume, Spark on a Docker Container**

This guide outlines the steps to install Ubuntu and JAVA and configure **Hadoop**,**MySQL**, **HIVE**, **Sqoop**, **Flume**, **Spark** in a Docker container for big data operations.

1. **Check Existing Containers**  
   List all containers (including stopped ones) to confirm the presence of `nielit-container`:
   ```bash
   docker ps -a
   ```

   Look for the container named `nielit-container` in the output.

2. **Restart the Existing Container (If You Want to Reuse It)**  
   If the container `nielit-container` is stopped and you want to use it, restart it:
   ```bash
   docker start nielit-container
   ```

   Then, attach to it using:
   ```bash
   docker exec -it nielit-container /bin/bash
   ```

3. **Remove the Existing Container (If You Want to Create a New One)**  
   If you no longer need the existing `nielit-container`, remove it:
   ```bash
   docker rm nielit-container
   ```

   After removing it, you can create a new container with the same name:
   ```bash
   docker run -it -p 9870:9870 -p 9000:9000 -p 8088:8088 -p 8080:8080 --name nielit-container ubuntu:20.04 bash
   ```

4. **Use a Different Container Name**  
   If you don’t want to delete the existing container, create a new one with a different name:
   ```bash
   docker run -it -p 9870:9870 -p 9000:9000 -p 8088:8088 -p 8080:8080 --name nielit-container2 ubuntu:20.04 bash

   ```

### Additional Notes
- **Check Container Logs**  
   If the container failed previously, check its logs to identify issues:
   ```bash
   docker logs nielit-container
   ```

- **Inspect Container Details**  
   To investigate the container further:
   ```bash
   docker inspect nielit-container
   ``` 

## **Step 1: Set up a Fresh Docker Container**

1. **Create and Start a New Ubuntu Container:**
   ```bash
   docker run -it -p 9870:9870 -p 9000:9000 -p 8088:8088 -p 8080:8080 --name nielit-container ubuntu:20.04 bash
   ```

2. **Update Package Lists:**
   ```bash
   apt-get update
   ```
![image](https://github.com/user-attachments/assets/711d5e9c-fc73-41d8-bf3f-e65d55e348c8)

---

## **Step 2: Install Java (OpenJDK 8) This Step will Take some time**

1. **Install OpenJDK 8:**
   ```bash
   apt-get install openjdk-8-jdk -y
   ```
![image](https://github.com/user-attachments/assets/6c7edc75-61fb-4f5e-a3fb-2e23d823cf84)


2. **Verify Java Installation:**
   ```bash
   java -version
   ```
![image](https://github.com/user-attachments/assets/a838c0ec-7f92-454c-af88-6731101cb514)


3. **Set `JAVA_HOME` Environment Variable:**
   Open `.bashrc`:
   ```bash
   apt-get install nano -y
   nano ~/.bashrc
   ```

   Add these lines:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   export PATH=$JAVA_HOME/bin:$PATH
   ```

4. **Apply the Changes:**
   ```bash
   source ~/.bashrc
   ```

---

## **Step 3: Install Hadoop**

1. **Install `wget` for File Downloads:**
   ```bash
   apt-get install wget -y
   ```

2. **Download and Extract Hadoop:**
   ```bash
   wget https://dlcdn.apache.org/hadoop/common/hadoop-2.10.2/hadoop-2.10.2.tar.gz
   
   tar -xzf hadoop-2.10.2.tar.gz -C /opt
   
   mv /opt/hadoop-2.10.2 /opt/hadoop
   ```

3. **Set Hadoop Environment Variables:**
   Open `.bashrc`:
   ```bash
   nano ~/.bashrc
   ```
   Add these lines:
   ```bash
   export HADOOP_HOME=/opt/hadoop
   export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
   ```

   Apply changes:
   ```bash
   source ~/.bashrc
   ```

4. **Verify Hadoop Installation:**
   ```bash
   hadoop version
   ```
![image](https://github.com/user-attachments/assets/cf113dae-d86b-4d01-a6c3-4ccfa3e6d371)


---

To ensure proper configuration of Hadoop, you need to edit some configuration files. Here are the configurations for `core-site.xml`, `hdfs-site.xml`, and `yarn-site.xml`.

### **1. Edit `core-site.xml`**

Run the following command to open the `core-site.xml` file:

```bash
nano /opt/hadoop/etc/hadoop/core-site.xml
```

Add the following configuration:

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

### **2. Edit `hdfs-site.xml`**

Run the following command to open the `hdfs-site.xml` file:

```bash
nano /opt/hadoop/etc/hadoop/hdfs-site.xml
```

Add the following configuration:

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>

    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///tmp/hadoop-${user.name}/dfs/data</value>
    </property>
</configuration>
```

### **3. Edit `yarn-site.xml`**

Run the following command to open the `yarn-site.xml` file:

```bash
nano /opt/hadoop/etc/hadoop/yarn-site.xml
```

Add the following configuration:

```xml
<configuration>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>localhost:8032</value>
    </property>

    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>localhost:8088</value>
    </property>
</configuration>
```

### **Save and Exit:**

After adding the configurations:
1. Press `CTRL + O` to save the file.
2. Press `Enter` to confirm.
3. Press `CTRL + X` to exit the editor.

This should properly configure your Hadoop system.


--- 

4. **Set `JAVA_HOME` in Hadoop:**
   Edit `hadoop-env.sh`:
   ```bash
   nano /opt/hadoop/etc/hadoop/hadoop-env.sh
   ```
   Add the line:
   ```bash
   export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
   ```
5. **Format NameNode:**
   ```bash
   hdfs namenode -format
   ```
![image](https://github.com/user-attachments/assets/7eb773ae-4e8c-484f-9be8-b018504190f9)


### 1. **Install SSH**

Hadoop requires SSH to be installed on all nodes in the cluster (even if you're running everything on a single node). Install SSH using the following commands:

```bash
apt update
apt install openssh-client openssh-server
```

After installing SSH, make sure that the `ssh` command is available:

```bash
ssh -v localhost
```

If this works, SSH is properly set up if not then start SSH Daemon.

### 2. **Start SSH Daemon**

Make sure the SSH daemon is running. You can start it using:

```bash
service ssh start
```

Then, ensure SSH is properly configured for passwordless login. You can do this by creating an SSH key and copying it to the `~/.ssh/authorized_keys` file on the same machine (or between nodes if you have a multi-node setup).

To generate the SSH key pair (Do not enter yes and don't enter anything keep blank):

```bash
ssh-keygen -t rsa
```

Then, copy the public key to the authorized keys:

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

### 3. **Verify Hadoop Configuration**

Ensure that your Hadoop `slaves` configuration is correct. By default, Hadoop expects a list of slave nodes (if you are running a multi-node setup), but since you’re on a single node, make sure the `slaves` file located at `$HADOOP_HOME/etc/hadoop/slaves` contains `localhost` or the hostname of your machine.

Edit the `slaves` file:

```bash
nano $HADOOP_HOME/etc/hadoop/slaves
```

Ensure it contains:

```text
localhost
```

It will ask you Are you sure you want to continue connecting (yes/no/[fingerprint])? yes ...Enter yes

```bash
start-all.sh
```
then verify

```bash
jps
```

You should see processes like `NameNode`, `DataNode`, and `SecondaryNameNode` if everything is working correctly.

Start ResourceManager
If the ResourceManager is not running, try starting it manually:

```bash
start-yarn.sh
```
Check the status of the HDFS services:

```bash
jps
```
![image](https://github.com/user-attachments/assets/ff73c6bb-b9d3-4f50-8653-3ff173a1f45f)


You should see processes like `NameNode`, `ResourceManager`, `DataNode`, and `SecondaryNameNode` if everything is working correctly.

![image](https://github.com/user-attachments/assets/250242f9-d48f-4ec2-b0da-4869cbb84927)


### 5. **Check HDFS Web UI**

Once the services are running, check the **ResourceManager and NodeManager Web UI**:

- **ResourceManager Web UI**: `http://localhost:8088`
- **NodeManager Web UI**: `http://localhost:8042`

![image](https://github.com/user-attachments/assets/76c37ea9-f385-41f2-94b3-15364250594c)


### 6. **Verify HDFS Access**

After ensuring that HDFS is running, check the HDFS directory:

```bash
hdfs dfs -ls /
```

It should now list the directories in the root of HDFS (e.g., `/user`, `/tmp`).

### Recap of Fixes:
- Install and configure SSH.
- Ensure SSH is running.
- Set up passwordless SSH.
- Check your `slaves` file for correct configuration.
- Restart Hadoop services (`start-dfs.sh`).
- Verify HDFS status with `jps` and `hdfs dfs -ls /`.



To install Hive and perform practical operations using Hive and HDFS, follow these steps. This guide assumes your Hadoop environment is already configured and running.

---

### **Step 1: Install Hive**

1. **Install Prerequisites:**
   Ensure `wget` is installed for downloading Hive:
   ```bash
   apt-get install wget -y
   ```

2. **Download Hive:**
   ```bash
   wget https://apache.root.lu/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz
   ```

3. **Extract Hive:**
   ```bash
   tar -xzf apache-hive-2.3.9-bin.tar.gz -C /opt
   mv /opt/apache-hive-2.3.9-bin /opt/hive
   ```

4. **Set Hive Environment Variables:**
   Open the `.bashrc` file:
   ```bash
   nano ~/.bashrc
   ```

   Add these lines:
   ```bash
   export HIVE_HOME=/opt/hive
   export PATH=$HIVE_HOME/bin:$PATH
   ```

   Apply the changes:
   ```bash
   source ~/.bashrc
   ```

5. **Verify Hive Installation:**
   ```bash
   hive --version
   ```
![image](https://github.com/user-attachments/assets/215219ff-7451-440a-9955-229647c69697)

---

### **Step 2: Configure Hive**

1. **Create Hive Directories in HDFS:**
   ```bash
   hdfs dfs -mkdir -p /user/hive/warehouse
   hdfs dfs -chmod g+w /user/hive/warehouse
   ```

2. **Configure Hive `hive-site.xml`:**
   Open the Hive configuration file:
   ```bash
   nano /opt/hive/conf/hive-site.xml
   ```

   Add the following configuration:
   ```xml
   <?xml version="1.0"?>
   <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
   <configuration>
       <property>
           <name>javax.jdo.option.ConnectionURL</name>
           <value>jdbc:derby:;databaseName=/opt/hive/metastore_db;create=true</value>
           <description>JDBC connection URL for the metastore database</description>
       </property>
       <property>
           <name>hive.metastore.warehouse.dir</name>
           <value>/user/hive/warehouse</value>
           <description>Location of default database for the warehouse</description>
       </property>
       <property>
           <name>hive.exec.scratchdir</name>
           <value>/tmp/hive</value>
           <description>Scratch directory for Hive jobs</description>
       </property>
   </configuration>
   ```

3. **Initialize Hive Metastore:**
   Run the following command to initialize Hive:
   ```bash
   schematool -initSchema -dbType derby
   ```

---

### **Step 3: Perform Practical Operations Using Hive and HDFS**

1. **Start Hive CLI:**
   ```bash
   hive
   ```
![image](https://github.com/user-attachments/assets/ce6eed93-0349-467d-b2ae-0d10196f54b0)


2. **Create a Database:**
   ```sql
   CREATE DATABASE mydatabase;
   ```

3. **Create a Table:**
   Example of creating an external table using HDFS:
   ```sql
   USE mydatabase;
   CREATE EXTERNAL TABLE students (
       id INT,
       name STRING,
       age INT
   )
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ','
   STORED AS TEXTFILE
   LOCATION '/user/hive/warehouse/students';
   ```

4. **Upload Data to HDFS:**
   Prepare a CSV file `students.csv` with the following content:
   ```
   1,John,22
   2,Jane,21
   3,Mark,23
   ```

   Upload the file to the HDFS location:
   ```bash
   hdfs dfs -put students.csv /user/hive/warehouse/students/
   ```
![image](https://github.com/user-attachments/assets/08f9251a-2163-44c1-aa80-5580023ffb93)


5. **Query Data Using Hive:**
   ```sql
   USE mydatabase;
   SELECT * FROM students;
   ```
![image](https://github.com/user-attachments/assets/fd5830ca-5b1c-460b-8446-d5bce78cd52f)

6. **Perform Aggregations:**
   ```sql
   SELECT age, COUNT(*) FROM students GROUP BY age;
   ```
![image](https://github.com/user-attachments/assets/4d121fc1-2e7d-4a11-bcd9-8bd803837d47)

7. **Load Data into Hive Table:**
   Create a managed table and load data into it:
   ```sql
   CREATE TABLE managed_students (
       id INT,
       name STRING,
       age INT
   )
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ',';

   LOAD DATA INPATH '/user/hive/warehouse/students/students.csv' INTO TABLE managed_students;
   ```

8. **Perform Advanced Queries:**
   Run queries like joins, filtering, and sorting:
   ```sql
   SELECT * FROM managed_students WHERE age > 21;
   ```
![image](https://github.com/user-attachments/assets/b1e75205-fa38-4534-8302-ab528cfe6fc5)

---

This setup and basic practical steps should get you started with Hive and HDFS. 

## ** Install MySQL**

1. **Install MySQL Server:**
   ```bash
   apt-get install mysql-server -y
   ```

2. **Start MySQL Service:**
   ```bash
   service mysql start
   ```

3. **Create a Test Database and User:**
   Log into MySQL:
   ```bash
   mysql -u root
   ```
   Run the following SQL commands:
   ```sql
   CREATE DATABASE testdb;
   CREATE USER 'sqoop_user'@'%' IDENTIFIED BY 'password123';
   GRANT ALL PRIVILEGES ON testdb.* TO 'sqoop_user'@'%';
   FLUSH PRIVILEGES;
   ```

---

Create a MySQL Table

First, let’s create a simple table called `employees` in your `testdb` database.

```sql
-- Select the database
USE testdb;

-- Create a table
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    hire_date DATE,
    salary DECIMAL(10, 2)
);
```

### 2. **Insert Test Data into the Table**
Now, let’s add some sample data to the `employees` table.

```sql
-- Insert sample data into the employees table
INSERT INTO employees (first_name, last_name, email, hire_date, salary) VALUES
('John', 'Doe', 'john.doe@example.com', '2020-01-15', 55000.00),
('Jane', 'Smith', 'jane.smith@example.com', '2019-03-22', 60000.00),
('Mike', 'Johnson', 'mike.johnson@example.com', '2021-07-10', 65000.00),
('Emily', 'Davis', 'emily.davis@example.com', '2018-12-05', 70000.00),
('David', 'Brown', 'david.brown@example.com', '2022-11-18', 48000.00);
```

### 3. **Verify the Data**
Once the data is inserted, you can run a `SELECT` query to verify the inserted data:

```sql
-- Verify the inserted data
SELECT * FROM employees;
```

### Example Output:

| id | first_name | last_name | email                  | hire_date  | salary  |
|----|------------|-----------|------------------------|------------|---------|
| 1  | John       | Doe       | john.doe@example.com    | 2020-01-15 | 55000.00|
| 2  | Jane       | Smith     | jane.smith@example.com  | 2019-03-22 | 60000.00|
| 3  | Mike       | Johnson   | mike.johnson@example.com| 2021-07-10 | 65000.00|
| 4  | Emily      | Davis     | emily.davis@example.com | 2018-12-05 | 70000.00|
| 5  | David      | Brown     | david.brown@example.com | 2022-11-18 | 48000.00|

![image](https://github.com/user-attachments/assets/1f9d2c98-7dbe-42f6-9c86-78ae4a2dfc8e)

```sql
EXIT;
```

## **Step 6: Install Sqoop**

1. **Download and Extract Sqoop:**
   ```bash
   wget https://archive.apache.org/dist/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
   tar -xzf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz -C /opt
   mv /opt/sqoop-1.4.7.bin__hadoop-2.6.0 /opt/sqoop
   ```

2. **Set Sqoop Environment Variables:**
   Open `.bashrc`:
   ```bash
   nano ~/.bashrc
   ```
   Add these lines:
   ```bash
   export SQOOP_HOME=/opt/sqoop
   export PATH=$SQOOP_HOME/bin:$PATH
   ```

   Apply changes:
   ```bash
   source ~/.bashrc
   ```

3. **Verify Sqoop Installation:**
   ```bash
   sqoop version
   ```
![image](https://github.com/user-attachments/assets/34ca8bd3-048a-4a9a-b76a-fbdb2f400f60)

---



To download the MySQL JDBC driver using `wget`, you can follow these steps:

### **Step 7: Download the MySQL JDBC Driver**
You can download the MySQL JDBC driver `.jar` file from the official MySQL website or from a Maven repository.

#### From MySQL's Official Site:
- Find the MySQL Connector/J download page: [MySQL Connector/J](https://dev.mysql.com/downloads/connector/j/)
- Direct download URL (for version 8.0.29 as an example):
  ```bash
  wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.29.tar.gz
  ```

#### From Maven Repository:
Alternatively, you can download it from Maven Central:
- Visit [MySQL Connector/J at Maven Central](https://mvnrepository.com/artifact/mysql/mysql-connector-java)
- Find the latest version, and use the direct download URL. For example, for version `8.0.29`:
  ```bash
  wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar
  ```

### 2. **Extract the JAR File**
If you downloaded a `.tar.gz` archive (from the MySQL website), you need to extract the `.jar` file:
```bash
tar -xvzf mysql-connector-java-8.0.29.tar.gz
ls
cd mysql-connector-java-8.0.29
```
This will extract the `mysql-connector-java-x.x.x.jar` file from the archive.

### 3. **Move the JAR File to Sqoop's lib Directory**
Once you have the `.jar` file, move it to Sqoop’s `lib` directory:
```bash
cp mysql-connector-java-8.0.29.jar /opt/sqoop/lib/
```

### 4. **Verify the Driver in Sqoop**
Now that the JDBC driver is in place, you can proceed with running your Sqoop command again:
```bash
sqoop list-databases --connect jdbc:mysql://localhost:3306 --username sqoop_user --password password123
```

## **Step 8: Test Sqoop with MySQL**

1. **Use Sqoop to List MySQL Databases (make sure service mysql start is running):**
   ```bash
   sqoop list-databases \
       --connect jdbc:mysql://localhost:3306 \
       --username sqoop_user --password password123
   ```

. **Expected Output:**
   A list of databases should appear, including `testdb`.
   
![image](https://github.com/user-attachments/assets/aea4f0b9-dff4-45e1-b2f3-4a54d66acfd8)



### 2. **Import the Table using Sqoop**
Now that you have a table with data, you can proceed with the Sqoop import:

```bash
sqoop import --connect jdbc:mysql://localhost:3306/testdb --username sqoop_user --password password123 --table employees --target-dir /user/hdfs/employees_data
```
![image](https://github.com/user-attachments/assets/9f530c7f-fba9-4c74-909c-d3def191920d)

### 3. **Append Incremental Import**
In **append** mode, Sqoop imports only new rows that have been added to the table since the last import.

#### Example Command for Append Incremental Import:
```bash
sqoop import --connect jdbc:mysql://localhost:3306/testdb \
             --username sqoop_user --password password123 \
             --table employees --target-dir /user/hdfs/employees_data \
             --incremental append --check-column id --last-value 100
```

### Explanation:
- **`--incremental append`**: Specifies that only new rows (added after the `--last-value`) should be imported.
- **`--check-column id`**: The column (`id`) used to track new rows. This should be a numeric column (e.g., auto-incrementing primary key) or any other column that can help identify new rows.
- **`--last-value 100`**: The last value of the `id` column that was imported in the previous run. This ensures that only rows with an `id > 100` will be imported.


### 4. **Check HDFS for Data**
After the Sqoop import completes successfully, you can check the HDFS directory to verify the imported data.

```bash
hadoop fs -ls /user/hdfs/employees_data
```
![image](https://github.com/user-attachments/assets/a7b28752-337a-4e75-820e-f6a02f9a2fdf)


This will show the files created in HDFS with the data from the `employees` table.


```bash
hadoop fs -cat /user/hdfs/employees_data/*
```

![image](https://github.com/user-attachments/assets/8806e578-b177-418c-847b-92e415e7a73b)

The `sqoop export` command is used to export data from HDFS back to a relational database. In your case, you have imported data into HDFS from the `testdb` MySQL database using the `sqoop import` command. Now, to export the data back into the MySQL database, you can use the following `sqoop export` command.

### Export Command Example:

## **🔥Clean the MySQL Table Before Export**
 **This will DELETE all records from MySQL and insert everything again.**  

### ✅ **Run This Command:**
```bash
mysql -u root -p
USE testdb;
TRUNCATE TABLE employees;
```

👉 export command:
```bash
sqoop export \
--connect jdbc:mysql://localhost:3306/testdb \
--username sqoop_user \
--password password123 \
--table employees \
--export-dir /user/hdfs/employees_data \
--input-fields-terminated-by ',' \
--lines-terminated-by '\n' \
--columns "id,first_name,last_name,email,hire_date,salary" \
--batch
```

👉 This will **completely clear the table** and insert fresh data.

## ✅ **Bonus Tip: Automate Sqoop Export Every Night 📅**
👉 Would you like me to create a **Shell Script** that will:
- ✅ Automatically export data from HDFS → MySQL every night at **12:00 AM**.  
- ✅ Ignore duplicates or update existing records.  
- ✅ Send you an email if the export fails.


## ✅ **Bonus Tip: Track Job Status on Web**
👉 You can also monitor the Sqoop export job on Hadoop web interface:  
```bash
http://localhost:8080
```

You can view:
- ✅ Job Status.
- ✅ Failed tasks.
- ✅ MapReduce output.


### Explanation of the Export Command:
- **`--connect jdbc:mysql://localhost:3306/testdb`**: The JDBC connection string to your MySQL database (`testdb`).
- **`--username sqoop_user`**: The MySQL username (`sqoop_user`) you created earlier.
- **`--password password123`**: The password for the `sqoop_user`.
- **`--table employees`**: The name of the table in the database where the data will be exported. Ensure that the `employees` table exists in the `testdb` database.
- **`--export-dir /user/hdfs/employees_data`**: The HDFS directory from which Sqoop will read the data. This should be the directory where the data was stored during the import operation.

### Notes:
- Make sure the HDFS directory (`/user/hdfs/employees_data`) contains the data in a format that is compatible with the MySQL table structure. For example, if you imported data into HDFS using a default text format, ensure that the `employees` table in MySQL matches the column structure of the data.
- If the `employees` table does not exist, you may need to create it manually in MySQL or use the `--create-table` flag in Sqoop to automatically create it based on the data.

You can now perform practical operations like running queries, etc., with this setup.

---

To stop all Hadoop nodes and the Docker container, follow these steps:

---



### **. Stop Hadoop Nodes**

Run the following commands to stop the Hadoop services gracefully:

```bash
stop-yarn.sh
stop-dfs.sh
```

This will stop the YARN ResourceManager, NodeManager, NameNode, and DataNode.

---

### **2. Stop the Docker Container**

To stop the running Docker container, first, list all running containers:

```bash
docker ps
```

This will display a list of running containers with their **CONTAINER ID**.

Stop the container using the following command:

```bash
docker stop <CONTAINER_ID_OR_NAME>
```

For example, if your container is named `nielit-container2`, use:

```bash
docker stop nielit-container2
```

---

### **3. Optionally, Remove the Container**

If you want to remove the container completely (if it’s no longer needed), use:

```bash
docker rm <CONTAINER_ID_OR_NAME>
```

---

### **4. Stop All Running Docker Containers (Optional)**

If you want to stop all running Docker containers at once, use:

```bash
docker stop $(docker ps -q)
```

To remove all stopped containers:

```bash
docker rm $(docker ps -aq)
```

---

These commands will stop all Hadoop nodes and the associated Docker container.

Here is a complete step-by-step guide to install Apache Flume on top of your Hadoop setup and demonstrate a working example:

---

### **Step 1: Install Apache Flume**

1. **Download Apache Flume**  
   Visit the official Apache Flume [download page](https://flume.apache.org/download.html) or use `wget` to download the latest binary tarball directly:  
   ```bash
   wget https://archive.apache.org/dist/flume/1.9.0/apache-flume-1.9.0-bin.tar.gz
   ```

2. **Extract the Tarball**  
   Extract the downloaded tarball:
   ```bash
   tar -xvzf apache-flume-1.9.0-bin.tar.gz
   ```

3. **Move the Folder**  
   Move the extracted folder to `/usr/local/flume`:
   ```bash
   mv apache-flume-1.9.0-bin /usr/local/flume
   ```

4. **Set Environment Variables**  
   Add Flume to your `PATH` by editing the `~/.bashrc` file:
   ```bash
   nano ~/.bashrc
   ```
   Add the following lines at the end of the file:
   ```bash
   export FLUME_HOME=/usr/local/flume
   export PATH=$PATH:$FLUME_HOME/bin
   ```
   Reload the environment variables:
   ```bash
   source ~/.bashrc
   ```

5. **Verify Installation**  
   Check Flume's version:
   ```bash
   flume-ng version
   ```
![image](https://github.com/user-attachments/assets/14fd9825-4efe-4c17-9167-3feab67710ac)

---

### **Step 2: Configure Flume**

1. Navigate to the Flume configuration directory:
   ```bash
   cd /usr/local/flume/conf
   ```

2. Create a new Flume agent configuration file:
   ```bash
   nano demo-agent.conf
   ```

3. Add the following content to define the Flume agent configuration:
   ```properties
   # Define the agent components
   demo.sources = source1
   demo.sinks = sink1
   demo.channels = channel1

   # Define the source
   demo.sources.source1.type = netcat
   demo.sources.source1.bind = localhost
   demo.sources.source1.port = 44444

   # Define the sink (HDFS)
   demo.sinks.sink1.type = hdfs
   demo.sinks.sink1.hdfs.path = hdfs://localhost:9000/user/flume/demo
   demo.sinks.sink1.hdfs.fileType = DataStream

   # Define the channel
   demo.channels.channel1.type = memory
   demo.channels.channel1.capacity = 1000
   demo.channels.channel1.transactionCapacity = 100

   # Bind the source and sink to the channel
   demo.sources.source1.channels = channel1
   demo.sinks.sink1.channel = channel1
   ```

   Replace `localhost` with your Hadoop Namenode hostname or IP address.
you can find it using cat $HADOOP_HOME/etc/hadoop/core-site.xml
---

### **Step 3: Start Flume Agent**

Run the Flume agent using the configuration file:
```bash
flume-ng agent \
--conf /usr/local/flume/conf \
--conf-file /usr/local/flume/conf/demo-agent.conf \
--name demo \
-Dflume.root.logger=INFO,console
```

This starts the Flume agent with the name `demo` and logs activities to the console.

![image](https://github.com/user-attachments/assets/8dcae12e-2b1f-490b-ae07-f052040b3c7d)

---
If you're facing error `bash: nc: command not found` indicates that the `netcat` (`nc`) utility is not installed in your container. Netcat is required to send data to the Flume source.

### **Steps to Resolve**

1. **Install Netcat in the Container**
   - Install `netcat` using the package manager inside the container:
     ```bash
     apt-get update
     apt-get install netcat -y
     ```
   - Verify the installation:
     ```bash
     nc -h
     ```

2. **Test the Netcat Command Again**
   After installing `netcat`, retry the command to send data to Flume:
   ```bash
   echo "Hello Flume Demo" | nc localhost 44444
   ```

3. **Verify Data in Flume Sink**
   - Check the configured HDFS path or the file sink location to verify that the message has been captured by the Flume agent.

---


### **Step 4: Test Flume Data Flow**

1. **Send Data to Flume Source**  
   Open another terminal and send data to the Netcat source using the `nc` command:
   ```bash
   echo "Hello Flume Demo" | nc localhost 44444
   ```
![image](https://github.com/user-attachments/assets/1290fb3e-cdac-4265-8c3a-067265783963)

### ****   Send multiple lines of data:****
   
###  Use a `while` loop to keep sending data

```bash
while true; do
  for i in {1..5}; do
    echo "This is message $i" | nc -q 0 localhost 44444
    sleep 1  # Optional: to add a delay between messages
  done
done
```

**or**

### Use `nc` in Listening Mode

```bash
for i in {1..5}; do
  echo "This is message $i"
done | nc -q 0 localhost 44444
```

This sends multiple lines at once, and `-q 0` ensures that `nc` closes the connection after the data is sent.


![image](https://github.com/user-attachments/assets/0ebb56f2-04b8-4071-8578-67d9ed01f1d9)


1. **Verify Data in HDFS**  
   Check the HDFS directory where Flume is writing data:
   ```bash
   hadoop fs -ls /user/flume/demo
   ```
   View the ingested data files:
   ```bash
   hadoop fs -cat /user/flume/demo/*
   ```

   You should see the messages sent via `Netcat`.
![image](https://github.com/user-attachments/assets/9460b9d8-8ba4-4788-a318-a55bac5a27d3)

---

### **Step 5: Optional Customizations**

1. **Roll Policies**  
   Adjust roll policies in the sink configuration:
   - **Roll by file size**:  
     ```properties
     demo.sinks.sink1.hdfs.rollSize = 1048576  # 1MB
     ```
   - **Roll by time interval**:  
     ```properties
     demo.sinks.sink1.hdfs.rollInterval = 300  # 5 minutes
     ```
   - **Roll by event count**:  
     ```properties
     demo.sinks.sink1.hdfs.rollCount = 1000
     ```

2. **Monitoring and Logging**  
   Configure monitoring and logging in `flume-env.sh` and `log4j.properties`.

---

### **Expected Results**

1. **Flume Console Output**  
   You will see logs showing Flume processing events and writing them to HDFS.

2. **HDFS Data**  
   The ingested data in HDFS will look like this:
   ```
   Hello Flume Demo
   This is message 1
   This is message 2
   This is message 3
   ```

---

### **Troubleshooting**

- **Agent Fails to Start**:  
   Check the logs for configuration errors:
   ```bash
   cat /usr/local/flume/logs/flume.log
   ```

- **Data Not in HDFS**:  
   Ensure the `namenode_host` in the sink configuration is correct and that the HDFS path is writable.

---



******If you want to install Apache Spark in the same Docker container where Hadoop is already installed, you can follow these steps:******

### Step 1: Access Your Existing Hadoop Container
First, access the running Hadoop container using the `docker exec` command:

```bash
docker exec -it <hadoop-container-name> /bin/bash
```

This will open a shell inside the container.

### Step 2: Install Apache Spark in the Container
Once inside the container, you'll need to download and install Apache Spark.

1. **Download Spark**:  
   Use `wget` or `curl` to download the latest Apache Spark release. For example, to download Spark 3.x (make sure to adjust the version number if needed):

   ```bash
   wget https://archive.apache.org/dist/spark/spark-3.5.1/spark-3.5.1-bin-hadoop3.tgz
   ```

2. **Extract the tar file**:

   ```bash
   tar -xvzf spark-3.5.1-bin-hadoop3.tgz
   ```

3. **Move Spark to a proper directory** (optional, for better organization):

   ```bash
   mv spark-3.5.1-bin-hadoop3 /opt/spark
   ```

4. **Set Environment Variables**:  
   Add the Spark environment variables to your `.bashrc` or `.bash_profile` so they persist across sessions:

   ```bash
   echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc
   echo 'export PATH=$SPARK_HOME/bin:$PATH' >> ~/.bashrc
   echo 'export SPARK_MASTER_URL=spark://localhost:7077' >> ~/.bashrc
   source ~/.bashrc
   ```

### Step 3: Set Up Hadoop Configuration in Spark

Spark needs to be configured to communicate with Hadoop HDFS. You need to point Spark to the Hadoop configuration directory. Assuming your Hadoop configuration files are in `/etc/hadoop/conf`, you can set the `HADOOP_CONF_DIR` environment variable to this path.

1. **Set the Hadoop configuration directory for Spark**:

   ```bash
   echo 'export HADOOP_CONF_DIR=/opt/hadoop/etc/hadoop' >> ~/.bashrc
   source ~/.bashrc
   ```

2. **Check if Hadoop configuration files are available**:  
   Ensure that the Hadoop configuration files such as `core-site.xml`, `hdfs-site.xml`, and others are present in `/opt/hadoop/etc/hadoop`.

### Step 4: Verify the Installation

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

### Step 5: Run a Spark Job

Once Spark is installed and running, you can test it by submitting a Spark job.

1. **Submit a job** (for example, a simple PySpark job):

   ```bash
   $SPARK_HOME/bin/spark-submit --master spark://localhost:7077 /path/to/your/spark-job.py
   ```

   Make sure to replace `/path/to/your/spark-job.py` with the actual path to your Spark job.

2. **Monitor the job**:  
   You can monitor the job's progress through the Spark Web UI at `http://localhost:8080`.

### Step 6: Stop Spark Services

When you're done, you can stop Spark services.

1. **Stop the Spark Worker**:

   ```bash
   $SPARK_HOME/sbin/stop-worker.sh
   ```

2. **Stop the Spark Master**:

   ```bash
   $SPARK_HOME/sbin/stop-master.sh
   ```

---

### Notes:

- Ensure you have sufficient resources (CPU, memory) allocated to your Docker container to run both Hadoop and Spark.
- If you're using HDFS in Hadoop, make sure your Spark configuration (`spark-defaults.conf`) has the necessary settings to access HDFS, such as `spark.hadoop.fs.defaultFS`.
- You can mount local directories containing Hadoop configurations and Spark jobs using Docker volumes.

This method installs Spark directly within the same container as Hadoop, eliminating the need for multiple containers but still allowing full Hadoop-Spark integration. Let me know if you need more help!

This concludes the installation and configuration process for **Ubuntu** and **JAVA** and then Configure **Hadoop, MySQL, HIVE, Sqoop, Flume, Spark** on Docker Container.
