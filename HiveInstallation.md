### Install Apache Hive on Top of Hadoop (Ubuntu 24.04)  

To install Apache Hive on top of Hadoop, follow these steps:

---

## **Prerequisites**  
- A working Hadoop installation (HDFS & YARN).  
- Java installed (`openjdk-11-jdk`).  
- SSH configured for Hadoop.  

If Hadoop is not installed, refer to the steps **[here](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)**.

---

## **Step 1: Download and Extract Apache Hive**  
Download Hive from the official Apache mirror:

```bash
wget https://dlcdn.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
```

Extract the archive:

```bash
tar -xvzf apache-hive-3.1.3-bin.tar.gz
sudo mv apache-hive-3.1.3-bin /usr/local/hive
```

Set Hive environment variables by adding the following lines to `~/.bashrc`:

```bash
export HIVE_HOME=/usr/local/hive
export PATH=$HIVE_HOME/bin:$PATH
export HADOOP_HOME=/usr/local/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
export HIVE_CONF_DIR=$HIVE_HOME/conf
```

Apply the changes:

```bash
source ~/.bashrc
```

---

## **Step 2: Configure Hive**  

### **2.1 Create Hive Directories in HDFS**
Hive requires a directory structure in HDFS. Run:

```bash
hdfs dfs -mkdir -p /user/hive/warehouse
hdfs dfs -chmod 777 /user/hive/warehouse
hdfs dfs -mkdir /tmp
hdfs dfs -chmod 777 /tmp
```

---

### **2.2 Configure `hive-site.xml`**
Create a configuration file:

```bash
cp $HIVE_HOME/conf/hive-default.xml.template $HIVE_HOME/conf/hive-site.xml
nano $HIVE_HOME/conf/hive-site.xml
```

Modify the following properties inside `<configuration>`:

```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
</property>

<property>
    <name>hive.metastore.warehouse.dir</name>
    <value>/user/hive/warehouse</value>
</property>

<property>
    <name>hive.exec.scratchdir</name>
    <value>/tmp/hive</value>
</property>
```

Save and exit.

---

## **Step 3: Initialize Hive Metastore**  

Run the following command:

```bash
schematool -initSchema -dbType derby
```

---

## **Step 4: Start Hive**
Run Hive CLI:

```bash
hive
```

If successful, you should see the `hive>` prompt.

---

## **(Optional) Step 5: Use MySQL as Metastore (Instead of Derby)**  

If you want a production-ready setup, replace the embedded Derby database with MySQL.

1. Install MySQL:
   ```bash
   sudo apt install mysql-server -y
   ```
   
2. Secure MySQL:
   ```bash
   sudo mysql_secure_installation
   ```

3. Create a Hive Metastore Database:
   ```bash
   mysql -u root -p
   ```

   Inside MySQL shell:

   ```sql
   CREATE DATABASE metastore;
   CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY 'hivepassword';
   GRANT ALL PRIVILEGES ON metastore.* TO 'hiveuser'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

4. Download the MySQL JDBC Connector:

   ```bash
   wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.33.tar.gz
   tar -xvzf mysql-connector-java-8.0.33.tar.gz
   sudo cp mysql-connector-java-8.0.33/mysql-connector-java-8.0.33.jar $HIVE_HOME/lib/
   ```

5. Update `hive-site.xml`:

   ```xml
   <property>
       <name>javax.jdo.option.ConnectionURL</name>
       <value>jdbc:mysql://localhost:3306/metastore?createDatabaseIfNotExist=true</value>
   </property>

   <property>
       <name>javax.jdo.option.ConnectionDriverName</name>
       <value>com.mysql.cj.jdbc.Driver</value>
   </property>

   <property>
       <name>javax.jdo.option.ConnectionUserName</name>
       <value>hiveuser</value>
   </property>

   <property>
       <name>javax.jdo.option.ConnectionPassword</name>
       <value>hivepassword</value>
   </property>
   ```

6. Initialize the MySQL-backed Metastore:

   ```bash
   schematool -initSchema -dbType mysql
   ```

7. Start Hive with MySQL-backed metastore:

   ```bash
   hive
   ```

---

## **Step 6: Verify Hive Setup**
Run a sample query:

```sql
CREATE TABLE test (id INT, name STRING);
SHOW TABLES;
```

If everything works, you have successfully installed Hive on Hadoop!
