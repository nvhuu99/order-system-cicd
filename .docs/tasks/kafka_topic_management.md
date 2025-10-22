### REFS:

* [Adding and removing topics](https://kafka.apache.org/documentation/#basic_ops_add_topic)
* [Kafka Replication High-level Design](https://cwiki.apache.org/confluence/display/kafka/kafka+replication#KafkaReplication-Synchronousreplication)

### Design:

**Num of replicas:** 

- How many server will have the data. Choosing

**Num of partitions:**

- A Kafka topic is divided into partitions. Kafka distributes these partitions across different brokers in the cluster. Which means not all topic data are store on a single server.
- One leader replica — the broker that handles all reads/writes.
- Follower replicas — brokers that keep copies of the partition data for fault tolerance.

**Choosing the number of replicas:**

- Leader does not send the replication request, but wait for replicas to pull the new message. But still, it's a sync-replication model. Therefore, the greater the replication-factor, the longer it take to publish a message. 
- **Trade off**: Replication-factor can easily adjust base on the performance. It's a trade off between write performance with data-redundancy.

**Choosing the number of partitions:**

- Read/Write operation performance seem not to be affect much by how many partitions. You still read/write from/to a single partition leader.
- Parallel processing: the more partitions, the more consumer can run in parallel. 
- **Trade off**: The number of partitions is not easily changed. Plan before you choose the number. It's a trade off between data-redundancy, cost of management, with parallelism.

### Add Topic:

**Auto creation:** created automatically when data is first published to a non-existent topic
**Create new topic:** beable to set partitions, replication-factor.

### Modifying Topic:

**Add more partition**: Depend on "partition algorithm", Kafka won't redistribute data, and it can be shuffle.
**Reduce partition:** Not supported at the momment.

### Commands:



Check if all brokers are registered and healthy
    
    /opt/bitnami/kafka/bin/kafka-broker-api-versions.sh --bootstrap-server localhost:9092

Check cluster ID and controller

  /opt/bitnami/kafka/bin/kafka-metadata-quorum.sh --bootstrap-server localhost:9092 describe --status

Topic commands:

    /opt/bitnami/kafka/bin/kafka-topics.sh --describe --topic cart-update-requests --bootstrap-server kafka:9092
    
    /opt/bitnami/kafka/bin/kafka-topics.sh --bootstrap-server kafka:9092 --list

    /opt/bitnami/kafka/bin/kafka-topics.sh --bootstrap-server kafka:9092 --delete --topic cart-update-requests

    /opt/bitnami/kafka/bin/kafka-topics.sh \
      --bootstrap-server kafka:9092 \
      --create \
      --replication-factor 3 \
      --partitions 3 \
      --topic cart-update-requests


