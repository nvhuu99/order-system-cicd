### Error logs:

```
[2025-11-18 02:21:02,099] INFO [MetadataLoader id=0] initializeNewPublishers: the loader is still catching up because we still don't know the high water mark yet. (org.apache.kafka.image.loader.MetadataLoader)
[2025-11-18 02:21:02,199] INFO [MetadataLoader id=0] initializeNewPublishers: the loader is still catching up because we still don't know the high water mark yet. (org.apache.kafka.image.loader.MetadataLoader)
[2025-11-18 02:21:02,300] INFO [MetadataLoader id=0] initializeNewPublishers: the loader is still catching up because we still don't know the high water mark yet. (org.apache.kafka.image.loader.MetadataLoader)
[2025-11-18 02:21:02,400] INFO [MetadataLoader id=0] initializeNewPublishers: the loader is still catching up because we still don't know the high water mark yet. (org.apache.kafka.image.loader.MetadataLoader)
[2025-11-18 02:21:02,486] WARN [RaftManager id=0] Graceful shutdown timed out after 5000ms (org.apache.kafka.raft.KafkaRaftClient)
[2025-11-18 02:21:02,486] ERROR [RaftManager id=0] Graceful shutdown of RaftClient failed (org.apache.kafka.raft.KafkaRaftClientDriver)
java.util.concurrent.TimeoutException: Timeout expired before graceful shutdown completed
        at org.apache.kafka.raft.KafkaRaftClient$GracefulShutdown.failWithTimeout(KafkaRaftClient.java:3675) [kafka-raft-4.0.0.jar:?]
        at org.apache.kafka.raft.KafkaRaftClient.maybeCompleteShutdown(KafkaRaftClient.java:3374) [kafka-raft-4.0.0.jar:?]
        at org.apache.kafka.raft.KafkaRaftClient.poll(KafkaRaftClient.java:3443) [kafka-raft-4.0.0.jar:?]
        at org.apache.kafka.raft.KafkaRaftClientDriver.doWork(KafkaRaftClientDriver.java:64) [kafka-raft-4.0.0.jar:?]
        at org.apache.kafka.server.util.ShutdownableThread.run(ShutdownableThread.java:136) [kafka-server-common-4.0.0.jar:?]
[2025-11-18 02:21:02,486] INFO [kafka-0-raft-io-thread]: Stopped (org.apache.kafka.raft.KafkaRaftClientDriver)
[2025-11-18 02:21:02,486] INFO [kafka-0-raft-io-thread]: Shutdown completed (org.apache.kafka.raft.KafkaRaftClientDriver)
[2025-11-18 02:21:02,501] INFO [MetadataLoader id=0] initializeNewPublishers: the loader is still catching up because we still don't know the high water mark yet. (org.apache.kafka.image.loader.MetadataLoader)
[2025-11-18 02:21:02,583] INFO [kafka-0-raft-outbound-request-thread]: Shutting down (org.apache.kafka.raft.KafkaNetworkChannel$SendThread)
[2025-11-18 02:21:02,584] INFO [kafka-0-raft-outbound-request-thread]: Shutdown completed (org.apache.kafka.raft.KafkaNetworkChannel$SendThread)
[2025-11-18 02:21:02,584] INFO [kafka-0-raft-outbound-request-thread]: Stopped (org.apache.kafka.raft.KafkaNetworkChannel$SendThread)
```

### Tried:

- [Similar Git issue](https://github.com/bitnami/charts/issues/34015): 

	Manually editing server.properties in the configmap to set `controller.quorum.voters` instead of the new controller.`quorum.bootstrap.servers` **resolves the issue**

	I have noticed that during the upgrade the setting controller.quorum.voters (static quorum, `kraft.version=0`) was replaced with controller.quorum.bootstrap.servers (dynamic quorum, 	kraft.version=1).

	As the documentation states, Kafka is not yet prepared to automatically migrate from one method to another, so 3.x cluster will continue to run using kraft.version=0 until that changes.
