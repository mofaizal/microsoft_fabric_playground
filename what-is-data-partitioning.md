# Data partitioning

Data partitioning is a technique used to divide large datasets into smaller, more manageable pieces called partitions. This is particularly important in distributed systems like Apache Spark, where partitioning can optimize the performance of data processing tasks by distributing the workload across multiple nodes.

Two common partitioning strategies are **modulus (modular) partitioning** and **range partitioning**. Here's an overview of each:

### **1. Modulus (Modular) Partitioning**

**How it Works:**
- In modulus partitioning, the partition is determined by applying the modulus operation (`%`) to a column’s value. This operation returns the remainder when one number is divided by another.
- For example, if you partition by a customer ID and you want to divide the data into 4 partitions, the partition number is determined by the formula: 
  \[
  \text{Partition Number} = \text{Customer ID} \% 4
  \]
- This ensures that records with similar values (in terms of modulo) end up in the same partition.

**Example:**
- Suppose you have customer IDs ranging from 1 to 1000, and you want to partition this data into 4 partitions.
- If the Customer ID is 15:
  \[
  15 \% 4 = 3
  \]
  So, this record will be placed in partition 3.
- If the Customer ID is 102:
  \[
  102 \% 4 = 2
  \]
  So, this record will be placed in partition 2.

**When to Use It:**
- Modulus partitioning is useful when you want to ensure an even distribution of data across partitions, and when the values are numeric or can be converted to a numeric format.
- It's commonly used in scenarios like distributed hash tables, sharding databases, and parallel processing systems.

### **2. Range Partitioning**

**How it Works:**
- In range partitioning, the data is divided into partitions based on a range of values in one or more columns.
- The range boundaries are defined explicitly, and each range of values corresponds to a different partition.
- For example, if partitioning by a date column, you could have ranges like "January-March", "April-June", etc., where each range is a partition.

**Example:**
- Suppose you have a dataset with a `Transaction Date` column and want to partition the data by quarter:
  - Partition 1: January 1st - March 31st
  - Partition 2: April 1st - June 30th
  - Partition 3: July 1st - September 30th
  - Partition 4: October 1st - December 31st
- A transaction dated May 15th would fall into Partition 2.

**When to Use It:**
- Range partitioning is ideal when the data is naturally ordered and you want to group related data together, such as time-series data, sorted numerical data, or alphabetically sorted strings.
- It is commonly used in databases, distributed storage systems, and big data processing frameworks to enhance query performance by allowing range scans within a partition.

### **Behind the Scenes:**

**In Distributed Systems:**
- **Modulus Partitioning:** The system computes the modulus for each record and assigns it to a partition. This is computationally light and ensures a uniform distribution, making it highly effective for balancing load across nodes.
- **Range Partitioning:** The system first needs to determine the range boundaries, which can involve sorting the data or analyzing the distribution of values. Once the ranges are defined, the data is scanned and assigned to the appropriate partition based on the range criteria.

### **Benefits of Partitioning:**
- **Performance:** Partitioning allows parallel processing by spreading data across multiple nodes or threads, reducing the overall processing time.
- **Scalability:** Systems can handle larger datasets by processing smaller, manageable partitions rather than dealing with the entire dataset at once.
- **Optimized Queries:** Queries can be optimized by scanning only the relevant partitions rather than the entire dataset, which speeds up data retrieval.

### **Drawbacks:**
- **Skewed Data Distribution:** If the data is not uniformly distributed, some partitions might end up with much more data than others, leading to processing bottlenecks (e.g., in modulus partitioning with skewed keys).
- **Complexity:** Managing partitions can add complexity to data management, particularly when dealing with large-scale data updates or dynamic partition schemes.

Understanding these partitioning strategies is crucial for optimizing data processing, particularly in distributed systems like Spark, where efficient data partitioning can make a significant difference in performance.



#### Let's clarify how the modulus operation (`%`) works, particularly in the context of partitioning.

### Understanding the Modulus Operation

The modulus operation (`%`) calculates the remainder of the division between two numbers. It’s not a division operation but rather a way to find out what remains after division.

For example:

- \( 102 \div 4 = 25 \) remainder **2**.  
- So, \( 102 \% 4 = 2 \).

The modulus operator gives the remainder as the result, **not** the quotient or the decimal division result. This remainder is then used to determine the partition.

### Example in Partitioning

Let’s revisit the modulus partitioning example:

Suppose you have customer IDs and want to distribute them across 4 partitions:

- **Customer ID 102**:
  - \( 102 \% 4 = 2 \)
  - So, this record goes into Partition 2.

- **Customer ID 25**:
  - \( 25 \% 4 = 1 \)
  - So, this record goes into Partition 1.

- **Customer ID 77**:
  - \( 77 \% 4 = 1 \)
  - So, this record goes into Partition 1 as well.

### Why is this Useful?

In distributed systems like Spark, the modulus operation is frequently used to distribute records evenly across partitions. For example, if you have 4 partitions:

- The system computes the remainder of each record’s key (like a customer ID) when divided by the number of partitions (e.g., 4).
- The record is then placed in the partition corresponding to that remainder.

### Summary of How It Works:

- **Modulus Operation:** Finds the remainder after division.
  - \( x \% n = r \), where `x` is your value (e.g., customer ID), `n` is the number of partitions, and `r` is the remainder.
  - The remainder `r` corresponds to the partition number.
- **Partition Assignment:** Data is assigned to a partition based on this remainder.

### Application Example:

Let's say you're partitioning customer data across 4 partitions:

- **Customer ID 100**: \( 100 \% 4 = 0 \) → Partition 0
- **Customer ID 101**: \( 101 \% 4 = 1 \) → Partition 1
- **Customer ID 102**: \( 102 \% 4 = 2 \) → Partition 2
- **Customer ID 103**: \( 103 \% 4 = 3 \) → Partition 3
- **Customer ID 104**: \( 104 \% 4 = 0 \) → Partition 0 (starts again)

