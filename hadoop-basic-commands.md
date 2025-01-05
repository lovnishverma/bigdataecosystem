Here’s  list of Basic **HDFS commands**

---

### **HDFS Commands - Extended**

#### **1. File Creation and Upload**
- **Create a new file locally**  
  Use the `echo` command to create a file locally:  
  ```bash
  echo "This is a sample file" > localfile.txt
  ```  

- **Upload a local file to HDFS**  
  ```bash
  hdfs dfs -put localfile.txt /user/hadoop/destination-path
  ```

- **Append content to an HDFS file**  
  ```bash
  hdfs dfs -appendToFile localfile.txt /user/hadoop/hdfspath
  ```
  Appends the content of `localfile.txt` to an existing HDFS file.

---

#### **2. Directory Operations**
- **Create multiple directories**  
  ```bash
  hdfs dfs -mkdir -p /path/to/dir1 /path/to/dir2
  ```
  Creates multiple directories in one command.

- **Check directory usage with summary**  
  ```bash
  hdfs dfs -du -s -h /path/to/directory
  ```
  Displays the summarized disk usage in human-readable format.

---

#### **3. File Operations**
- **Rename a file in HDFS**  
  ```bash
  hdfs dfs -mv /path/to/oldfile /path/to/newfile
  ```
  Renames or moves a file within HDFS.

- **Copy a file within HDFS**  
  ```bash
  hdfs dfs -cp /path/to/source /path/to/destination
  ```

- **Count files, directories, and bytes in HDFS**  
  ```bash
  hdfs dfs -count /path/to/directory
  ```

- **Display the first few lines of a file in HDFS**  
  ```bash
  hdfs dfs -head /path/to/file
  ```
  Displays the first few lines of a file.

- **Display the last few lines of a file in HDFS**  
  ```bash
  hdfs dfs -tail /path/to/file
  ```

- **Display file checksum**  
  ```bash
  hdfs dfs -checksum /path/to/file
  ```
  Shows the checksum of a file for verifying data integrity.

---
You're right! The **remove file** command is an essential part of file management in HDFS. Here is the missing command for **removing a file**:

---

### **File Removal in HDFS**

#### **Remove a file in HDFS**
```bash
hdfs dfs -rm /path/to/file
```
This command removes a file from HDFS.

#### **Remove a directory in HDFS**
```bash
hdfs dfs -rmdir /path/to/directory
```
This removes an empty directory from HDFS.

#### **Remove a directory and its contents recursively**
```bash
hdfs dfs -rm -r /path/to/directory
```
This command removes the directory and all its contents recursively.

---


#### **4. File Permission and Ownership**
- **Change file or directory permissions**  
  ```bash
  hdfs dfs -chmod 755 /path/to/file-or-directory
  ```

- **Change file or directory ownership**  
  ```bash
  hdfs dfs -chown user:group /path/to/file-or-directory
  ```

- **Set file replication factor**  
  ```bash
  hdfs dfs -setrep -w 3 /path/to/file-or-directory
  ```
  Changes the replication factor of a file or directory to `3` and waits for the operation to complete.

---

#### **5. Data Verification and Repair**
- **Verify the file checksum**  
  ```bash
  hdfs dfs -checksum /path/to/file
  ```
  Verify the checksum to ensure the file's integrity.

- **Recover corrupted blocks in HDFS**  
  ```bash
  hdfs fsck /path/to/file -move -delete
  ```
  Checks and repairs corrupted files by moving or deleting bad blocks.

---

### **Additional YARN Commands**

#### **Resource Manager Operations**
- **Check cluster metrics**  
  ```bash
  yarn cluster -metrics
  ```

- **View NodeManager details**  
  ```bash
  yarn node -list
  ```

#### **Container Management**
- **List containers for an application**  
  ```bash
  yarn container -list <Application_ID>
  ```

- **Kill a specific container**  
  ```bash
  yarn container -kill <Container_ID>
  ```

---

### **HDFS Admin Commands**

#### **1. Check and Monitor HDFS**
- **Show HDFS file system status**  
  ```bash
  hdfs dfsadmin -report
  ```

- **Safemode operations**  
  Enable, disable, or get the status of HDFS safemode:  
  ```bash
  hdfs dfsadmin -safemode enter
  hdfs dfsadmin -safemode leave
  hdfs dfsadmin -safemode get
  ```

#### **2. DataNode Management**
- **Decommission a DataNode**  
  Update the `dfs.exclude` file to include the DataNode, then execute:  
  ```bash
  hdfs dfsadmin -refreshNodes
  ```

- **Check DataNode block status**  
  ```bash
  hdfs fsck / -blocks -locations
  ```

---

### **General Tips**
- Always use `-help` with any Hadoop command to explore additional options:
  ```bash
  hdfs dfs -help
  yarn -help
  hadoop -help
  ```

- Use aliases for frequently used commands. For example, add this to your `.bashrc` or `.zshrc`:
  ```bash
  alias hls="hdfs dfs -ls"
  alias hput="hdfs dfs -put"
  alias hget="hdfs dfs -get"
  ```

These commands cover a wide range of HDFS and YARN operations, from basic file manipulation to administrative tasks, ensuring you have the tools to effectively manage a Hadoop cluster.
