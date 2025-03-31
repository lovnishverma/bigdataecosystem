To perform a **Word Count** using Hadoop, follow these steps:

---

### **Configure `mapred-site.xml`**  
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
Hadoop provides a built-in Word Count example. **(Replace 3.3.6 with your hadoop Version)** Run it with:

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar wordcount /user/nielit/input /user/nielit/output
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

**METHOD: 2**

You can write your own **Java MapReduce program** for word count in Hadoop. Follow these steps:

---

## **1. Create the Word Count Java Program**
Create a new file:  
```bash
nano WordCount.java
```

Copy and paste the following Java code:

```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;
import java.util.StringTokenizer;

public class WordCount {

    public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
            }
        }
    }

    public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
                sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

---

## **2. Compile the Java Code**
Make sure you have Hadoop's libraries available. Use the following command to compile:

```bash
javac -classpath $(hadoop classpath) -d . WordCount.java
```

This will generate `.class` files inside the current directory.

---

## **3. Create a JAR File**
Now, package the compiled Java files into a JAR:

```bash
jar -cvf WordCount.jar *.class
```

---

## **4. Upload Input File to HDFS**
If not already uploaded, create an input directory and upload your text file:

```bash
hadoop fs -mkdir -p /user/nielit/input
hadoop fs -put data.txt /user/nielit/input/
```

Verify:
```bash
hadoop fs -ls /user/nielit/input/
```

---

## **5. Run Your Word Count Program**
Execute your custom Word Count JAR in Hadoop:

```bash
hadoop jar WordCount.jar WordCount /user/nielit/input /user/nielit/output
```

---

## **6. View Output**
After the job completes, check the output:

```bash
hadoop fs -ls /user/nielit/output
```

To see the results:

```bash
hadoop fs -cat /user/nielit/output/part-r-00000
```

---

## **7. Download Output (Optional)**
If you want to save the output to your local machine:

```bash
hadoop fs -get /user/nielit/output/part-r-00000 wordcount_output.txt
cat wordcount_output.txt
```

---

## **Troubleshooting**
- If the output directory already exists, delete it before rerunning:
  ```bash
  hadoop fs -rm -r /user/nielit/output
  ```