This way, the records are distributed evenly across the partitions, ensuring no single partition becomes a bottleneck. This approach is essential in distributed systems to achieve parallelism and load balancing.

Let's dive deeper into how the modulus operation (`%`) works and how it relates to partitioning in distributed systems.

### **Understanding the Modulus Operation**

The modulus operation (`%`) calculates the remainder of a division between two numbers. It is expressed as:

\[ x \% n = r \]

Where:
- \( x \) is the number you're dividing (e.g., Customer ID 77).
- \( n \) is the divisor (e.g., number of partitions, which is 4 in this case).
- \( r \) is the remainder of the division.

### **Step-by-Step Explanation**

Let’s break it down using **Customer ID 77**:

1. **Divide the Customer ID by the number of partitions**:
   - \( 77 \div 4 = 19 \) with a remainder of **1**.

2. **Calculate the modulus**:
   - \( 77 \% 4 = 1 \).

3. **Result**:
   - The remainder is **1**, so the record with **Customer ID 77** will be placed in **Partition 1**.

### **How Modulus Affects Partitioning**

When you partition data in a distributed system (like Spark), you might want to distribute records evenly across different partitions. To do this, the system uses the modulus operation on a key column (like Customer ID) to determine which partition a record should go to.

- **Example with 4 Partitions**:
  - **Partition 0**: Holds records where \( x \% 4 = 0 \).
  - **Partition 1**: Holds records where \( x \% 4 = 1 \).
  - **Partition 2**: Holds records where \( x \% 4 = 2 \).
  - **Partition 3**: Holds records where \( x \% 4 = 3 \).

### **Examples with Other Customer IDs**

Let’s consider a few more examples to solidify the concept:

- **Customer ID 100**:
  - \( 100 \% 4 = 0 \) → **Partition 0**.
- **Customer ID 102**:
  - \( 102 \% 4 = 2 \) → **Partition 2**.
- **Customer ID 105**:
  - \( 105 \% 4 = 1 \) → **Partition 1**.
- **Customer ID 108**:
  - \( 108 \% 4 = 0 \) → **Partition 0**.

### **Why Use Modulus for Partitioning?**

The modulus operation helps evenly distribute records across the partitions, ensuring that no single partition becomes overloaded with too much data (which could slow down processing). It’s a simple yet effective way to balance the workload in a distributed environment.

### **Summary**

- The modulus operation \( x \% n \) calculates the remainder when dividing \( x \) by \( n \).
- In partitioning, this remainder determines which partition a record goes into.
- For Customer ID 77, \( 77 \% 4 = 1 \), so it goes into Partition 1.
- This technique helps evenly distribute data across multiple partitions, optimizing performance in distributed systems.

This explanation should give you a better understanding of how modulus works in the context of partitioning.

## Using IPL Dataset how the Partitioning done

### **1. Partitioning by `info_season`**

Partitioning your dataset by `info_season` means dividing the data based on the season year. This is useful if you often query data by specific seasons. Here’s how it would work:

#### **Range Partitioning Example**

- **Range Partitioning**: You could partition the data by ranges of seasons. For example, you might have partitions like:
  - Partition 1: Seasons 2008-2009
  - Partition 2: Seasons 2010-2011
  - Partition 3: Seasons 2012 and onwards

This way, if you query data for the 2008 season, Spark knows it only needs to look at Partition 1.

#### **Modulus Partitioning Example**

- **Modulus Partitioning**: You can use modulus partitioning by the season year to distribute the data evenly. For instance, partition the data into 4 partitions by taking the year modulo 4:

```python
partition_number = info_season % 4
```

This would place:

- 2008 in Partition 0 (2008 % 4 = 0)
- 2009 in Partition 1 (2009 % 4 = 1)
- 2010 in Partition 2 (2010 % 4 = 2)
- 2011 in Partition 3 (2011 % 4 = 3)
- 2012 in Partition 0 (2012 % 4 = 0) (and so on…)

### **2. Applying Partitioning in Spark**

Let’s say you want to partition your DataFrame by `info_season` when saving it. Here’s how you can do it in PySpark:

#### **Example: Saving with Range Partitioning**

If you want to save your DataFrame partitioned by `info_season`:

```python
# Assuming df is your DataFrame with the 'info_season' column
df.write.partitionBy("info_season").parquet("/path/to/save/location")
```

#### **Example: Using Modulus Partitioning**

If you want to partition the data based on the modulus of `info_season`:

```python
from pyspark.sql.functions import col, expr

# Create a new column 'season_mod' to hold the modulus partition number
df = df.withColumn("season_mod", col("info_season") % 4)

# Save partitioned by this new column
df.write.partitionBy("season_mod").parquet("/path/to/save/location")
```

### **3. Querying Partitioned Data**

When you query partitioned data, Spark can skip scanning partitions that don’t meet the query criteria, which improves performance.

For example, if you only need data for the 2008 season, Spark will only scan the relevant partition (Partition 0 in the modulus example, or the range covering 2008).

### **4. Advantages of Partitioning**

- **Performance:** Partitioning reduces the amount of data scanned during queries, speeding up processing.
- **Scalability:** Partitioned data is easier to manage and distribute across a cluster.
- **Organization:** Data is organized by logical partitions (e.g., year, region), making it easier to work with.

### **Conclusion**

Partitioning by `info_season` is a powerful technique for optimizing queries and managing large datasets. Whether you choose range or modulus partitioning depends on your specific use case. Range partitioning groups data logically by season, while modulus partitioning evenly distributes data across partitions, ensuring balanced workloads.