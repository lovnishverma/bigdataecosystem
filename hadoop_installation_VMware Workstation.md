### **Step-by-Step Guide to Installing Hadoop on Ubuntu (VM)**
This guide will walk you through installing **Hadoop 3.3.6** (latest stable version) on **Ubuntu 24.04** running in a VMware virtual machine.

---

## **Prerequisites**
1. **Ubuntu VM** running in **VMware Workstation**.
2. At least **4 GB RAM** and **50 GB disk space**.
3. **Java 8 or later** installed.

---

## **Step 1: Update Ubuntu Packages**
Before proceeding, update and upgrade system packages:
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **Step 2: Install Java**
Hadoop requires Java to run. Install OpenJDK 11 (recommended for stability):
```bash
sudo apt install openjdk-11-jdk -y
```
Verify Java installation:
```bash
java -version
```
You should see output similar to:
```
openjdk version "11.0.20" 2024-XX-XX
```

---

## **Step 3: Create a Hadoop User**
For security reasons, avoid running Hadoop as `root`. Instead, create a new user:
```bash
sudo adduser hadoop
```
Grant sudo privileges:
```bash
sudo usermod -aG sudo hadoop
```
Switch to the new user:
```bash
su - hadoop
```

---

## **Step 4: Download Hadoop**
Visit the Apache Hadoop official site:  
🔗 [https://hadoop.apache.org/releases.html](https://hadoop.apache.org/releases.html)

Download Hadoop 3.3.6:
```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```
Extract the file:
```bash
tar -xvzf hadoop-3.3.6.tar.gz
```
Move Hadoop to `/usr/local/`:
```bash
sudo mv hadoop-3.3.6 /usr/local/hadoop
```

---

## **Step 5: Configure Hadoop Environment Variables**
Edit the `~/.bashrc` file:
```bash
nano ~/.bashrc
```
Add the following lines at the end:
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
Save and exit (CTRL+X → Y → ENTER).

Apply changes:
```bash
source ~/.bashrc
```

---

## **Step 6: Configure Hadoop**
### **1. Configure `hadoop-env.sh`**
Edit the Hadoop environment configuration file:
```bash
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Find the line:
```bash
export JAVA_HOME=
```
Set Java path:
```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```
Save and exit.

---

### **2. Configure Core Site (`core-site.xml`)**
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
Save and exit.

---

### **3. Configure HDFS (`hdfs-site.xml`)**
Edit:
```bash
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
Add the following:
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
Save and exit.

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

### **4. Configure MapReduce (`mapred-site.xml`)**
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
Save and exit.

---

### **5. Configure YARN (`yarn-site.xml`)**
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
Save and exit.

---

## **Step 7: Format the Namenode**
Run:
```bash
hdfs namenode -format
```
You should see **"Storage directory successfully formatted"**.

---

## **Step 8: Start Hadoop Services**
Start HDFS:
```bash
start-dfs.sh
```
Start YARN:
```bash
start-yarn.sh
```

Check running services:
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

## **Step 9: Verify Hadoop Installation**
1. Open a browser.
2. Go to the **HDFS web UI**:  
   📌 **http://localhost:9870/**
3. Go to the **YARN web UI**:  
   📌 **http://localhost:8088/**

---

## **Step 10: Configure SSH for Hadoop (Optional for Multi-Node Cluster)**
Enable SSH:
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
It should log in without a password.

---

## **Step 11: Stop Hadoop**
To stop services:
```bash
stop-dfs.sh
stop-yarn.sh
```

---

## **Conclusion**
You have successfully installed **Hadoop 3.3.6** on **Ubuntu 24.04** running in a VMware virtual machine. 🚀 Now, you can start working with HDFS, YARN, and MapReduce!

Would you like a guide on **running MapReduce jobs** or setting up a **multi-node Hadoop cluster**?
