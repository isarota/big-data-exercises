# Solution for Real-Time Data Query Exercise

## Problem Summary:  
Batch (daily), streaming (per second), and online (per minute) data are processed.  
**Objective**: Combine these data sources for customer satisfaction calculation, ensure hourly windowed aggregations function correctly, and design an alternative structure to validate the accuracy of results.  

---

## Architectural Design  

### 1. Managing Data with Lambda Architecture  
Lambda architecture is the most suitable for processing both batch and real-time (streaming) data together.  

- **Batch Layer**:  
  - Daily batch data is stored in a Data Warehouse (DWH) or NoSQL-based environment.  
  - Spark processes these data for batch operations and historical analysis.  

- **Speed Layer (Streaming)**:  
  - Streaming data from Kafka or RabbitMQ is processed using Spark Streaming in hourly windows.  
  - Windowing can be implemented as follows:  

```scala
val windowedStream = streamData
  .groupBy(window($"timestamp", "1 hour"))
  .agg(avg($"customerSatisfaction").as("avgSatisfaction"))
```

- **Serving Layer**:  
  - Batch and streaming data are merged using Spark SQL or DataFrames.  
  - Real-time satisfaction calculations are performed as follows:  

```sql
SELECT customerId, satisfactionScore
FROM streamingData
JOIN batchData ON streamingData.customerId = batchData.customerId
```

---

#### 2. Streaming Window Aggregation  
Hourly window operations for streaming data are critical:  

- **Sliding Window**:  
  Aggregates each hour of data and continuously updates with new incoming data.  

- **State Management**:  
  State is maintained during aggregation using Spark Structured Streaming:  

```scala
val streamingQuery = streamData
  .groupBy(window($"timestamp", "1 hour"), $"customerId")
  .agg(avg($"satisfactionScore").as("avgSatisfaction"))
  .writeStream
  .outputMode("update")
  .format("console")
  .start()
```

---

#### 3. Alternative Test Structure  
To test and validate the current method’s accuracy, a parallel system can be established:  

- **Alternative Processing Framework**:  
  - Independent of Spark, an Apache Flink-based system can be implemented for result comparison.  
  - Flink can perform similar window aggregation on streaming data.  

- **Data Validation**:  
  - Process the same batch, streaming, and online data using Flink.  
  - Compare results from Spark and Flink for accuracy verification.  

- **Test Scenarios**:  
  - Compare results for different data sizes (e.g., 1-hour, 1-day, 1-week).  
  - Check for data loss using Spark Checkpoint and Flink State.  

---

#### 4. Disaster Recovery  
To prevent data loss, a backup mechanism should be implemented:  

- **Checkpointing**:  
  - Perform checkpointing for Spark Streaming on HDFS or Amazon S3.  
  - Regularly save the state of each windowed aggregation.  

- **Replication**:  
  - Replicate data on Kafka or RabbitMQ to prevent data loss in case of broker failure.  

- **State Backup**:  
  - Backup aggregation states using Spark Structured Streaming on HDFS.  

---

#### 5. Performance and Resource Optimization  
Ensure the system can handle large data volumes and real-time demands:  

- **Partitioning**:  
  - Partition batch data by customer ID for efficient processing.  

```sql
CREATE TABLE batch_data
PARTITIONED BY (customerId)
```

- **Resource Allocation**:  
  - Use Spark Dynamic Allocation for both streaming and batch processing.  

- **Monitoring**:  
  - Monitor Spark and Kafka performance using Prometheus and Grafana.  
  - Track event counts, window operations, and query durations to identify bottlenecks.  

---

### Pseudocode Example  

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import window, avg

# Create SparkSession
spark = SparkSession.builder \
    .appName("CustomerSatisfaction") \
    .getOrCreate()

# Load batch data
batch_data = spark.read.format("parquet").load("/path/to/batch_data")

# Read streaming data
stream_data = spark.readStream.format("kafka") \
    .option("kafka.bootstrap.servers", "localhost:9092") \
    .option("subscribe", "customer_satisfaction") \
    .load()

# Perform window aggregation on streaming data
windowed_stream = stream_data \
    .groupBy(window("timestamp", "1 hour"), "customerId") \
    .agg(avg("satisfactionScore").alias("avgSatisfaction"))

# Merge batch and streaming data
final_data = windowed_stream.join(batch_data, "customerId")

# Print results
query = final_data.writeStream \
    .outputMode("append") \
    .format("console") \
    .start()

query.awaitTermination()
```

---

### Conclusion  

- Batch, streaming, and online data were processed together using **Lambda Architecture**.  
- Hourly window operations were implemented with Spark Structured Streaming.  
- An alternative test structure using **Apache Flink** was suggested.  
- Disaster recovery mechanisms (checkpointing and replication) were applied.  
- The solution was optimized for performance to ensure accurate results and uninterrupted processing.  
