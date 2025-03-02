To perform a **Word Count** using Hadoop, follow these steps:

---

## **1. Ensure Hadoop is Running**
Before running the Word Count example, ensure Hadoop is running:

```bash
start-dfs.sh
start-yarn.sh
```
Verify with:
```bash
jps
```
You should see **NameNode, DataNode, ResourceManager, and NodeManager** running.

---

## **2. Upload the Input File to HDFS**
If you haven't already created a directory in HDFS, do it now:

```bash
hadoop fs -mkdir -p /user/nielit/input
```

Now, upload your text file (`data.txt`):

```bash
hadoop fs -put data.txt /user/nielit/input/
```

Verify the upload:
```bash
hadoop fs -ls /user/nielit/input/
```

---

## **3. Run the Word Count Example**
Hadoop provides a built-in Word Count example. Run it with:

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.4.0.jar wordcount /user/nielit/input /user/nielit/output
```

> **Note:** If you downloaded a different Hadoop version, update `3.4.0` accordingly.

---

![Screenshot from 2025-03-02 17-45-02](https://github.com/user-attachments/assets/44613f55-cdf0-48fc-9769-9ff7066c70e3)

## **4. Check the Output**
Once the job completes, view the output files:

```bash
hadoop fs -ls /user/nielit/output
```

The output is usually stored in `part-r-00000`. To read the results:

```bash
hadoop fs -cat /user/nielit/output/part-r-00000
```

---

## **5. Download the Output to Your Local System (Optional)**
If you want to copy the results from HDFS to your local machine:

```bash
hadoop fs -get /user/nielit/output/part-r-00000 wordcount_output.txt
cat wordcount_output.txt
```


![Screenshot from 2025-03-02 17-46-44](https://github.com/user-attachments/assets/5d5d3b51-708f-4ff3-8476-a518c5f8ee3e)

