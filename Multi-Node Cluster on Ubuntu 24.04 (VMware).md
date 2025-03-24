# **Complete Guide: Install Hadoop Multi-Node Cluster on Ubuntu 24.04 (VMware)**
This guide covers installing and configuring **Hadoop 3.3.6** on **two Ubuntu 24.04 virtual machines** inside **VMware Workstation**.

## **Prerequisites**
1. **Two Ubuntu 24.04 VMs** running in **VMware Workstation**.
2. **At least 4GB RAM & 50GB disk space per VM**.
3. **Static IPs for both VMs**.
4. **Java 8 or later installed**.

---

# **Step 1: Configure Static IPs for Both VMs**
### **1. Check Network Interface Name**
On **both VMs**, open Terminal and run:
```bash
ip a
```
Find your network interface (e.g., `ens33` or `eth0`).

### **2. Edit Netplan Configuration**
Run:
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
For the **Master Node** (VM 1):
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
For the **Worker Node** (VM 2):
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.101/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
### **3. Apply Changes**
```bash
sudo netplan apply
ip a  # Verify new IP
```

---

# **Step 2: Install Java on Both VMs**
Hadoop requires Java. Install **OpenJDK 11**:
```bash
sudo apt update && sudo apt install openjdk-11-jdk -y
```
Verify installation:
```bash
java -version
```
Expected output:
```
openjdk version "11.0.20" 2024-XX-XX
```

---

# **Step 3: Create Hadoop User on Both VMs**
```bash
sudo adduser hadoop
sudo usermod -aG sudo hadoop
su - hadoop
```

---

# **Step 4: Configure SSH Access**
1. **Install SSH on Both VMs**:
   ```bash
   sudo apt install ssh -y
   ```
2. **Generate SSH Keys on Master Node**:
   ```bash
   ssh-keygen -t rsa -P ""
   ```
3. **Copy SSH Key to Worker Node**:
   ```bash
   ssh-copy-id hadoop@192.168.1.101
   ```
4. **Test SSH Connection from Master to Worker**:
   ```bash
   ssh hadoop@192.168.1.101
   ```
   It should log in without asking for a password.

---

# **Step 5: Download and Install Hadoop**
Perform the following steps **on both VMs**.

### **1. Download Hadoop**
```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
tar -xvzf hadoop-3.3.6.tar.gz
sudo mv hadoop-3.3.6 /usr/local/hadoop
```

### **2. Set Environment Variables**
Edit `~/.bashrc`:
```bash
nano ~/.bashrc
```
Add:
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
Save and apply:
```bash
source ~/.bashrc
```

---

# **Step 6: Configure Hadoop**
## **1. Configure `hadoop-env.sh`**
Edit:
```bash
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Set Java path:
```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

---

## **2. Configure Core Site (`core-site.xml`)**
Edit:
```bash
nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
Replace with:
```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://master:9000</value>
    </property>
</configuration>
```

---

## **3. Configure HDFS (`hdfs-site.xml`)**
Edit:
```bash
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
Add:
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
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
sudo chown -R hadoop:hadoop /usr/local/hadoop/hdfs
```

---

## **4. Configure MapReduce (`mapred-site.xml`)**
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

---

## **5. Configure YARN (`yarn-site.xml`)**
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

---

# **Step 7: Set Up Master and Worker Nodes**
## **1. Edit Hosts File on Both VMs**
```bash
sudo nano /etc/hosts
```
Add:
```
192.168.1.100  master
192.168.1.101  worker1
```

## **2. Define Workers on Master Node**
On the **Master Node**, edit:
```bash
nano $HADOOP_HOME/etc/hadoop/workers
```
Add:
```
worker1
```

---

# **Step 8: Start Hadoop Cluster**
## **1. Format Namenode (Master Only)**
```bash
hdfs namenode -format
```

## **2. Start Hadoop Services (Master Only)**
```bash
start-dfs.sh
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

# **Step 9: Verify Hadoop Cluster**
## **Check Web UI**
1. **HDFS Web UI**:  
   📌 **http://master:9870/**
2. **YARN Resource Manager**:  
   📌 **http://master:8088/**

---

# **Step 10: Stop Hadoop**
To stop services:
```bash
stop-dfs.sh
stop-yarn.sh
```

---

# **Conclusion**
You have successfully set up a **Hadoop multi-node cluster** on **two Ubuntu 24.04 VMs** inside **VMware Workstation**!
