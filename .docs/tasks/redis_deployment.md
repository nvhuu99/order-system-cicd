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
  
### Test fail over:

Make the master unavailable for 30s:

    redis-cli -p 6379 DEBUG sleep 30

Observe the vote and fail over process:

    kubectl logs <pod> -c <sentinal> -f -n infras

---

### Storage:

Basically, Redis components need PVCs for WAL. Since it primarily store data all loaded to the memory, PVCs shoud not be more too distinct from the memory.

