Here’s a **Guide** to installing **Hadoop 3.3.6** on **Ubuntu 24.04** running in a VMware virtual machine.** This guide includes additional tips, verification steps, troubleshooting, and SSH configuration for a single-node setup (pseudo-distributed mode).  

---

# **Step-by-Step Guide to Installing Hadoop 3.3.6 on Ubuntu 24.04 (VMware)**  
This guide covers:  
✅ Installing **Hadoop 3.3.6** on **Ubuntu 24.04**  
✅ Configuring **HDFS, YARN, and MapReduce**  
✅ Setting up **passwordless SSH**  
✅ Ensuring **proper Java installation**  
✅ Troubleshooting common issues  

---

## **1️⃣ Prerequisites**  
Before starting, ensure:  
✔ You have **Ubuntu 24.04** running in **VMware Workstation**.  
✔ At least **4GB RAM**, **50GB disk space**, and **4 CPU cores** are allocated to the VM.  
✔ Java **8 or later** is installed.  

---

## **2️⃣ Update Ubuntu Packages**  
Update system packages to avoid dependency issues:  
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **3️⃣ Install Java (OpenJDK 11)**  
Hadoop requires Java. The recommended version is **OpenJDK 11**:  
```bash
sudo apt install openjdk-11-jdk -y
```
Verify installation:  
```bash
java -version
```
Expected output (may vary slightly):  
```
openjdk version "11.0.20" 2024-XX-XX
```
**Alternative:** If you need Java 8 for compatibility, install it using:  
```bash
sudo apt install openjdk-8-jdk -y
```

---

## **4️⃣ Create a Dedicated Hadoop User**  
Running Hadoop as `root` is insecure. Create a separate **hadoop** user:  
```bash
sudo adduser hadoop
```
Grant it **sudo privileges**:  
```bash
sudo usermod -aG sudo hadoop
```
Switch to the new user:  
```bash
su - hadoop
```

---

