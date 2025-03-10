### ✅ **What is Apache Flume in Big Data? 🚀**

---

### 💡 **Definition of Apache Flume:**
👉 **Apache Flume** is a **data ingestion tool** used to **collect, aggregate, and transfer large volumes of streaming data** (such as **log files, social media data, server logs, IoT data, etc.**) **into Hadoop (HDFS/Hive).**

---

## ✅ **Why Do We Need Apache Flume? 🤔**
### 📊 **Problem:**
Suppose you have:
- ✅ **Millions of log files** generated every second from **Web Servers, IoT devices, Sensors, etc.**
- ✅ Or you have **Streaming Data from Twitter, Facebook, YouTube, etc.**
- ✅ Or you have **Server Logs** from your website.

👉 You want to **send this streaming data** into:
- ✅ **HDFS (Hadoop File System)** for storage.
- ✅ **Hive** for querying and analysis.
- ✅ **HBase** for real-time access.

👉 **How will you transfer this large streaming data continuously?** 🤔

---

## ✅ **Solution: Use Apache Flume 💯**
👉 Apache Flume will **continuously capture streaming data** from:
- ✅ **Web Servers (logs)**  
- ✅ **IoT Devices (sensor data)**  
- ✅ **Social Media (Twitter, Facebook)**  
- ✅ **Application Logs (Tomcat, Apache)**  

👉 And automatically **push it into Hadoop (HDFS/Hive)** without manual work.

---

## ✅ **Where is Flume Used in Real Life? 💡**
| Industry                  | Flume is Used For                                                             |
|--------------------------|---------------------------------------------------------------------------------|
| 📊 **E-commerce (Amazon, Flipkart)** | Capturing **user behavior logs**, product clicks, browsing history, etc.  |
| 💻 **IT Companies (Google, Facebook)** | Collecting **application logs**, crash logs, web traffic logs, etc.      |
| 📡 **IoT Devices (Smart Homes)**     | Streaming data from **IoT devices, sensors, CCTV, etc.**               |
| 📜 **News Websites**             | **Capturing real-time news**, logs, and content from different sources.    |
| 🛰️ **Social Media Platforms**   | Capturing **tweets, Facebook posts, YouTube comments, etc.**              |

---

## ✅ **How Does Apache Flume Work? 🚀**
👉 **Apache Flume works on a Pipeline Architecture.**

### ✔ **Pipeline = Source → Channel → Sink → Hadoop (HDFS)**
| Component    | What it Does                                                             |
|--------------|-------------------------------------------------------------------------|
| ✅ **Source**  | Collects **data from source (logs, Twitter, IoT, etc.)**                |
| ✅ **Channel** | Temporarily stores the data (like a queue or buffer).                  |
| ✅ **Sink**    | Sends data to **HDFS, Hive, or HBase**.                                 |
| ✅ **Hadoop**  | Stores the data permanently for analysis.                              |

---

## ✅ **Architecture of Apache Flume 🔥**
Here’s how Flume works step-by-step:

```
                 ┌─────────────────┐
 Data Source --> │     Source      │ --> Captures Data (Logs, Twitter, IoT)
                 └─────────────────┘
                           │
                           ▼
                 ┌─────────────────┐
 Data Buffer --> │     Channel     │ --> Holds data temporarily (like a Queue)
                 └─────────────────┘
                           │
                           ▼
                 ┌─────────────────┐
 Data Storage -->│      Sink       │ --> Sends Data to HDFS, Hive, or HBase
                 └─────────────────┘
                           │
                           ▼
                ┌───────────────────────┐
 Data in Hadoop│    HDFS / Hive / HBase   │
                └───────────────────────┘
```

---

## ✅ **Example of Apache Flume Use Cases 🚀**
Here are some real-world use cases:

---

### ✔ **1. Capturing Web Server Logs (Access Logs, Error Logs)**
Suppose you have a website with **1 Billion hits/day** like **Flipkart, Amazon, etc.**.

👉 Every hit generates a log file:  
```
2025-03-10 12:34:55 INFO User Clicked on Product ID: 2345
2025-03-10 12:35:00 INFO User Added Product ID: 2345 to Cart
```

👉 Flume will:
- ✅ **Capture these logs**.
- ✅ **Stream them to Hadoop (HDFS)** in real-time.
- ✅ You can **analyze it later in Hive**.

