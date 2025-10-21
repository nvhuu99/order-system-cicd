### REFS:

* [Redis Sentinal](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/)

* [Redis Sentinal Quick Tutorial](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/#a-quick-tutorial)

---

### Note:

* Redis use asyncronous replication, means it can't guarantee all writes are success even with distributed mode.

* When the old master that just comeback available, all of its unsync data will be purished, and synced with the current master. 

### Sentinal Quorum:

If you have 5 Sentinel processes, and the quorum for a given master set to the value of 2, this is what happens:

- Two Sentinels agree about the master being unreachable, start a failover. Note: master here is the process, not the node (a node run both master and sentinal process)

- The majority of sentinal must be reachable for the failover to actually start. Otherwise, it wont start. Note: majority = (total / 2) + 1 

### HA Configurations:

**Master config**:

  Stop accepting writes if can't write to at least 1 replica after maximum 10 seconds of lag. Also, if a master failed the lag condition, its marked as unreachable (the master it self, not the replicas).

  Obviously, the client must have the timeout greater than the lag-time, or it the write will failed.

    min-replicas-to-write 1
    min-replicas-max-lag 10

**Setinal configs:**

    port 5000
    sentinel monitor mymaster 127.0.0.1 6379 2
    sentinel down-after-milliseconds mymaster 5000
    sentinel failover-timeout mymaster 60000
    sentinel parallel-syncs mymaster 1

### Failover process:

1. Sentinal vote for new master internally
2. Redis Client use SENTINAL command to periodically fetch metadata and update new master address.
  
### Test fail over:

Setup redis-client that print the current master every 1 second:

    kubectl run redis-client --restart='Never' --image redis --command -- sleep infinity
    
    kubectl exec --tty -i redis-client -- bash

    vim client-increment.sh
    
    #!/bin/bash
    export REDISCLI_AUTH=password
    while true
    do
        CURRENT_PRIMARY=$(redis-cli -h redis-sentinel -p 26379 SENTINEL get-master-addr-by-name mymaster)
        CURRENT_PRIMARY_HOST=$(echo $CURRENT_PRIMARY | cut -d' ' -f1 | head -n 1)
        echo "Current master's host: $CURRENT_PRIMARY_HOST"
        redis-cli -h ${CURRENT_PRIMARY_HOST} -p 6379 INCR mycounter
        sleep 1
    done

Kill the current master, the new master should be elected within a few seconds:

    kubectl delete pods <master_pod_name:redis-node-0>

---

### Storage:

Basically, Redis components need PVCs for WAL. Since it primarily store data all loaded to the memory, PVCs shoud not be more too distinct from the memory.

The storage are seperated and used independently for each node. They do not share the same storage.

