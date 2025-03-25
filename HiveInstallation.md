### **Beginner-Friendly Guide to Installing Apache Hive 4.0.1 on Ubuntu 24.04 (VMware)**
This guide will walk you through installing **Apache Hive 4.0.1** on **Hadoop 3.3.6** in a **single-node setup (pseudo-distributed mode)**.

---

## **🔹 Prerequisites**
✔ You have **Ubuntu 24.04** running in **VMware Workstation**  
✔ **Hadoop 3.3.6** is already installed and configured (**HDFS, YARN, MapReduce**)  
✔ **Java 8 or later** is installed  
✔ PostgreSQL or MySQL is installed for the Hive Metastore (PostgreSQL recommended)

---

## **1️⃣ Download and Install Apache Hive 4.0.1**
### **Step 1: Download Hive**
Go to the official Hive release page:  
🔗 [https://hive.apache.org/downloads.html](https://hive.apache.org/downloads.html)  

Download the latest stable version (Hive 4.0.1):  
```bash
wget https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz
```

### **Step 2: Extract and Move Hive to `/usr/local/`**
```bash
tar -xvzf apache-hive-4.0.1-bin.tar.gz
sudo mv apache-hive-4.0.1-bin /usr/local/hive
```

### **Step 3: Set Permissions**
```bash
sudo chown -R hadoop:hadoop /usr/local/hive
```

---

## **2️⃣ Configure Hive Environment Variables**
Edit `~/.bashrc` to add Hive to the system path:  
```bash
nano ~/.bashrc
```
Add the following lines at the end:  
```bash
# Hive Environment Variables
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin
export HADOOP_HOME=/usr/local/hadoop
export HIVE_CONF_DIR=$HIVE_HOME/conf
```
Save & exit (CTRL+X → Y → ENTER).  

Apply changes:  
```bash
source ~/.bashrc
```

Verify:  
```bash
hive --version
```
Expected output:  
```
Hive 4.0.1
```

---

## **3️⃣ Configure Hive Metastore (PostgreSQL)**
Hive requires a database for the **metastore**. We will use **PostgreSQL**.

### **Step 1: Install PostgreSQL**
```bash
sudo apt install postgresql postgresql-contrib -y
```

### **Step 2: Create a Hive Metastore Database**
```bash
sudo -i -u postgres
psql
```
Inside PostgreSQL, run:
```sql
CREATE DATABASE metastore;
CREATE USER hiveuser WITH PASSWORD 'hivepassword';
ALTER ROLE hiveuser SET client_encoding TO 'utf8';
ALTER ROLE hiveuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE hiveuser SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE metastore TO hiveuser;
\q
exit
```

### **Step 3: Configure Hive to Use PostgreSQL**
Edit `hive-site.xml`:  
```bash
nano $HIVE_HOME/conf/hive-site.xml
```
Add the following configuration:
```xml
<configuration>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:postgresql://localhost:5432/metastore</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>org.postgresql.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hiveuser</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>hivepassword</value>
    </property>
</configuration>
```
Save & exit.

---

## **4️⃣ Initialize Hive Metastore**
### **Step 1: Download PostgreSQL JDBC Driver**
```bash
wget https://jdbc.postgresql.org/download/postgresql-42.5.4.jar -P $HIVE_HOME/lib/
```

### **Step 2: Initialize the Metastore**
```bash
schematool -dbType postgres -initSchema
```
Expected output:  
✅ **"Schema initialization completed successfully"**

---

## **5️⃣ Start Hive Services**
### **Step 1: Start the Hive Metastore**
```bash
hive --service metastore &
```
It should start without errors.

### **Step 2: Start HiveServer2**
```bash
hive --service hiveserver2 &
```
It should start and listen on port **10000**.

---

## **6️⃣ Verify Hive Installation**
### **Step 1: Open Hive CLI**
```bash
hive
```
Expected output:  
```
hive>
```
### **Step 2: Run a Sample Query**
```sql
SHOW DATABASES;
```
Expected output:  
```
default
```
---

## **7️⃣ Optional: Install Beeline (Hive JDBC Client)**
For an advanced SQL client:
```bash
beeline -u jdbc:hive2://localhost:10000
```
Run:
```sql
SHOW TABLES;
```

---

## **onclusion**
🎉 You have successfully installed **Apache Hive 4.0.1** on **Ubuntu 24.04 with PostgreSQL Metastore**! You can now run Hive queries on Hadoop.

Would you like a guide for **Hive with MySQL Metastore** instead? 🚀
