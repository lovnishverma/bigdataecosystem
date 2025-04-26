# **Complete Steps to Install Apache Hive on Ubuntu**

Apache Hive is a data warehouse infrastructure built on top of Hadoop. This guide will show how to install and configure Hive on **Ubuntu**.

---

## **Step 1: Install Prerequisites**
Before installing Hive, ensure your system has the necessary dependencies.

### **1.1 Install Java**
Hive requires Java to run. Install it if it's not already installed:
```bash
sudo apt update
sudo apt install default-jdk -y
java -version  # Verify installation
```

### **1.2 Install Hadoop (Required for Hive)**
Hive requires Hadoop to function properly. If Hadoop is not installed, install it using:

```bash
sudo apt install hadoop -y
hadoop version  # Verify installation
```
If you need a full Hadoop setup, follow a [Hadoop installation guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html).

### **1.3 Install wget (If not installed)**
```bash
sudo apt install wget -y
```

---

## **Step 2: Download and Install Apache Hive**
### **2.1 Download Hive**
```bash
wget https://apache.root.lu/hive/hive-2.3.9/apache-hive-2.3.9-bin.tar.gz
```
*Check the latest version from the official Hive website:* [Apache Hive Downloads](https://hive.apache.org/downloads.html)

### **2.2 Extract Hive and Move to /opt Directory**
```bash
sudo tar -xzf apache-hive-2.3.9-bin.tar.gz -C /opt
sudo mv /opt/apache-hive-2.3.9-bin /opt/hive
```

---

## **Step 3: Set Up Environment Variables**
To run Hive commands globally, configure environment variables.

### **3.1 Open the `.bashrc` File**
```bash
nano ~/.bashrc
```

### **3.2 Add the Following Lines at the End**
```bash
export HIVE_HOME=/opt/hive
export PATH=$HIVE_HOME/bin:$PATH
```

### **3.3 Apply the Changes**
```bash
source ~/.bashrc
```

### **3.4 Verify Hive Installation**
```bash
hive --version
```
If Hive is installed correctly, it will print the version.

---

## **Step 4: Configure Hive**
### **4.1 Create Hive Directories in HDFS**
```bash
hdfs dfs -mkdir -p /user/hive/warehouse
hdfs dfs -chmod -R 770 /user/hive/warehouse
hdfs dfs -chown -R $USER:$USER /user/hive/warehouse
```

### **4.2 Configure `hive-site.xml`**
Edit the Hive configuration file:
```bash
sudo nano /opt/hive/conf/hive-site.xml
```

Add the following configurations:

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

Save and exit (`CTRL + X`, then `Y` and `ENTER`).

---

## **Step 5: Set Proper Permissions**
```bash
sudo chown -R $USER:$USER /opt/hive
sudo chmod -R 755 /opt/hive
```

---

## **Step 6: Initialize Hive Metastore**
Hive uses a database (Derby by default) to store metadata.

### **6.1 Run Schema Initialization**
```bash
/opt/hive/bin/schematool -initSchema -dbType derby
```

---

## **Step 7: Start Hive**
After setup, you can now start Hive.

### **7.1 Run Hive Shell**
```bash
hive
```

### **7.2 Verify Hive is Working**
Run the following command inside the Hive shell:
```sql
SHOW DATABASES;
```
It should list default databases.

---

## **(Optional) Configure Hive with MySQL (For Production Use)**
Using **MySQL** instead of Derby is recommended for better performance.

### **1. Install MySQL Server**
```bash
sudo apt install mysql-server -y
sudo systemctl start mysql
sudo systemctl enable mysql
```

### **2. Create a Hive Metastore Database**
```bash
mysql -u root -p
```
Inside the MySQL shell, run:
```sql
CREATE DATABASE metastore;
CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY 'hivepassword';
GRANT ALL PRIVILEGES ON metastore.* TO 'hiveuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### **3. Configure Hive to Use MySQL**
Edit `hive-site.xml`:
```bash
nano /opt/hive/conf/hive-site.xml
```
Replace the Derby configuration with:
```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost/metastore?createDatabaseIfNotExist=true</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
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

### **4. Download MySQL JDBC Driver**
```bash
wget https://downloads.mysql.com/archives/get/p/3/file/mysql-connector-java-8.0.28.tar.gz
tar -xzf mysql-connector-java-8.0.28.tar.gz
sudo mv mysql-connector-java-8.0.28/mysql-connector-java-8.0.28.jar /opt/hive/lib/
```

### **5. Reinitialize Hive Metastore**
```bash
/opt/hive/bin/schematool -initSchema -dbType mysql
```

---

## **Hive is Now Ready to Use! 🚀**
With this setup, Hive is installed and ready for queries.
