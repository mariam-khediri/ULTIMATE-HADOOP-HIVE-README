# **📖 ULTIMATE HADOOP & HIVE README**  
**From Zero to Big Data Hero**  

---

## **🔍 1. What is Hadoop & Hive?**  
### **Hadoop Definition**  
Hadoop is an **open-source framework** for distributed storage (HDFS) and processing (MapReduce) of large datasets across clusters.  

### **Hive Definition**  
Hive is a **data warehouse system** built on Hadoop that provides SQL-like querying (HiveQL) for structured data.  

### **Key Components**  
| **Component**       | **Purpose**                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| **HDFS**           | Distributed file system for scalable storage.                              |
| **YARN**           | Resource manager for job scheduling.                                       |
| **MapReduce**      | Batch processing model for parallel computation.                           |
| **Hive**           | SQL interface for querying data stored in HDFS.                            |
| **HBase**          | NoSQL database for real-time read/write access.                            |

### **When to Use Hadoop & Hive?**  
- **Hadoop**: Batch processing of **PB-scale data** (logs, backups).  
- **Hive**: SQL analytics on **structured data** (business reports).  

---

## **🛠 2. Installation & Setup**  
### **Step 1: Install Java**  
```bash
sudo apt update
sudo apt install openjdk-8-jdk  # Hadoop works best with Java 8
```

### **Step 2: Download Hadoop**  
```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
tar -xzvf hadoop-3.3.6.tar.gz
cd hadoop-3.3.6
```

### **Step 3: Configure Hadoop**  
1. Edit `etc/hadoop/core-site.xml`:  
   ```xml
   <configuration>
     <property>
       <name>fs.defaultFS</name>
       <value>hdfs://localhost:9000</value>
     </property>
   </configuration>
   ```
2. Edit `etc/hadoop/hdfs-site.xml`:  
   ```xml
   <property>
     <name>dfs.replication</name>
     <value>1</value>  <!-- Single-node cluster -->
   </property>
   ```

### **Step 4: Start HDFS**  
```bash
bin/hdfs namenode -format  # Format HDFS (first time only)
sbin/start-dfs.sh         # Start HDFS daemons
jps                       # Verify NameNode/DataNode are running
```

### **Step 5: Install Hive**  
```bash
wget https://downloads.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
tar -xzvf apache-hive-3.1.3-bin.tar.gz
cd apache-hive-3.1.3-bin
```

---

## **📊 3. Basic Usage**  
### **Hadoop HDFS Commands**  
| Command                          | Description                                  |
|----------------------------------|---------------------------------------------|
| `bin/hdfs dfs -mkdir /user`      | Create a directory in HDFS.                |
| `bin/hdfs dfs -put data.txt /user` | Upload a file to HDFS.                    |
| `bin/hdfs dfs -cat /user/data.txt` | View file contents.                       |

### **Running a MapReduce Job**  
1. **WordCount Example**:  
   ```bash
   bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar wordcount /user/input.txt /user/output
   ```
2. **View Results**:  
   ```bash
   bin/hdfs dfs -cat /user/output/part-r-00000
   ```

### **HiveQL Basics**  
1. **Start Hive Shell**:  
   ```bash
   bin/hive
   ```
2. **Create a Table**:  
   ```sql
   CREATE TABLE employees (id INT, name STRING, salary FLOAT)
   ROW FORMAT DELIMITED
   FIELDS TERMINATED BY ',';
   ```
3. **Load Data**:  
   ```sql
   LOAD DATA LOCAL INPATH '/home/user/employees.csv' INTO TABLE employees;
   ```
4. **Query Data**:  
   ```sql
   SELECT name, AVG(salary) FROM employees GROUP BY name;
   ```

---

## **⚡ 4. Intermediate Skills**  
### **Hive Partitioning**  
```sql
CREATE TABLE logs (
  log_date STRING,
  message STRING
) PARTITIONED BY (year INT, month INT);

-- Load data into a partition
ALTER TABLE logs ADD PARTITION (year=2023, month=10);
```

### **Hive Joins**  
```sql
SELECT a.*, b.* 
FROM employees a 
JOIN departments b ON a.dept_id = b.id;
```

### **Hive UDFs (User-Defined Functions)**  
1. **Write a Java UDF**:  
   ```java
   package com.hive.udf;
   public class UpperUDF extends UDF {
     public String evaluate(String input) {
       return input.toUpperCase();
     }
   }
   ```
2. **Register in Hive**:  
   ```sql
   ADD JAR /path/to/udf.jar;
   CREATE TEMPORARY FUNCTION upper_udf AS 'com.hive.udf.UpperUDF';
   SELECT upper_udf(name) FROM employees;
   ```

---

## **🚀 5. Advanced Techniques**  
### **Hive Optimization**  
1. **Bucketing**:  
   ```sql
   CREATE TABLE bucketed_users (
     id INT,
     name STRING
   ) CLUSTERED BY (id) INTO 32 BUCKETS;
   ```
2. **ORC/Parquet Formats**:  
   ```sql
   CREATE TABLE orc_users (
     id INT,
     name STRING
   ) STORED AS ORC;
   ```

### **YARN Resource Management**  
1. **Submit a Job**:  
   ```bash
   bin/yarn jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar wordcount /input /output
   ```
2. **Monitor Jobs**:  
   Access YARN UI at `http://localhost:8088`.

### **Hive on Spark**  
1. **Set Execution Engine**:  
   ```sql
   SET hive.execution.engine=spark;
   ```

---

## **📚 6. Learning Resources**  
### **Free**  
- [Hadoop Documentation](https://hadoop.apache.org/docs/stable/)  
- [Hive Documentation](https://cwiki.apache.org/confluence/display/Hive/Home)  

### **Paid**  
- **Book**: *Hadoop: The Definitive Guide* (O’Reilly).  
- **Course**: [Big Data Specialization on Coursera](https://www.coursera.org/specializations/big-data).  

---

## **❓ FAQ**  
**Q: How to fix "No space left on device" in HDFS?**  
→ Increase disk space or set `dfs.datanode.du.reserved` in `hdfs-site.xml`.  

**Q: Hive vs. RDBMS?**  
→ Hive scales for **big data** but lacks real-time transactions.  

**Q: How to debug MapReduce jobs?**  
→ Check logs at `http://localhost:8042/logs` (NodeManager UI).  

---

## **🎯 Final Tips**  
✅ **Use `EXPLAIN` in Hive** to optimize queries:  
   ```sql
   EXPLAIN SELECT * FROM employees WHERE salary > 50000;
   ```
✅ **Enable Hive compression**:  
   ```sql
   SET hive.exec.compress.output=true;
   ```
✅ **Avoid small files** in HDFS – Use `hadoop archive` (HAR).  

---
