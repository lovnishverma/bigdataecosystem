### **Step-by-Step Guide to Install Apache Hive 4.0.1 on Top of Hadoop**
This guide will help you install **Apache Hive 4.0.1** on top of **Hadoop** in an easy-to-follow manner.

---

## **Prerequisites**  
Before installing Hive, make sure you have the following:  

✅ **Hadoop Installed & Running**  
   - If you don’t have Hadoop installed, follow this guide: [Install Hadoop on Ubuntu](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html).  
   - Ensure that HDFS (Hadoop Distributed File System) is running.  

✅ **Java Installed**  
   - Run the command below to check if Java is installed:  
     ```bash
     java -version
     ```  
   - If Java is **not installed**, install it using:  
     ```bash
     sudo apt update
     sudo apt install openjdk-11-jdk -y
     ```  

---

## **Step 1: Download and Extract Apache Hive 4.0.1**  

1️⃣ **Download Hive 4.0.1**:  
   ```bash
   wget https://downloads.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz
   ```  

2️⃣ **Extract the downloaded file**:  
   ```bash
   tar -xvzf apache-hive-4.0.1-bin.tar.gz
   ```  

3️⃣ **Move the extracted folder to `/usr/local/hive`**:  
   ```bash
   sudo mv apache-hive-4.0.1-bin /usr/local/hive
   ```  

---

## **Step 2: Set Up Environment Variables**  

1️⃣ Open the **bash configuration file**:  
   ```bash
   nano ~/.bashrc
   ```  

2️⃣ Add the following lines **at the end of the file**:  
   ```bash
   export HIVE_HOME=/usr/local/hive
   export PATH=$HIVE_HOME/bin:$PATH
   export HADOOP_HOME=/usr/local/hadoop
   export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
   export HIVE_CONF_DIR=$HIVE_HOME/conf
   ```  

3️⃣ **Save the file** (Press `CTRL + X`, then `Y`, then `ENTER`).  

4️⃣ Apply the changes:  
   ```bash
   source ~/.bashrc
   ```  

---

## **Step 3: Configure Hive**  

### **3.1 Create Hive Directories in HDFS**  
Hive stores data inside Hadoop’s **HDFS**, so we need to create some directories:  

```bash
hdfs dfs -mkdir -p /user/hive/warehouse
hdfs dfs -chmod 777 /user/hive/warehouse
hdfs dfs -mkdir /tmp
hdfs dfs -chmod 777 /tmp
```  

---

### **3.2 Configure Hive Settings**  

1️⃣ Copy the default configuration file:  
   ```bash
   cp $HIVE_HOME/conf/hive-default.xml.template $HIVE_HOME/conf/hive-site.xml
   ```  

2️⃣ Open the Hive configuration file:  
   ```bash
   nano $HIVE_HOME/conf/hive-site.xml
   ```  

3️⃣ Add the following inside `<configuration>`:  
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

4️⃣ **Save the file** (Press `CTRL + X`, then `Y`, then `ENTER`).  

---

## **Step 4: Initialize Hive Metastore**  

1️⃣ Run the following command to initialize the Hive database:  
   ```bash
   schematool -initSchema -dbType derby
   ```  
   - **This sets up the internal Hive database using Derby.**  
   - If you see **"Schema initialization completed successfully"**, you are good to go!  

---

## **Step 5: Start Hive**  

1️⃣ Launch Hive by typing:  
   ```bash
   hive
   ```  
2️⃣ If successful, you should see the `hive>` prompt:  
   ```
   hive>
   ```  

**🎉 Congratulations! Hive 4.0.1 is now installed and running on top of Hadoop.**  

---

## **(Optional) Step 6: Use MySQL as Hive Metastore (For Production Use)**  
The default Derby database is good for testing but **not for production**. If you want to use **MySQL for Hive Metastore**, follow these steps:  

### **6.1 Install MySQL**  

```bash
sudo apt update
sudo apt install mysql-server -y
```  

### **6.2 Secure MySQL**  
```bash
sudo mysql_secure_installation
```  

### **6.3 Create Hive Metastore Database**  

1️⃣ Open MySQL:  
   ```bash
   sudo mysql -u root -p
   ```  

2️⃣ Inside the MySQL prompt, run:  

   ```sql
   CREATE DATABASE metastore;
   CREATE USER 'hiveuser'@'localhost' IDENTIFIED BY 'hivepassword';
   GRANT ALL PRIVILEGES ON metastore.* TO 'hiveuser'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```  

### **6.4 Download MySQL JDBC Connector**  

1️⃣ Download the **MySQL JDBC Driver**:  
   ```bash
   wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.33.tar.gz
   ```  

2️⃣ Extract and copy the `.jar` file:  
   ```bash
   tar -xvzf mysql-connector-java-8.0.33.tar.gz
   sudo cp mysql-connector-java-8.0.33/mysql-connector-java-8.0.33.jar $HIVE_HOME/lib/
   ```  

### **6.5 Configure Hive to Use MySQL**  

1️⃣ Open the **Hive configuration file**:  
   ```bash
   nano $HIVE_HOME/conf/hive-site.xml
   ```  

2️⃣ Add the following properties:  
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

3️⃣ **Save the file** (Press `CTRL + X`, then `Y`, then `ENTER`).  

4️⃣ Initialize the MySQL-backed metastore:  
   ```bash
   schematool -initSchema -dbType mysql
   ```  

5️⃣ **Start Hive with MySQL-backed Metastore**:  
   ```bash
   hive
   ```  

---

## **Step 7: Verify Hive Installation**  

Run a sample query to check if everything is working:  

```sql
CREATE TABLE test (id INT, name STRING);
SHOW TABLES;
```

If the table is created successfully, your Hive installation is **fully functional!** 🎉  

---

## **Conclusion**  
You have successfully installed **Apache Hive 4.0.1** on top of **Hadoop**. You also configured it with **MySQL Metastore** for a production-ready setup.  

Let me know if you need any help! 🚀
