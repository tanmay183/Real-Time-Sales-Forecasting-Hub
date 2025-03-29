# **Sales Data Projection Pipeline - Detailed Analysis**  
![architecture](https://github.com/user-attachments/assets/ef0b7cce-00c0-411a-825e-af8c3dfa7e72)

## **🔹 Introduction**  
The **Sales Data Projection Pipeline** is a **real-time Change Data Capture (CDC) system** designed to efficiently **ingest, transform, and analyze sales data**. The pipeline captures real-time changes from **DynamoDB**, streams them via **Kinesis**, processes them using **Lambda**, and stores them in **Amazon S3** for further analysis using **Athena** and **Glue**.  

🚀 **Key Benefits:**  
✔️ **Real-time processing** of sales transactions  
✔️ **Scalable architecture** leveraging AWS services  
✔️ **Efficient data transformation** using Lambda  
✔️ **Cost-effective storage & querying** via S3 & Athena  
✔️ **Minimal operational overhead** with automated schema detection  

---

## **🔹 Pipeline Architecture**  

The pipeline follows a **sequential workflow**, ensuring smooth data flow from ingestion to analysis. Below are the key steps:  

### **1️⃣ Data Capture with DynamoDB & Streams**  
- A **DynamoDB table (`OrderTable`)** is created to store sales transactions, with `orderId` as the primary key.  
- **DynamoDB Streams** are enabled to **capture changes** (INSERT, UPDATE, DELETE) in real time.  
- A **Python script (`mock_data_generator_for_dynamodb.py`)** is used to **simulate sales transactions** and insert records into DynamoDB.  

**Why use DynamoDB Streams?**  
✔️ Captures only **new or modified data**, reducing unnecessary processing.  
✔️ Enables **real-time tracking** of sales transactions.  

---

### **2️⃣ Real-Time Data Streaming via EventBridge Pipe & Kinesis**  
- **EventBridge Pipe** is used to automatically route **DynamoDB CDC events** to **Kinesis Data Stream (`Kinesis-sales-order`)**.  
- **Kinesis ensures fault-tolerant, scalable streaming** of sales data.  

**Why use Kinesis instead of direct S3 ingestion?**  
✔️ **Enables real-time data streaming** instead of batch processing.  
✔️ **Ensures high throughput** while maintaining low latency.  
✔️ **Supports parallel data processing** for multiple consumers.  

---

### **3️⃣ Data Transformation & Processing with Lambda & Firehose**  
- **Kinesis Firehose (`kinesis-to-S3-delivery`)** is configured to **batch and transform** data before storage.  
- A **Lambda function (`stream_transformation`)** processes raw Kinesis records into a structured format before sending them to **Amazon S3**.  

**Key Benefits of Using Lambda for Transformation:**  
✔️ Ensures **data formatting consistency**.  
✔️ Reduces **S3 storage overhead** by removing redundant fields.  
✔️ Enhances **query performance** in Athena by ensuring clean data.  

🔹 **Example of Data Transformation:**  
**🔸 Raw Kinesis Data:**  
```json
{ "orderId": "123", "product": "Laptop", "price": "1000", "timestamp": "2025-03-29T12:00:00Z" }
{ "orderId": "124", "product": "Phone", "price": "500", "timestamp": "2025-03-29T12:01:00Z" }
```  
**🔸 Transformed Data Stored in S3:**  
```json
{ "orderId": "123", "product": "Laptop", "price": 1000, "timestamp": "2025-03-29 12:00:00" }
{ "orderId": "124", "product": "Phone", "price": 500, "timestamp": "2025-03-29 12:01:00" }
```  

---

### **4️⃣ Storage in Amazon S3**  
- **Transformed sales data is stored in an S3 bucket (`sales-data-projection-outcome`).**  
- **Firehose ensures data is batched efficiently** before writing to S3, optimizing cost and performance.  

✔️ **Why S3?**  
- **Highly scalable** and **cost-efficient** storage solution.  
- **Supports Athena queries** without needing a traditional database.  
- **Durable storage** with 99.99% availability.  

---

### **5️⃣ Querying & Analysis using AWS Glue & Athena**  
- **AWS Glue Database (`sales-data-projection-db`)** is created to organize data for querying.  
- **AWS Glue Crawler** automatically detects schema and creates a metadata table.  
- **Athena SQL Queries** can be executed directly on S3-stored data.  

🔹 **Example Query in Athena to Retrieve Latest Sales Data:**  
```sql
SELECT * FROM sales_data_projection_db.sales_table
WHERE timestamp >= now() - interval '1 day'
ORDER BY timestamp DESC;
```  
✔️ Enables **ad-hoc analysis without a separate database**.  
✔️ Supports **efficient querying on large datasets** using Presto engine.  

---

## **🔹 Step-by-Step Implementation Summary**  
| Step | Service | Action |
|------|---------|--------|
| **1️⃣ Data Capture** | **DynamoDB** | Stores sales transactions & enables Streams |
| **2️⃣ Real-Time Streaming** | **EventBridge Pipe & Kinesis** | Streams data changes in real-time |
| **3️⃣ Transformation & Processing** | **Lambda & Kinesis Firehose** | Cleans & processes records before storage |
| **4️⃣ Storage** | **Amazon S3** | Stores structured sales data |
| **5️⃣ Query & Analysis** | **AWS Glue & Athena** | Enables querying & insights extraction |

---

## **🔹 Key Advantages of This Architecture**  
✅ **Real-Time Data Processing** – Enables live tracking of sales events.  
✅ **Scalability** – Automatically scales with increasing data volume.  
✅ **Low Latency** – Ensures near-instantaneous data updates.  
✅ **Minimal Cost** – Pay-as-you-go AWS services eliminate upfront infrastructure costs.  
✅ **Schema Evolution Support** – AWS Glue automatically detects changes in data structure.  

---

## **🔹 Final Thoughts**  
The **Sales Data Projection Pipeline** is a **robust, scalable, and cost-effective solution** for handling real-time sales data. By leveraging **DynamoDB, Kinesis, Lambda, S3, Glue, and Athena**, this architecture ensures that **data ingestion, processing, storage, and analysis happen seamlessly** with minimal manual intervention.  

