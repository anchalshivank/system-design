### **Generalized System Design Questions with Case Scenarios**

#### **1. Scalability**
- **Question**: How do you scale a system to handle a sudden spike in traffic, and what trade-offs do you consider?
- **Case Scenario**: Imagine your stock system sees a market crash—10M users (not 1M) send buy requests for stock #54 in 1 minute. How do you scale the Buying Service and Redis to handle 10M requests (e.g., 166k/second)? What happens if you add too many instances too fast?
- **Hint**: Think Load Balancer, consistent hashing, sharding, and resource limits.

#### **2. Consistency**
- **Question**: How do you ensure data consistency across multiple service instances in a distributed system?
- **Case Scenario**: In the stock system, instance 4.1 matches a buy (100.5) and sell (101.0) for stock #54, but instance 4.2 misses the Kafka update due to network lag. A new bid (100.5) arrives at 4.2—how do you prevent it from routing incorrectly (e.g., to PostgreSQL instead of matching)? What if Kafka is down for 5 seconds?
- **Hint**: Locks, offsets, eventual consistency, fallback checks.

#### **3. Low Latency**
- **Question**: How do you minimize latency in a real-time system while maintaining accuracy?
- **Case Scenario**: Traders in your stock system demand sub-second updates for stock #54 prices. With 1M users, Kafka updates take 50ms to reach instances 4.1-4.3, and Redis `MULTI` takes 1ms. How do you ensure bids match in <100ms? What if a trader complains about a 200ms delay?
- **Hint**: Caching, in-memory checks, reducing network hops.

#### **4. Fault Tolerance**
- **Question**: How do you design a system to stay operational when a key component fails?
- **Case Scenario**: Redis 4 (holding stock #54 orders) crashes during a peak of 1M requests. You have a read replica (Redis 4R). How do you switch over without losing trades? What if the Load Balancer fails instead—how do you keep requests flowing?
- **Hint**: Replicas, failover, redundancy, health checks.

#### **5. Data Partitioning**
- **Question**: How do you partition data across nodes to balance load and ensure fast access?
- **Case Scenario**: Stock #54 gets 90% of 1M requests, while other stocks (501-1000) on instance 4 get 10%. Consistent hashing puts all #54 traffic on instance 4, overwhelming it. How do you re-partition (e.g., Redis, Kafka) to spread the load? What if a new hot stock (#55) emerges later?
- **Hint**: Sharding, virtual nodes, dynamic rebalancing.

#### **6. Caching Strategy**
- **Question**: How do you use caching to improve performance, and when do you invalidate it?
- **Case Scenario**: Instance 4 caches `lowest_buy` (100.0) and `highest_sell` (101.0) for stock #54 in-memory. A burst of 1M requests shifts prices to 99.0-102.0, but Kafka lags 1s. How do you keep the cache fresh? What if you cache all 5000 stocks’ ranges—memory issues?
- **Hint**: TTL, write-through, refresh triggers.

#### **7. Handling Backpressure**
- **Question**: How do you manage a system when downstream components can’t keep up with incoming requests?
- **Case Scenario**: PostgreSQL (storing out-of-range bids for stock #54) slows to 1k inserts/second during 1M requests, while Redis handles 10k/second. Bids like 96.5 pile up—how do you avoid crashing? What if Kafka chokes at 20k updates/second?
- **Hint**: Rate limiting, buffering, async processing.

#### **8. Real-Time Updates**
- **Question**: How do you deliver real-time updates to millions of clients efficiently?
- **Case Scenario**: 1M users watching stock #54’s price need updates every match (10k/second). Kafka pushes to instances, but how do you get prices to users’ apps in <1s? What if 10% disconnect and reconnect simultaneously?
- **Hint**: WebSocket, SSE, fan-out, reconnection handling.

#### **9. Trade-Offs in Storage**
- **Question**: How do you choose between different storage systems for a workload, and what are the trade-offs?
- **Case Scenario**: Stock #54’s 1M requests generate 10k trades/second. You use Redis for orders, PostgreSQL for trades, and InfluxDB for price history. What if trades need ACID guarantees but PostgreSQL slows down? Swap with Cassandra?
- **Hint**: Consistency vs. speed, throughput vs. latency.

#### **10. Monitoring and Debugging**
- **Question**: How do you monitor a system to catch issues early and debug failures?
- **Case Scenario**: During 1M requests for stock #54, some users see “trade failed” errors. Latency spikes to 500ms, and Kafka lag hits 2s. How do you spot this? If instance 4.2 crashes, how do you figure out why?
- **Hint**: Metrics, logs, alerts, distributed tracing.
