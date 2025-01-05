Here's a **comprehensive list of HDFS commands** with easy-to-understand instructions for quick reference:
![image](https://github.com/user-attachments/assets/1ea3ba32-1b68-4584-b521-3f5e6f5c6ffb)

---

## 📂 **HDFS Commands - Complete Reference**

---

### **1. Basic File Operations**

#### 📄 **Create a new file locally**
Create a file on your local system:
```bash
echo "This is a sample file" > localfile.txt
```

#### 📤 **Upload a local file to HDFS**
Upload a local file to HDFS:
```bash
hdfs dfs -put localfile.txt /user/hadoop/destination-path
```

#### ⬇️ **Download a file from HDFS to the local file system**
Use the `-get` command to copy files from HDFS to the local system:
```bash
hdfs dfs -get /path/to/hdfspath /localpath
```

#### 🖼️ **View the file content from HDFS**
View the contents of a file directly without copying it:
```bash
hdfs dfs -cat /path/to/file
```

#### ✍️ **Append content to an HDFS file**
Append local file content to an existing file on HDFS:
```bash
hdfs dfs -appendToFile localfile.txt /user/hadoop/hdfspath
```

---

### **2. Directory Operations**

#### 📁 **Create a directory**
Create a new directory in HDFS:
```bash
hdfs dfs -mkdir /path/to/directory
```

#### 🛠️ **Create multiple directories**
Create multiple directories in a single command:
```bash
hdfs dfs -mkdir -p /path/to/dir1 /path/to/dir2
```

#### 🧑‍💻 **Check directory usage with summary**
View the disk usage of a directory in human-readable format:
```bash
hdfs dfs -du -s -h /path/to/directory
```

#### 📑 **List contents of a directory**
List the files in a directory on HDFS:
```bash
hdfs dfs -ls /path/to/directory
```

---

### **3. File Operations**

#### ✏️ **Rename or move a file in HDFS**
Rename or move a file within HDFS:
```bash
hdfs dfs -mv /path/to/oldfile /path/to/newfile
```

#### 📦 **Copy a file within HDFS**
Copy a file from one location in HDFS to another:
```bash
hdfs dfs -cp /path/to/source /path/to/destination
```

#### 🗂️ **Count files, directories, and bytes in HDFS**
Get the count of files, directories, and the total byte size in a directory:
```bash
hdfs dfs -count /path/to/directory
```

#### 📝 **Display the first few lines of a file**
View the first few lines of a file:
```bash
hdfs dfs -head /path/to/file
```

#### 📚 **Display the last few lines of a file**
View the last few lines of a file:
```bash
hdfs dfs -tail /path/to/file
```

#### 🔒 **Display file checksum**
Verify file integrity by checking the checksum:
```bash
hdfs dfs -checksum /path/to/file
```

---

### **4. File Permission and Ownership**

#### 🔧 **Change file or directory permissions**
Change the permissions of a file or directory:
```bash
hdfs dfs -chmod 755 /path/to/file-or-directory
```

#### 🧑‍🔧 **Change file or directory ownership**
Change the ownership of a file or directory:
```bash
hdfs dfs -chown user:group /path/to/file-or-directory
```

#### 📊 **Set file replication factor**
Change the replication factor of a file or directory:
```bash
hdfs dfs -setrep -w 3 /path/to/file-or-directory
```

---

### **5. Data Verification and Repair**

#### 🛡️ **Verify the file checksum**
Check if the file’s checksum matches its original value:
```bash
hdfs dfs -checksum /path/to/file
```

#### 🛠️ **Recover corrupted blocks in HDFS**
Recover corrupted files by moving or deleting bad blocks:
```bash
hdfs fsck /path/to/file -move -delete
```

---

### **6. Data Migration and Export**

#### 📤 **Export a directory to the local filesystem**
Copy a directory from HDFS to a local file system:
```bash
hdfs dfs -get /path/to/hdfspath /localpath
```

#### 🔄 **Export a file from one HDFS directory to another**
Copy a file from one HDFS location to another:
```bash
hdfs dfs -cp /path/to/hdfspath /new/path/to/hdfspath
```

---

### **7. File System Check**

#### 🏥 **Check the health of HDFS**
Perform a health check on HDFS and get details about block and file status:
```bash
hdfs fsck / -files -blocks -locations
```

#### 📈 **Check block replication status**
View block replication details and the location of blocks:
```bash
hdfs fsck / -blocks -locations
```

---

### **8. HDFS Admin Commands**

#### 🔍 **Show HDFS file system status**
Get a report on the status and health of the HDFS system:
```bash
hdfs dfsadmin -report
```

#### 🛑 **Enable safemode**
Enter HDFS safemode (used for maintenance operations):
```bash
hdfs dfsadmin -safemode enter
```

#### 🚪 **Disable safemode**
Exit from HDFS safemode:
```bash
hdfs dfsadmin -safemode leave
```

#### 📊 **Check safemode status**
Check if HDFS is in safemode:
```bash
hdfs dfsadmin -safemode get
```

#### 🧑‍🔧 **Decommission a DataNode**
Remove a DataNode from the cluster (by updating the `dfs.exclude` file):
```bash
hdfs dfsadmin -refreshNodes
```

---

### **9. YARN Commands**

#### 🖥️ **Resource Manager Operations**

##### 📊 **Check cluster metrics**
Get detailed metrics for the YARN cluster:
```bash
yarn cluster -metrics
```

##### 🔍 **View NodeManager details**
List the details of NodeManagers in the YARN cluster:
```bash
yarn node -list
```

#### 🧑‍💻 **Container Management**

##### 📋 **List containers for an application**
List the containers running for a specific application:
```bash
yarn container -list <Application_ID>
```

##### ⛔ **Kill a specific container**
Terminate a running container:
```bash
yarn container -kill <Container_ID>
```

---

### **10. General Hadoop Commands**

#### 🆘 **Display all Hadoop-related commands**
Get a list of all Hadoop commands:
```bash
hadoop -help
```

#### 📚 **Display help for specific HDFS commands**
Get detailed help for HDFS commands:
```bash
hdfs dfs -help
```

#### 📄 **Display help for YARN commands**
Get detailed help for YARN commands:
```bash
yarn -help
```

---

### **11. General Tips for Hadoop**

- **Use aliases for commonly used commands**  
  Save time by creating aliases for frequently used commands. Add these to your `.bashrc` or `.zshrc`:
  ```bash
  alias hls="hdfs dfs -ls"
  alias hput="hdfs dfs -put"
  alias hget="hdfs dfs -get"
  ```

- **Use `-help` with any Hadoop command**  
  To learn more options and features, always try `-help` with any Hadoop command:
  ```bash
  hdfs dfs -help
  yarn -help
  hadoop -help
  ```

---

By following these instructions, you will be able to easily manage and manipulate files, directories, and resources in Hadoop Distributed File System (HDFS) and YARN.
