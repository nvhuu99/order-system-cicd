### Components

These components can be deploy separately or as a single process:

- Controller: Manages cluster metadata, Leader election for topic partitions
- Broker: Storing message streams in topics, serving producers and consumers

### Replication Model (KRaf)

**Kafka uses a Leader-Follower model:**

    Topic: my-topic, Partition: 0 → [Leader: Broker1, Followers: Broker2, Broker3]
    Topic: my-topic, Partition: 1 → [Leader: Broker2, Followers: Broker1, Broker3]  
    Topic: my-topic, Partition: 2 → [Leader: Broker3, Followers: Broker1, Broker2]

- Every partition has one leader and multiple followers
- Leaders are distributed across brokers (not one master for entire cluster)
- Clients only communicate with leaders for each partition
- Followers replicate data from leaders asynchronously

**Kafka maintains an ISR list:**

- Replica must be alive and connected
- Must not lag behind beyond replica.lag.time.max.ms (default: 30 seconds)
- Must make regular fetch requests to leader

**Failover process:**

- **Kafka nodes failover:** In short, they use the "quorum & majority" machenism, and each node will vote for itself.

- **Client perspective:** The Kafka client library automatically and periodically fetches the latest metadata from the cluster . If a broker fails and a new leader is elected for a partition, the client receives this update in the next metadata refresh and will seamlessly switch to communicating with the new leader.

---

### Storage:

The storage are seperated and independent for each node. They do not share the same storage. Data are store directly on the PV.