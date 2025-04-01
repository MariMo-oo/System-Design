# System Design & Database Choices

These notes summarize key considerations when selecting databases for different system design use cases. They cover non-functional requirements (NFRs), caching, file storage, search, metrics tracking, and more.

---

## 1. DB Impact on NFRs and Non-Functional Requirements

- **Key Point:** Different databases are optimized for various query patterns, data structures, and scales.
- **Focus:** Choose a DB that best fits the type of data and operational requirements (e.g., latency, scalability, and consistency).

---

## 2. Use Cases and Factors for Database Selection

When choosing a database, consider these factors:

1. **Data Structure:**
   - **Structured Data:** Typically organized in tables with rows and columns.
   - **Unstructured Data:** Includes data types like text, images, or JSON documents.

2. **Query Patterns:**
   - **Random Access vs. Bulk Reads:** Some DBs are optimized for random reads/updates, while others excel at handling bulk data operations.
   - **Transactional Requirements:** Use databases that support ACID properties when you need strong consistency (e.g., payment systems).

3. **Scale:**
   - Consider how the volume of data changes over time.
   - **Examples:** For rapidly growing datasets (like location pings in ride-sharing apps), columnar databases such as Cassandra or HBase might be appropriate.

---

## 3. Caching Solutions

Caching reduces the load on your primary database by temporarily storing frequently requested data.

- **When to Cache:**
  - Frequent DB calls that can create performance bottlenecks.
  - Components with high latency.
- **How It Works:**
  - The key is typically based on the query’s where clause or request parameters.
  - The value is the expected output of that query.
- **Common Solutions:**
  - **Redis:** Highly battle-tested and widely used.
  - **Memcached**
  - **Etcd**
  - **Hazelcast**

---

## 4. File Storage Options

For file storage, especially for large binary objects (BLOBs) like images and videos, use blob storage solutions:

- **Blob Storage:** Not a typical DB, as these files are not queried frequently.
- **Common Solutions:**
  - **Amazon S3:** Affordable and widely adopted.
- **Enhancement:** Use a Content Delivery Network (CDN) alongside S3 to deliver content faster to geographically distributed users.

### Example:
- **Amazon Product Images/Videos:** Store in S3, and distribute via a CDN for lower latency.
- **Netflix Media:** Uses a combination of blob storage and CDNs for efficient global delivery.

---

## 5. Content Delivery Networks (CDN)

A CDN is a network of distributed servers that cache content closer to the user’s geographical location.

- **Purpose:** Reduces latency by serving static assets (images, videos) from a location near the user.
- **Usage:** Rather than querying the primary storage (like S3) every time, a CDN caches the data for quick access.

---

## 6. Text-Based Search Solutions

For searching text data, a dedicated search engine is often more efficient than a general-purpose database.

- **Use Cases:**
  - Searching for movies on Netflix by title or description.
  - Product searches on Amazon.
- **Technologies:**
  - **Elasticsearch:** Built on top of Apache Lucene, provides powerful full-text search capabilities.
  - **Solr:** Also built on Apache Lucene, used for similar search requirements.
- **Fuzzy Search:**
  - Handles minor typos (e.g., “airport” vs. “airprot”) using techniques like edit distance.
- **Note:** These engines are not primary sources of truth, as they do not guarantee data consistency like traditional databases.

---

## 7. Metrics Tracking & Time-Series Databases

For applications like monitoring system metrics (e.g., CPU utilization, latency):

- **Time-Series DB Characteristics:**
  - Optimized for sequential, append-only writes.
  - Efficient at handling large volumes of time-stamped data.
- **Common Solutions:**
  - **InfluxDB**
  - **OpenTSDB**
- **Example Use Case:**
  - Building a monitoring system similar to Prometheus or Grafana.

---

## 8. Data Warehousing

For storing and analyzing large datasets (e.g., for offline reporting):

- **Purpose:** Aggregate vast amounts of data for analytics and reporting.
- **Common Implementation:**
  - **Hadoop:** Suitable for data warehousing where complex queries and analytics are performed on large datasets.
- **Usage Scenario:**
  - Dumping raw data into Hadoop and then building reporting tools on top of that data warehouse.

---

## 9. Choosing Between RDBMS and NoSQL

### Relational Databases (RDBMS)
- **Use When:**
  - Data is structured (tables, rows, columns).
  - ACID compliance is necessary (e.g., financial transactions).
- **Examples:** MySQL, Oracle, PostgreSQL.
- **Scenario:**
  - Payment systems where a transaction must deduct from one account and credit another atomically.

### NoSQL Databases
- **Use When:**
  - Data is non-structured or semi-structured (documents, key-value pairs).
  - Schema flexibility is needed (e.g., product catalogs with varying attributes).
- **Examples:**
  - **Document DBs:** MongoDB, Couchbase.
  - **Columnar DBs:** Cassandra, HBase, ideal for scenarios with ever-increasing data volumes and simple queries (e.g., tracking driver locations in ride-sharing apps).

---

## 10. Combining Databases in Real Systems

In real-world system design interviews, you may need to use a combination of databases to meet all requirements:

- **Example Scenario:**
  - **Order Placement:** Use an RDBMS for transactions requiring ACID properties.
  - **Historical Reporting:** After an order is delivered, store data in a columnar database for analytics and reporting.
- **Key Insight:** Often, one system cannot serve all purposes optimally. Combining multiple specialized databases can offer a balance of performance, consistency, and scalability
