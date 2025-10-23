### REFS:

* [Prometheus Storage](https://prometheus.io/docs/prometheus/latest/storage/)

### Options:

1. Local Filesystem: can't be replicated
2. Remote Storage

### WAL & In memory storage:

- The current block for incoming samples is kept in memory. Write-ahead log files are stored in the wal directory in 128MB segments. Prometheus will retain a minimum of three write-ahead log files.

### Local Storage:

- Ingested samples are grouped into blocks of two hours. Each block is a directory, inside are: index file, metadata file, and the data (segment files).

### Remote Storage:

- (...For now, I want to use local storage) 

### Configurations:

1. Config WAL block size, and max num of blocks to kept inmemory at a time. 

2. Data retention: policy how to keep the data before delete them (size, or time)

3. Decide the disk size: `retention_time_seconds * ingested_samples_per_second * bytes_per_sample`. The intested samples per second is based on the scrape interval, and the number of timeseries.
