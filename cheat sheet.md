
## Table of Contents
1. [Protocols](#protocols)
2. [Databases](#databases)
3. [Caching](#caching)
4. [Scaling](#scaling)
5. [Messaging & Queues](#messaging--queues)
6. [Global Coordination](#global-coordination)
7. [Architectural Patterns](#architectural-patterns)
8. [Other Techniques](#other-techniques)
9. [Tradeoffs (Comparisons)](#tradeoffs-comparisons)

---

## Protocols

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **HTTP/HTTPS**    | Stateless web protocol; HTTPS adds TLS encryption. | REST APIs, Web Services | Ubiquitous, interoperable. | Use Flask/FastAPI with TLS (e.g., Certbot). | Secure e-commerce sites (e.g., checkout pages). | HTTP lacks multiplexing; HTTPS adds security vs. HTTP’s plaintext. |
| **HTTP/2**        | Enhanced HTTP with multiplexing and binary framing. | High-performance APIs | Reduces latency, supports push. | Deploy with Nginx or gRPC over HTTP/2. | Real-time dashboards (e.g., stock trading UI). | HTTP/2 vs. HTTP/1.1: Binary, multiplexing vs. text, sequential. |
| **gRPC**          | High-performance RPC with HTTP/2 and Protobufs. | gRPC libraries (Go, Java) | Fast, supports streaming. | Define .proto files, generate stubs. | Low-latency microservices (e.g., analytics). | gRPC vs. REST: Binary vs. JSON, streaming vs. request-response. |
| **WebSockets**    | Bidirectional, persistent TCP connection. | Real-time apps (Socket.IO) | Full-duplex, low overhead. | Use `ws` (Node.js) with Redis Pub/Sub. | Chat apps (e.g., Slack messaging). | WebSockets vs. HTTP: Stateful vs. stateless. |
| **MQTT**          | Lightweight pub/sub for IoT devices. | Mosquitto, Paho MQTT | Bandwidth-efficient, reliable. | Deploy Mosquitto broker, use Paho client. | IoT sensor data (e.g., smart thermostats). | MQTT vs. AMQP: Lightweight vs. feature-rich. |

**Tricky Question**: *How does HTTP/2’s multiplexing improve performance?*  
**Answer**: It allows multiple requests/responses over one connection, reducing latency vs. HTTP/1.1’s sequential nature.

---

## Databases

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Relational (SQL)** | Structured, tabular data with ACID compliance. | PostgreSQL, MySQL | Strong consistency, complex queries. | Use RDS, query with SQLAlchemy. | Banking transactions (e.g., balance updates). | SQL vs. NoSQL: Structured vs. flexible schemas. |
| **NoSQL (Key-Value)** | Simple key-value storage, often in-memory. | Redis, DynamoDB | High-speed lookups, caching. | Deploy Redis or DynamoDB via AWS SDK. | Session caching (e.g., user logins). | Redis vs. DynamoDB: In-memory vs. persistent. |
| **NoSQL (Document)** | Flexible, JSON-like documents. | MongoDB, CouchDB | Hierarchical data, scalability. | Use MongoDB Atlas, query with PyMongo. | Social media posts (e.g., Twitter feeds). | Document vs. Key-Value: Nested vs. flat data. |
| **NoSQL (Columnar)** | Wide-column store for analytics. | Cassandra, Bigtable | Scalable writes, time-series. | Deploy Cassandra, use CQL. | IoT analytics (e.g., sensor logs). | Columnar vs. Document: Analytics vs. flexibility. |
| **NoSQL (Graph)** | Node-edge model for relationships. | Neo4j, ArangoDB | Complex relationship queries. | Use Neo4j, query with Cypher. | Social networks (e.g., friend suggestions). | Graph vs. Relational: Relationships vs. tables. |

**Tricky Question**: *When should you choose NoSQL over SQL?*  
**Answer**: Use NoSQL for unstructured data, high scalability, or flexible schemas (e.g., social media); SQL for structured data and strong consistency (e.g., banking).

---

## Caching

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Cache-aside**   | App-managed lazy-loaded cache. | Redis, Memcached | Reduces DB load, flexible. | Check Redis, fetch on miss. | User profiles (e.g., LinkedIn views). | Cache-aside vs. Write-through: Manual vs. auto-sync. |
| **Write-through** | Sync cache and DB updates. | Redis | Ensures consistency. | Update Redis and DB together. | Inventory systems (e.g., stock levels). | Write-through vs. Write-behind: Sync vs. async. |
| **Write-behind**  | Async DB updates after cache write. | Redis with Celery | Low write latency. | Use Redis, queue DB updates. | Logging actions (e.g., user clicks). | Write-behind vs. Write-through: Latency vs. consistency. |
| **Eviction Strategy** | Rules to remove old cache data. | LRU, TTL (Redis) | Manages memory efficiently. | Set Redis `maxmemory-policy`. | Session caches (e.g., web app logins). | LRU vs. TTL: Usage-based vs. time-based. |

**Tricky Question**: *What’s the difference between write-through and write-behind caching?*  
**Answer**: Write-through updates cache and DB synchronously for consistency; write-behind updates DB asynchronously for lower latency but risks inconsistency.

---

## Scaling

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Horizontal Scaling** | Adds more nodes to distribute load. | Kubernetes, Sharding | High scalability. | Use Kubernetes, shard with Vitess. | Video streaming (e.g., Netflix). | Horizontal vs. Vertical: Distributed vs. single-node. |
| **Vertical Scaling** | Upgrades a single machine’s resources. | Bigger EC2 instance | Simpler for small scale. | Upgrade AWS instance type. | ML training (e.g., model computation). | Vertical vs. Horizontal: Ease vs. scalability. |
| **Load Balancing** | Distributes traffic across servers. | Nginx, HAProxy | Improves reliability. | Configure Nginx round-robin. | Web apps (e.g., Google search). | Load Balancing vs. Sharding: Traffic vs. data split. |
| **Database Sharding** | Splits DB across servers by key. | Vitess, Citus | Scales writes. | Define shard keys in app logic. | Social media (e.g., Instagram users). | Sharding vs. Replication: Write vs. read scale. |

**Tricky Question**: *What’s the main advantage of horizontal scaling over vertical scaling?*  
**Answer**: Horizontal scaling offers near-infinite scalability and resilience by adding nodes, while vertical scaling is limited by hardware.

---

## Messaging & Queues

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Message Queue** | Buffers tasks for processing. | RabbitMQ, Kafka | Decouples components. | Use RabbitMQ with `pika` or Kafka with topics. | Order processing (e.g., Amazon checkout). | Kafka vs. RabbitMQ: Streams vs. queues. |
| **Pub/Sub**       | Broadcasts messages to subscribers. | Google Pub/Sub, Redis | Scalable event distribution. | Use Redis `PUBLISH`/`SUBSCRIBE`. | Stock updates (e.g., trading apps). | Pub/Sub vs. Queue: Broadcast vs. point-to-point. |
| **Event-Driven**  | Reacts to asynchronous events. | Kafka, Lambda | Real-time processing. | Trigger Lambda with Kafka events. | Fraud detection (e.g., banking). | Event-Driven vs. Queue: Reactive vs. buffered. |

**Tricky Question**: *How does pub/sub differ from a message queue?*  
**Answer**: Pub/sub broadcasts to multiple subscribers; a queue delivers each message to one consumer.

---

## Global Coordination

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Consensus Algorithm** | Agreement on state across nodes. | Raft, Paxos | Reliable distributed state. | Use Raft in Consul. | Leader election (e.g., Kubernetes etcd). | Raft vs. Paxos: Simplicity vs. complexity. |
| **Distributed Locking** | Exclusive resource access across nodes. | Zookeeper, Redis | Prevents conflicts. | Use Redis `SETNX` with expiry. | Booking systems (e.g., hotel reservations). | Locking vs. Consensus: Access vs. state. |
| **CAP Theorem**   | Balances Consistency, Availability, Partition Tolerance. | Design tradeoffs | Guides system design. | Test with Jepsen. | CP for banks, AP for social apps. | CP vs. AP: Accuracy vs. uptime. |

**Tricky Question**: *How does CAP theorem affect system design?*  
**Answer**: It forces trade-offs: prioritize consistency (CP) for accuracy or availability (AP) for uptime based on use case.

---

## Architectural Patterns

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Microservices** | Small, independent services with APIs. | Docker, Kubernetes | Scalability, flexibility. | Dockerize services, use gRPC. | E-commerce (e.g., Netflix billing). | Microservices vs. Monolith: Granular vs. simple. |
| **Serverless**    | Event-driven, no server management. | AWS Lambda | Cost-efficient, auto-scaling. | Use Lambda with S3 triggers. | IoT processing (e.g., thermostat data). | Serverless vs. Microservices: Ephemeral vs. persistent. |
| **Event-Driven**  | Reacts to events, not requests. | Kafka | Decoupled, real-time. | Use Kafka with consumer groups. | Ride updates (e.g., Uber). | Event-Driven vs. Request-Driven: Async vs. sync. |

**Tricky Question**: *Why choose microservices over a monolith?*  
**Answer**: Microservices enable independent scaling and deployment, ideal for large, complex systems.

---

## Other Techniques

| **Topic**         | **What It Is**                                      | **What to Use**      | **Why**                              | **How to Achieve**                     | **When to Use (Example)**                  | **Key Differences/Comparison**                  |
|-------------------|----------------------------------------------------|----------------------|-------------------------------------|---------------------------------------|-------------------------------------------|------------------------------------------------|
| **Consistent Hashing** | Distributes data with minimal reshuffling. | Redis Cluster | Reduces remapping. | Use hash rings in Redis. | CDN caching (e.g., Cloudflare). | Consistent vs. Simple Hashing: Minimal vs. full rehash. |
| **Rate Limiting** | Limits request frequency. | Token Bucket (Redis) | Prevents overload. | Use Redis with Lua scripts. | API protection (e.g., login attempts). | Token vs. Leaky Bucket: Burst vs. steady rate. |
| **Fault Tolerance** | Operates despite failures. | Replication, Failover | High availability. | Replicate with MySQL, failover with HAProxy. | Emergency systems (e.g., alerts). | Replication vs. Failover: Data vs. service continuity. |

**Tricky Question**: *How does consistent hashing help during scaling?*  
**Answer**: It minimizes data movement by mapping data to a ring, reducing reshuffling when nodes change.

---

## Tradeoffs (Comparisons)

| **Techniques Compared**         | **Description**                                      | **When to Use (Example)**                                      | **Key Differences**                  |
|---------------------------------|-----------------------------------------------------|----------------------------------------------------------------------|-------------------------------------|
| **Vertical vs. Horizontal Scaling** | Vertical: bigger machine; Horizontal: more nodes. | Vertical: Simple apps (e.g., blogs). Horizontal: Scalable systems (e.g., YouTube). | Vertical is simpler; Horizontal is scalable. |
| **Strong vs. Eventual Consistency** | Strong: immediate sync; Eventual: delayed sync. | Strong: Banks (e.g., transfers). Eventual: Social apps (e.g., likes). | Strong prioritizes accuracy; Eventual prioritizes performance. |
| **REST vs. gRPC** | REST: HTTP text; gRPC: HTTP/2 binary. | REST: Public APIs (e.g., weather). gRPC: Microservices (e.g., payments). | REST is universal; gRPC is faster. |

**Tricky Question**: *How do you choose between REST and gRPC?*  
**Answer**: Use REST for simplicity and broad compatibility; gRPC for performance and streaming in microservices.

