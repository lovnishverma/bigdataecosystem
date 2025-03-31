Guide for installing **Hadoop 3.3.6 on Ubuntu 24.04** in a **VMware virtual machine**. This guide includes **troubleshooting tips, verification steps, and SSH configuration** to ensure a **properly working** single-node (pseudo-distributed) Hadoop setup.  

---

# **🚀 Complete Guide to Installing Hadoop 3.3.6 on Ubuntu 24.04 (VMware)**
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

## **4️⃣ Create a Hadoop User (Optional - Skip for now)**
Instead of using root or your personal user, create a dedicated **hadoop** user:  
```bash
sudo adduser hadoop
```
Add your user to the `sudo` group:  
```bash
sudo usermod -aG sudo hadoop
```
Switch to the `hadoop` user:  
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
sudo chown -R $USER:$USER /usr/local/hadoop
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
Replace it with: (Replace `11` with `8` if you installed JDK 8)  
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
sudo chown -R $USER:$USER /usr/local/hadoop/hdfs
```

---

### **4️⃣ Configure `mapred-site.xml`**  
```bash
cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
Add:  
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
</configuration>
```
Save & exit.

---

### **5️⃣ Configure `yarn-site.xml`**  
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

## **8️⃣ Configure Passwordless SSH**
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

---

## **9️⃣ Format the Namenode & Start Hadoop**  
Format the Namenode:  
```bash
hdfs namenode -format
```
Start HDFS:  
```bash
start-dfs.sh
```
Start YARN:  
```bash
start-yarn.sh
```
Verify:  
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

## **✅ Verify Hadoop Installation**  
📌 Open a browser and go to:  
✔ HDFS Web UI → **http://localhost:9870/**  
✔ YARN Web UI → **http://localhost:8088/**  

---

🎉 **Congratulations!** You have successfully installed **Hadoop 3.3.6** on **Ubuntu 24.04 (VMware)**! 😊
