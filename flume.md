Here is a complete step-by-step guide to install Apache Flume on top of your Hadoop setup and demonstrate a working example:

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
   Send multiple lines of data:
   ```bash
   for i in {1..5}; do echo "This is message $i" | nc localhost 44444; done
   ```

2. **Verify Data in HDFS**  
   Check the HDFS directory where Flume is writing data:
   ```bash
   hadoop fs -ls /user/flume/demo
   ```
   View the ingested data files:
   ```bash
   hadoop fs -cat /user/flume/demo/*
   ```

   You should see the messages sent via `Netcat`.

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
