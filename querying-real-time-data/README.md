# Real-Time Data Query Problem

This problem involves designing an architecture, which can be presented either verbally or with a diagram. The solution should include hardware or software recommendations, supported by technology/product suggestions and implementation details.

## Case:

For a hypothetical customer satisfaction calculation application, batch, online, and streaming data related to customers are processed.
- **Batch data** resides in DWH/NoSQL systems.
- **Streaming data** is handled through message queues (MQ) or Kafka.
- **Online data** is accessed via APIs.

All data environments share a unique key for customers. Customer satisfaction is calculated for a base population every minute using the relevant iteration’s stream data. To compute satisfaction for the stream population, hourly stream windowed aggregations, which include previous minute iterations, are critical. Batch and online data are integrated with stream windows on Spark for additional computations. Data from these three modules is evaluated simultaneously to take customer-specific actions.

All data is ingested into Spark, where Spark SQL and DataFrame operations are used for querying and achieving customer satisfaction calculations.

# Data Processing Architecture Diagram

```plaintext
  +---------------+  +---------------+  +---------------+
  | Stream Data 1 |  | Stream Data 2 |  | Stream Data 3 |
  +---------------+  +---------------+  +---------------+
          |                 |                  |
          +-----------------+------------------+
                            |
                            |                                +----------------+
                            |                          ----- | Batch DWH Data |
                  +------------------+                |      +----------------+
                  |     * Spark      |------Get--------
                  |   Hesaplamalar   |                |      +------------------+
                  +------------------+                 ----- | Batch NoSQL Data |
                            |                                +------------------+
                            |Api Bulk Get
                            |
                      +-------------+
                      | Online Data |
                      +-------------+
```

---

## Data Details:

- **Batch Data:**
  - Updates daily.
  - Only the most recent day's data is required for satisfaction calculations.
  - For 100 million customers, 5,000 different data types impacting satisfaction are generated daily.
  - Data is stored in DWH or NoSQL environments across different tables/collections.

- **Streaming Data:**
  - Managed via MQ/Kafka and updated in real-time.
  - Approximately 5,000 events per second are sent, with 100 variables per customer.
  - Stream data is stored in hourly windows and aggregated for calculations.

- **Online Data:**
  - Retrieved via bulk API calls every minute for the customer population.

Spark integrates the batch, streaming, and online data in line with **Lambda Architecture**, using Spark SQL or DataFrames.

---

## Testing the Spark Process

The accuracy of the Spark process and Spark SQL results used in the customer satisfaction calculation must be validated. To enable testing:

1. **Establish an Alternative Method:**
   - Create an alternative architecture to verify the existing method’s results.
   - Use separate sources to process the data differently while maintaining compatibility with real-time streaming data.

2. **Approach:**
   - Batch, streaming, and online data should also flow through this alternative pipeline, allowing comparative validation of Spark SQL results.