Here’s a **fully optimized and foolproof guide** to installing **Hadoop 3.3.6** on **Ubuntu 24.04** running in a VMware virtual machine.** This guide includes additional tips, verification steps, troubleshooting, and SSH configuration for a single-node setup (pseudo-distributed mode).  

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
