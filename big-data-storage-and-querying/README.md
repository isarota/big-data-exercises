# Big Data Storage and Querying

This problem requires the design of an architecture, which can be presented either verbally or with a diagram. The proposed design should be supported with hardware or software recommendations, as well as technology/product suggestions and implementation details.

#### Case:
The goal is to archive regulatory records such as server logs, firewall logs, and network trace logs, and to enable querying these logs via a web service under specific SLAs (Service Level Agreements).

- An average of **2 TB** of compressed log files (gzip) is generated daily. The compression ratio is 10:1, meaning the uncompressed size of the files can reach **20 TB**.
- File sizes range between **10 MB and 100 MB**. The contents include system logs, firewall logs, communication logs, etc.
- Source system data will be written to an **NFS server** in a specified folder structure.
- The data must be queryable within a maximum of **2 hours** after it is created on the source system.
- Log records need to be stored in a queryable format for a **2-year retention period**.
- Queries are expected to retrieve logs by specific unique IDs for each log type within **1-day records**.
- On average, **10 distinct query types** are received daily. Based on `WHERE` conditions, the total number of queries exceeds **1,000**.
- The SLA for a single query is **5 minutes**. (If a query reads a week’s worth of data, the SLA would be 5x7 = **35 minutes**.)

---

### Requirements:
To meet the SLA requirements, what topology and architecture should be designed?  
What technologies should be used for storage, middleware, and endpoints to implement this architecture?  
Disaster recovery should also be considered when designing the solution.