## **5️⃣ Download & Install Hadoop 3.3.6**  
Navigate to the **Apache Hadoop downloads page**:  
🔗 [https://hadoop.apache.org/releases.html](https://hadoop.apache.org/releases.html)  

Download Hadoop 3.3.6:  
```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```
Verify the file integrity (optional but recommended):  
```bash
sha512sum hadoop-3.3.6.tar.gz
```
Compare the hash with the one on the official website.  

Extract Hadoop:  
```bash
tar -xvzf hadoop-3.3.6.tar.gz
```
Move it to `/usr/local/`:  
```bash
sudo mv hadoop-3.3.6 /usr/local/hadoop
```
Set permissions:  
```bash
sudo chown -R hadoop:hadoop /usr/local/hadoop
```

---

## **6️⃣ Configure Hadoop Environment Variables**  
Edit the `~/.bashrc` file:  
```bash
nano ~/.bashrc
```
Add these lines at the end:  
```bash
# Hadoop Environment Variables
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
Save & exit (CTRL+X → Y → ENTER).  

Apply changes:  
```bash
source ~/.bashrc
```
Verify:  
```bash
echo $HADOOP_HOME
```
Expected output: `/usr/local/hadoop`

---

## **7️⃣ Configure Hadoop Core Files**  
### **1️⃣ Configure `hadoop-env.sh`**  
Edit Hadoop environment configuration:  
```bash
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Find the line:  
```bash
export JAVA_HOME=
```
Replace it with:  
```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```
Save & exit.

---

### **2️⃣ Configure `core-site.xml`**  
Edit:  
```bash
nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
Replace existing content with:  
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
Save & exit.

---

### **3️⃣ Configure `hdfs-site.xml`**  
Edit:  
```bash
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
Add:  
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.name.dir</name>
        <value>file:///usr/local/hadoop/hdfs/namenode</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>file:///usr/local/hadoop/hdfs/datanode</value>
    </property>
</configuration>
```
Create necessary directories:  
```bash
mkdir -p /usr/local/hadoop/hdfs/namenode
mkdir -p /usr/local/hadoop/hdfs/datanode
```
Set permissions:  
```bash
sudo chown -R hadoop:hadoop /usr/local/hadoop/hdfs
```

---

### **4️⃣ Configure `mapred-site.xml`**  
Copy template:  
```bash
cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
Edit:  
```bash
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
Add:  
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
Save & exit.

---

### **5️⃣ Configure `yarn-site.xml`**  
Edit:  
```bash
nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
Add:  
```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```
Save & exit.

---

## **8️⃣ Format the Namenode**  
```bash
hdfs namenode -format -force
```
Expected output:  
✅ **"Storage directory successfully formatted"**

---

## **9️⃣ Start Hadoop Services**  
Start HDFS:  
```bash
start-dfs.sh
```
Start YARN:  
```bash
start-yarn.sh
```
Verify running processes:  
```bash
jps
```
Expected output:  
```
NameNode
DataNode
ResourceManager
NodeManager
```

---

## **🔟 Verify Hadoop Installation**  
📌 Open a browser and go to:  
✔ HDFS Web UI → **http://localhost:9870/**  
✔ YARN Web UI → **http://localhost:8088/**  

---

## **1️⃣1️⃣ Configure Passwordless SSH (Optional)**  
```bash
sudo apt install ssh -y
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```
Test SSH:  
```bash
ssh localhost
```
It should log in without asking for a password.

---

## **1️⃣2️⃣ Stop Hadoop Services**  
```bash
stop-dfs.sh
stop-yarn.sh
```
Verify:  
```bash
jps
```
Output should be **empty**.

---

## **✅ Conclusion**  
🎉 You have successfully installed **Hadoop 3.3.6** on **Ubuntu 24.04 (VMware)**! 🚀 You can now run HDFS, YARN, and MapReduce.

For **multi-node cluster setup** follow [This guide](https://github.com/lovnishverma/bigdataecosystem/blob/main/Multi-Node%20Cluster%20on%20Ubuntu%2024.04%20(VMware).md)



# **Step-by-Step Guide to Installing Apache Hive on Ubuntu 24.04 (VMware)**
This guide will help you install **Apache Hive 4.0.0** on **Hadoop 3.3.6** in an **Ubuntu 24.04 VM** running in **VMware Workstation**.  

## **🔹 Prerequisites**
✅ You have **Hadoop 3.3.6** installed and running.  
✅ Java **8 or later** is installed.  
✅ At least **4GB RAM**, **50GB disk space**, and **4 CPU cores** allocated.  
✅ **MySQL or PostgreSQL** (optional) for Metastore.

---

## **1️⃣ Step 1: Update System Packages**
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **2️⃣ Step 2: Download & Install Apache Hive**
🔗 Visit the official **Apache Hive** download page:  
[https://hive.apache.org/downloads.html](https://hive.apache.org/downloads.html)  

Download Hive 4.0.0:  
```bash
wget https://downloads.apache.org/hive/hive-4.0.0/apache-hive-4.0.0-bin.tar.gz
```
Extract it:  
```bash
tar -xvzf apache-hive-4.0.0-bin.tar.gz
```
Move it to `/usr/local/`:  
```bash
sudo mv apache-hive-4.0.0-bin /usr/local/hive
```
Set permissions:  
```bash
sudo chown -R hadoop:hadoop /usr/local/hive
```

---

## **3️⃣ Step 3: Configure Hive Environment Variables**
Edit `~/.bashrc`:  
```bash
nano ~/.bashrc
```
Add these lines at the end:  
```bash
# Hive Environment Variables
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin
export HADOOP_HOME=/usr/local/hadoop
export HIVE_CONF_DIR=$HIVE_HOME/conf
export HADOOP_CLASSPATH+="$HIVE_HOME/lib/*"
```
Save and exit (CTRL+X → Y → ENTER).  

Apply changes:  
```bash
source ~/.bashrc
```
Verify:  
```bash
echo $HIVE_HOME
```
Expected output: `/usr/local/hive`

---

## **4️⃣ Step 4: Configure Hadoop for Hive**
### **1️⃣ Configure `hadoop-env.sh`**  
Edit Hadoop environment settings:  
```bash
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Add this line:  
```bash
export HADOOP_CLASSPATH+="$HIVE_HOME/lib/*"
```
Save and exit.

---

## **5️⃣ Step 5: Set Up Metastore Database (MySQL)**
Apache Hive requires a metastore. You can use **Derby (default), MySQL, or PostgreSQL**.  

### **Option 1: Use Embedded Derby (For Testing Only)**
Run:  
```bash
schematool -initSchema -dbType derby
```

### **Option 2: Use MySQL (Recommended for Production)**
#### **1️⃣ Install MySQL**
```bash
sudo apt install mysql-server -y
sudo systemctl start mysql
sudo systemctl enable mysql
```

#### **2️⃣ Create a Hive Database & User**
Log in to MySQL:  
```bash
sudo mysql -u root -p
```
Run these SQL commands:  
```sql
CREATE DATABASE metastore;
CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY 'hivepassword';
GRANT ALL PRIVILEGES ON metastore.* TO 'hiveuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

#### **3️⃣ Install MySQL JDBC Driver**
Download the MySQL Connector:  
```bash
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.33.tar.gz
```
Extract and move to Hive’s library:  
```bash
tar -xvzf mysql-connector-java-8.0.33.tar.gz
sudo mv mysql-connector-java-8.0.33/mysql-connector-java-8.0.33.jar /usr/local/hive/lib/
```

---

## **6️⃣ Step 6: Configure Hive Metastore**
Edit Hive configuration file:  
```bash
nano $HIVE_HOME/conf/hive-site.xml
```
Add this configuration for **MySQL Metastore**:
```xml
<configuration>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://localhost/metastore?createDatabaseIfNotExist=true</value>
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
</configuration>
```
Save and exit.

---

## **7️⃣ Step 7: Initialize the Hive Metastore**
Run:  
```bash
schematool -initSchema -dbType mysql
```
Expected output: ✅ **"Schema initialization complete"**

---

## **8️⃣ Step 8: Start Hive Services**
1️⃣ **Start Hadoop services**:  
```bash
start-dfs.sh
start-yarn.sh
```
2️⃣ **Start Hive Metastore**:  
```bash
hive --service metastore &
```
3️⃣ **Start HiveServer2** (for client connections):  
```bash
hiveserver2 &
```

---

## **9️⃣ Step 9: Verify Hive Installation**
Launch the Hive shell:  
```bash
hive
```
Run:  
```sql
SHOW DATABASES;
```
Expected output:  
```
default
metastore
```

---

## **🔟 Step 10: Run a Sample Hive Query**
### **Create a Table**
```sql
CREATE TABLE students (id INT, name STRING, age INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```
### **Load Sample Data**
```bash
nano students.csv
```
Add:  
```
1,John,22
2,Sarah,24
3,David,21
```
Save and exit.  

Load data into Hive:  
```sql
LOAD DATA LOCAL INPATH 'students.csv' INTO TABLE students;
```
### **Query Data**
```sql
SELECT * FROM students;
```
Expected output:  
```
1    John    22
2    Sarah   24
3    David   21
```

---

## **🔚 Conclusion**
🎉 You have successfully installed **Apache Hive 4.0.0** on **Hadoop 3.3.6** (Ubuntu 24.04, VMware)! 🚀  

---

## **💡 Troubleshooting**
### **1️⃣ Hive command not found?**
```bash
source ~/.bashrc
```

### **2️⃣ Metastore error (JDOException)?**
Check `hive-site.xml` for **correct database credentials**.

### **3️⃣ Table not found?**
```sql
SHOW TABLES;
```