### **Flume Configuration Example:**
```properties
# Flume Agent Configuration
agent1.sources = source1
agent1.channels = channel1
agent1.sinks = sink1

# Source Configuration (Log File)
agent1.sources.source1.type = exec
agent1.sources.source1.command = tail -f /var/log/httpd/access.log

# Channel Configuration
agent1.channels.channel1.type = memory

# Sink Configuration (HDFS)
agent1.sinks.sink1.type = hdfs
agent1.sinks.sink1.hdfs.path = hdfs://localhost:9000/user/logs
```

✅ Flume will **capture log files in real-time** and push them to **HDFS**.

---

### ✔ **2. Capturing Twitter Data (Trending Hashtags)**
Suppose you want to capture **live tweets** on a trending hashtag like:
```
#election2025
#iphone16
#IndiaWins
```

👉 **Flume can capture these tweets** and push them to **HDFS/Hive** for analysis.

### ✅ Flume Twitter Configuration Example:
```properties
# Source Configuration
agent1.sources.source1.type = org.apache.flume.source.twitter.TwitterSource
agent1.sources.source1.consumerKey = YOUR_CONSUMER_KEY
agent1.sources.source1.consumerSecret = YOUR_CONSUMER_SECRET
agent1.sources.source1.accessToken = YOUR_ACCESS_TOKEN
agent1.sources.source1.accessTokenSecret = YOUR_ACCESS_TOKEN_SECRET

# Sink Configuration (HDFS)
agent1.sinks.sink1.type = hdfs
agent1.sinks.sink1.hdfs.path = hdfs://localhost:9000/user/twitter
```

👉 ✅ **Flume will capture live tweets** and push them to **HDFS**.

---

### ✔ **3. IoT Sensor Data (Smart Homes, CCTV, Temperature Sensors)**
Suppose you have:
- ✅ **IoT Sensors (Temperature, Humidity, CCTV)**.
- ✅ You want to capture the data in real-time.

👉 Flume will:
- ✅ Continuously read sensor data.
- ✅ Push it to HDFS in real-time.
- ✅ You can then analyze it.

---

## ✅ **Types of Flume Channels 🚀**
| Channel Type     | Use Case                                                      |
|-----------------|-----------------------------------------------------------------|
| ✅ **Memory Channel** | Fastest but not durable (if Flume crashes, data is lost).    |
| ✅ **File Channel**   | Slower but data is saved even if Flume crashes.              |
| ✅ **Kafka Channel**  | Highly scalable and fault-tolerant (best for production).    |

---

## ✅ **Why Is Flume Better Than Manual Data Transfer? 🚀**
| Feature                   | Manual File Transfer    | Apache Flume                         |
|--------------------------|------------------------|----------------------------------------|
| **Data Transfer Speed**   | Very Slow              | Lightning Fast 🚀                    |
| **Streaming Data**        | Impossible             | Handles Real-time Streaming 🚀        |
| **Data Loss**             | High                   | Zero Data Loss (Fault-tolerant)       |
| **Automation**            | Manual Effort          | Fully Automated                       |
| **Big Data Compatibility**| Not Possible           | Integrates with Hadoop, Hive, HBase    |

---

## ✅ **Where Does Apache Flume Send Data? 🚀**
| Data Source               | Flume Can Send Data To                              |
|--------------------------|-----------------------------------------------------|
| ✅ **Log Files**        | **HDFS / Hive / HBase / Kafka**                      |
| ✅ **Social Media**     | **Hive / Spark / ElasticSearch**                     |
| ✅ **IoT Devices**     | **Hadoop / MongoDB / Kafka**                         |
| ✅ **Web Server Logs** | **HDFS / Hive / Kafka**                              |

---

## ✅ **Why Is Flume So Powerful? 💯**
👉 Flume can:
- ✅ **Ingest Terabytes of Data/Hour.**  
- ✅ Handle **Millions of Streaming Logs/Second**.  
- ✅ Push data to **Hadoop, Hive, HBase, Kafka, etc.**  
- ✅ Fully Automated.  
- ✅ Real-time Data Processing.  

---

## ✅ **🔥 Final Answer**
👉 **Apache Flume** is used for:
- ✅ **Real-time streaming data capture.**  
- ✅ **Log file ingestion from web servers.**  
- ✅ **Capturing social media data (Twitter, YouTube, etc.).**  
- ✅ **Moving IoT data (sensors, CCTV) to Hadoop.**

---


### **Here is a complete step-by-step guide to install Apache Flume on top of your Hadoop setup and demonstrate a working example:**

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

   Send multiple lines of data:
   ```bash
   for i in {1..5}; do echo "This is message $i" | nc localhost 44444; done
   ```
![image](https://github.com/user-attachments/assets/e2cc2a42-7f26-4b6b-81cc-5102a1f39a7f)

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
