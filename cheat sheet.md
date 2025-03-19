# System Design Cheat Sheet for Interviews

## Table of Contents
1. [Protocols](#protocols)
2. [Server Software/Services](#server-software-services)
3. [Databases](#databases)
4. [Caching](#caching)
5. [Scaling](#scaling)
6. [Messaging & Queues](#messaging--queues)
7. [Global Coordination](#global-coordination)
8. [Other Techniques](#other-techniques)
9. [Architectural Patterns](#architectural-patterns)
10. [System Design Building Blocks](#system-design-building-blocks)
11. [Tradeoffs](#tradeoffs)
12. [Advanced Topics](#advanced-topics)

---

## Protocols

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **HTTP/HTTPS** (Hypertext Transfer Protocol / Hypertext Transfer Protocol Secure) | Stateless protocol for web requests; HTTPS adds TLS (Transport Layer Security) encryption. | REST (Representational State Transfer) APIs, Web Services | Ubiquitous, interoperable, ideal for client-server. | Use Flask/FastAPI with TLS (e.g., Certbot for HTTPS). |
| **HTTP/2** (Hypertext Transfer Protocol Version 2) | Evolution of HTTP with binary framing and multiplexing. | High-performance APIs | Reduces latency via multiplexing, header compression. | Deploy with Nginx (HTTP/2 module) or gRPC over HTTP/2. |
| **gRPC** (Google Remote Procedure Call) | High-performance RPC (Remote Procedure Call) using HTTP/2 and Protocol Buffers. | Microservices, RPC | Low-latency, strongly-typed, supports streaming. | Use gRPC-Go with Protobuf; integrate with Kubernetes. |
| **WebSockets** | Persistent, bidirectional connection over TCP (Transmission Control Protocol). | Real-time apps (chat, gaming) | Full-duplex, low overhead for live updates. | Implement with `Socket.IO` or `ws` (Node.js); scale with Redis Pub/Sub. |
| **MQTT** (Message Queuing Telemetry Transport) | Lightweight pub/sub protocol for resource-constrained devices. | IoT (Internet of Things), telemetry | Bandwidth-efficient, reliable over unstable networks. | Use Mosquitto broker; connect with Paho MQTT client. |
| **AMQP** (Advanced Message Queuing Protocol) | Advanced message queuing protocol with robust routing. | Reliable messaging (RabbitMQ) | Ensures delivery, supports complex routing (e.g., fanout). | Deploy RabbitMQ; configure exchanges with `pika` (Python). |
| **Kafka Protocol** (Apache Kafka Protocol) | Distributed log protocol for event streaming. | High-throughput streaming | Durable, scalable, replayable event logs. | Use Kafka cluster with `confluent-kafka` (Python); ZooKeeper for coordination. |
| **GraphQL** (Graph Query Language) | Query language protocol for precise data fetching. | Flexible APIs (GitHub API) | Reduces over-fetching, client-driven queries. | Use Apollo Server; define schemas with resolvers. |

**Tricky Question**: *How does HTTP/2's multiplexing improve performance compared to HTTP/1.1?*  
**Answer**: HTTP/2 allows multiple requests and responses concurrently over a single TCP connection, reducing latency and improving resource utilization compared to HTTP/1.1’s sequential requests.

**Tricky Question**: *When would you choose gRPC over REST for a microservices architecture?*  
**Answer**: gRPC is ideal for low-latency, high-performance scenarios, especially with real-time communication or when strong typing and efficient serialization are needed.

---

## Server Software/Services

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **SMTP** (Simple Mail Transfer Protocol) via Mail Servers | Protocol for email, implemented by mail servers. | Postfix, Sendmail | Standard for email transmission. | Configure Postfix with SMTP relay; use `nodemailer` for automation. |
| **FTP/SFTP** (File Transfer Protocol / Secure File Transfer Protocol) via File Servers | Protocols for file transfer; SFTP uses SSH (Secure Shell) for security. | FileZilla Server, OpenSSH (SFTP) | FTP for simplicity, SFTP for encrypted transfers. | Set up OpenSSH (SFTP) with key auth; use `scp` for transfers. |
| **RTMP** (Real-Time Messaging Protocol) via Streaming Servers | Real-time video streaming protocol via dedicated servers. | Nginx-RTMP, Wowza | Low-latency for live broadcasts (Twitch). | Configure Nginx-RTMP; stream via OBS with RTMP URL. |
| **SNS** (Simple Notification Service) | Managed service for multi-channel notifications. | AWS SNS (Amazon Web Services Simple Notification Service) | Scalable push notifications (SMS, mobile). | Use AWS SDK (boto3) with SNS topics; integrate with Lambda. |
| **NFS** (Network File System) | Distributed filesystem for shared storage. | NFS Server (Linux) | Enables multi-server file access. | Deploy `nfs-kernel-server` on Linux; mount with `mount -t nfs`. |

**Tricky Question**: *How does a reverse proxy differ from a load balancer?*  
**Answer**: A reverse proxy forwards client requests to servers and handles responses, while a load balancer distributes traffic across servers to prevent overload.

**Tricky Question**: *What are the advantages of using a CDN over a traditional web server?*  
**Answer**: CDNs (Content Delivery Networks) cache content at edge locations closer to users, reducing latency and offloading traffic from the origin server.

---

## Databases

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Relational (SQL)** (Structured Query Language) | Tabular, schema-enforced data with ACID (Atomicity, Consistency, Isolation, Durability) properties. | PostgreSQL, MySQL | Strong consistency, transactional integrity. | Use RDS (Relational Database Service) or local install; query with SQLAlchemy (Python). |
| **NoSQL (Key-Value)** (Not Only SQL) | Simple key-value pairs, often in-memory. | Redis (Remote Dictionary Server), DynamoDB | High-speed lookups, caching. | Deploy Redis locally; use DynamoDB via AWS SDK. |
| **NoSQL (Document)** | JSON-like documents with flexible schemas. | MongoDB, CouchDB | Hierarchical data, schema evolution (CMS). | Use MongoDB Atlas; query with PyMongo. |
| **NoSQL (Columnar)** | Wide-column store optimized for analytics. | Apache Cassandra, Bigtable | Scalable writes, time-series (recommendations). | Deploy Cassandra cluster; use CQL (Cassandra Query Language) with `cassandra-driver`. |
| **NoSQL (Graph)** | Node-edge model for relationship-heavy data. | Neo4j, ArangoDB | Complex queries (social networks, fraud). | Run Neo4j; query with Cypher via `neo4j-driver`. |
| **Time-Series** | Optimized for sequential, timestamped data. | InfluxDB, TimescaleDB | Efficient for IoT, monitoring metrics. | Deploy InfluxDB; use `influxdb-client-python`. |

**Tricky Question**: *When would you choose a NoSQL database over a relational database?*  
**Answer**: NoSQL is better for large volumes of unstructured/semi-structured data, offering horizontal scalability and flexible schemas.

**Tricky Question**: *What are the key differences between a document store and a key-value store?*  
**Answer**: Document stores manage hierarchical data with flexible schemas; key-value stores are simpler, offering fast lookups but limited querying.

---

## Caching

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Cache-aside** | App-managed cache with lazy loading. | Redis, Memcached (Memory Cached) | Reduces DB load, flexible control. | Use `redis-py`; app checks cache, fetches on miss. |
| **Write-through** | Synchronous cache and DB updates. | Redis | Cache-DB consistency for read-heavy loads. | Implement with Redis; update both in app logic. |
| **Write-behind** | Asynchronous DB sync after cache write. | Redis | Reduces write latency, eventual consistency. | Use Redis with Celery for async DB writes. |
| **Read-through** | Cache auto-fetches DB on miss. | CDN, API Gateway | Simplifies app logic (e.g., CDN caching). | Configure AWS API Gateway cache or Cloudflare CDN. |
| **Distributed Cache** | Cache spanning multiple nodes with coherence. | Hazelcast, Redis Cluster | Scales for high availability/performance. | Deploy Redis Cluster; use consistent hashing. |
| **Eviction Strategy** | Policies for removing stale cache entries. | LRU (Least Recently Used), LFU (Least Frequently Used), TTL (Time To Live) | Balances memory use (LRU: recent, LFU: frequent). | Set Redis `maxmemory-policy` (e.g., `allkeys-lru`). |

**Tricky Question**: *How does a write-through cache ensure data consistency?*  
**Answer**: Data is written to both cache and backing store simultaneously, ensuring the cache always reflects the latest data.

**Tricky Question**: *What are the pros and cons of using a distributed cache?*  
**Answer**: Pros include scalability and high availability; cons include complexity in cache coherence and network latency.

---

## Scaling

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Horizontal Scaling** | Adds more nodes to distribute workload. | Microservices, Sharding | Infinite scale (e.g., Twitter). | Use Kubernetes for microservices; shard with Vitess (Vitess Database Clustering System). |
| **Vertical Scaling** | Increases power of a single machine. | Single powerful machine | Simpler for compute/memory-bound tasks. | Upgrade AWS EC2 instance (e.g., t3.large → t3.2xlarge). |
| **Load Balancing** | Distributes traffic across servers. | Nginx, HAProxy | Enhances reliability, evens load. | Configure Nginx with round-robin or least-connection. |
| **Database Sharding** | Partitions DB across servers by key. | Vitess (MySQL), Citus (Citus Data) | Scales writes for massive datasets. | Use Vitess for MySQL; define shard keys in app logic. |

**Tricky Question**: *How does horizontal scaling differ from vertical scaling in terms of cost and complexity?*  
**Answer**: Horizontal scaling is cost-effective and resilient but requires complex architecture; vertical scaling is simpler but costlier long-term.

**Tricky Question**: *What are the challenges of implementing database sharding?*  
**Answer**: Challenges include data distribution, query routing, and maintaining consistency across shards.

---

## Messaging & Queues

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Message Queue** | Buffers tasks/messages for processing. | Kafka (Apache Kafka), RabbitMQ (RabbitMQ Message Broker) | Kafka: durable streams; RabbitMQ: reliable queues. | Deploy Kafka with `confluent-kafka`; RabbitMQ with `pika`. |
| **Event-Driven Architecture** | Systems triggered by events, not requests. | Kafka, AWS Lambda (Amazon Web Services Lambda) | Decoupled, real-time (e.g., Uber). | Use Kafka topics with Lambda triggers; process with consumers. |
| **Pub/Sub** (Publish/Subscribe) | Broadcasts messages to multiple subscribers. | Google Pub/Sub, Redis Pub/Sub | Scalable, decoupled event distribution. | Use Google Pub/Sub API or Redis `PUBLISH`/`SUBSCRIBE`. |

**Tricky Question**: *How does a message queue differ from a pub/sub system?*  
**Answer**: Message queues use point-to-point communication; pub/sub broadcasts to multiple subscribers.

**Tricky Question**: *What are the advantages of using an event-driven architecture?*  
**Answer**: It promotes loose coupling, scalability, and real-time processing but can be complex to manage.

---

## Global Coordination

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Consensus Algorithm** | Achieves agreement on state across nodes. | Paxos (Paxos Algorithm), Raft (Raft Consensus Algorithm) | Reliable leader election (e.g., etcd). | Use Raft in Consul; implement Paxos for custom systems. |
| **Distributed Locking** | Ensures exclusive resource access across nodes. | Zookeeper (Apache Zookeeper), Redis | Prevents race conditions (e.g., Redis locks). | Use Zookeeper locks or Redis `SETNX` with expiry. |
| **CAP Theorem** (Consistency, Availability, Partition Tolerance) | Framework balancing Consistency, Availability, Partition Tolerance. | Understand tradeoffs | Guides design (CP: banking, AP: social). | Test with Jepsen for consistency under partitions. |

**Tricky Question**: *How does the CAP theorem influence the design of distributed systems?*  
**Answer**: It forces trade-offs between consistency, availability, and partition tolerance based on system needs (e.g., CP for banks, AP for social media).

**Tricky Question**: *What are the differences between Paxos and Raft consensus algorithms?*  
**Answer**: Paxos is complex but theoretically robust; Raft is more understandable and easier to implement.

---

## Other Techniques

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Consistent Hashing** | Distributes data with minimal reshuffling. | Redis Cluster, DynamoDB | Reduces remapping (e.g., CDNs). | Implement hash rings in Redis Cluster or DynamoDB. |
| **Rate Limiting** | Caps request frequency to prevent overload. | Token Bucket, Leaky Bucket | Ensures fairness, prevents abuse (APIs). | Use Redis with Lua for Token Bucket; Nginx for Leaky Bucket. |
| **Fault Tolerance** | Maintains operation despite failures. | Replication, Failover | High availability (e.g., YouTube). | Replicate with MySQL; failover with HAProxy. |
| **Circuit Breaker** | Halts requests to failing services. | Hystrix, Resilience4j | Prevents cascading failures (microservices). | Use Resilience4j in Spring Boot; monitor with metrics. |

**Tricky Question**: *How does consistent hashing minimize data movement during scaling?*  
**Answer**: It maps data to a ring, minimizing remapping when nodes are added/removed, reducing data movement.

**Tricky Question**: *What are the benefits of using a circuit breaker pattern in microservices?*  
**Answer**: It prevents cascading failures by stopping requests to failing services, allowing recovery and reducing load.

---

## Architectural Patterns

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Microservices** | Small, autonomous services communicating via APIs. | REST/gRPC, Docker, Kubernetes | Independent scaling, deployment (Netflix). | Dockerize services; orchestrate with Kubernetes; use gRPC. |
| **Serverless** | Event-driven compute without server management. | AWS Lambda, Google Cloud Functions | Cost-efficient, auto-scaling (sporadic loads). | Deploy Lambda with S3 triggers; use API Gateway. |
| **Event-Driven** | Systems reacting to asynchronous events. | Kafka, RabbitMQ | Decoupled, real-time (Uber). | Use Kafka for streams; process with consumer groups. |

**Tricky Question**: *How does a microservices architecture improve scalability compared to a monolithic architecture?*  
**Answer**: Microservices allow independent scaling of components, improving resource utilization and deployment speed.

**Tricky Question**: *What are the trade-offs of using a serverless architecture?*  
**Answer**: Benefits include cost savings and scalability; drawbacks include vendor lock-in and cold start latency.

---

## System Design Building Blocks

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **APIs** (Application Programming Interface) | Interfaces for programmatic interaction. | REST, gRPC | REST: simple, gRPC: performant. | Build REST with FastAPI; gRPC with Protobuf. |
| **CDN** (Content Delivery Network) | Edge servers caching static content globally. | Cloudflare, Akamai | Lowers latency, offloads origin (e.g., Netflix). | Configure Cloudflare with origin pull; cache static files. |
| **Proxy vs Reverse Proxy** | Proxy forwards client requests; reverse proxy distributes to servers. | Nginx, HAProxy | Proxy: client-side, Reverse: server-side load balancing. | Use Nginx as reverse proxy with upstreams; proxy via client configs. |
| **DNS** (Domain Name System) | Resolves domain names to IP addresses. | AWS Route 53, Cloudflare DNS | Fast, reliable name resolution. | Set Route 53 with geo-DNS; use Cloudflare for DDoS protection. |
| **API Gateway** | Centralized entry point for API management. | Kong, AWS API Gateway | Handles auth, throttling, routing (e.g., microservices). | Deploy Kong with plugins; use AWS Gateway with Lambda. |

**Tricky Question**: *How does an API gateway enhance security in a microservices architecture?*  
**Answer**: It centralizes authentication, authorization, and rate limiting, providing a single secure entry point.

**Tricky Question**: *What are the differences between a proxy and a reverse proxy?*  
**Answer**: Proxies forward client requests to servers; reverse proxies receive requests for servers, often for load balancing/caching.

---

## Tradeoffs

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Vertical vs Horizontal Scaling** | Vertical: bigger machine; Horizontal: more machines. | Vertical for power, Horizontal for distribution | Vertical: simple, Horizontal: scalable. | Benchmark with JMeter; simulate with Kubernetes. |
| **Concurrency vs Parallelism** | Concurrency: task switching; Parallelism: simultaneous execution. | Concurrency for multitasking, Parallelism for speed | Concurrency: responsiveness, Parallelism: throughput. | Profile with `perf` or `concurrent.futures` (Python). |
| **Long Polling vs WebSockets** | Long Polling: HTTP waits; WebSockets: persistent connection. | Long Polling for legacy, WebSockets for real-time | Long Polling: compatible, WebSockets: efficient. | Test with Postman (Long Polling) vs. `ws` (WebSockets). |
| **Batch vs Stream Processing** | Batch: processes chunks; Stream: real-time flow. | Batch for datasets, Stream for real-time | Batch: analytics, Stream: immediacy. | Compare Spark (batch) vs. Kafka Streams (stream). |
| **Stateful vs Stateless Design** | Stateful: retains context; Stateless: no memory. | Stateful for sessions, Stateless for scalability | Stateful: simple sessions, Stateless: scale. | Simulate with Redis (stateful) vs. REST (stateless). |
| **Strong vs Eventual Consistency** | Strong: immediate sync; Eventual: delayed sync. | Strong for transactions, Eventual for scalability | Strong: accuracy, Eventual: performance. | Test with Jepsen or Chaos Monkey for consistency. |

**Tricky Question**: *How do you decide between using a relational database and a NoSQL database?*  
**Answer**: Consider data structure, scalability, consistency needs, and query complexity (e.g., relational for structured data, NoSQL for scale).

**Tricky Question**: *What are the implications of choosing strong consistency over eventual consistency?*  
**Answer**: Strong consistency ensures accuracy but may reduce availability/performance; eventual consistency prioritizes availability.

---

## Advanced Topics

| **Topic**                          | **What It Is**                                                                   | **What to Use?**                                                                 | **Why?**                                                                                   | **How to Achieve**                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| **Distributed Tracing** | Tracks requests across microservices. | Jaeger (Jaeger Tracing), Zipkin (Zipkin Tracing) | Debugs latency, dependencies. | Use OpenTelemetry with Jaeger; visualize traces. |
| **Disaster Recovery** | Restores system post-failure. | Backups, Replication, Failover | Ensures continuity (e.g., outages). | Use S3 backups, MySQL replication, HAProxy failover. |
| **Distributed Web Crawler** | Parallel web scraping across nodes. | Kafka, Redis, Bloom Filters (Bloom Filter Data Structure) | Kafka: task queue, Bloom: deduplication. | Kafka for jobs, Redis for state, Bloom for uniqueness. |
| **Distributed Cloud Storage** | Scalable, redundant file storage. | Consistent Hashing, Replication, Sharding | Fault-tolerant scale (e.g., S3). | Use hash rings, replicate with HDFS, shard data. |
| **Bloom Filters** | Probabilistic structure for set membership. | Redis Bloom, custom implementation | Space-efficient deduplication. | Use Redis Bloom module or `pybloomfiltermmap`. |
| **Zero-Trust Architecture** (Zero-Trust Security Model) | Security model assuming no implicit trust. | Istio (Istio Service Mesh), OAuth2 (Open Authorization 2.0) | Mitigates insider threats, breaches. | Use Istio for service mesh; OAuth2 for auth. |
| **Chaos Engineering** (Chaos Engineering Practice) | Intentionally injects failures to test resilience. | Chaos Monkey (Chaos Monkey Tool), Gremlin (Gremlin Chaos Engineering Platform) | Validates fault tolerance (e.g., Netflix). | Deploy Chaos Monkey on AWS; script failure scenarios. |

**Tricky Question**: *How does distributed tracing help in debugging microservices?*  
**Answer**: It provides end-to-end visibility into request flows, identifying bottlenecks and failures across services.

**Tricky Question**: *What are the key components of a zero-trust architecture?*  
**Answer**: Strict identity verification, least privilege access, and continuous monitoring secure resources.

---
