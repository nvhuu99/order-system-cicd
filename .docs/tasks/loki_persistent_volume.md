### Refs

* [Loki Architecture](https://grafana.com/docs/loki/latest/get-started/architecture/)

### Write path:

> First, all hash entries labels, we got StreamID. Log entries with the same StreamID will be indexed together. Indexes will be store in "chunks-format". And logs entries are store in block-format. The data will be compressed before storing.

> Loki stores all data in a single object storage backend, such as Amazon Simple Storage Service (S3), Google Cloud Storage (GCS), Azure Blob Storage, among others. This mode uses an adapter called index shipper (or short shipper) to store index (TSDB or BoltDB) files the same way we store chunk files in object storage.

> Index shipper: This component basically an optimizer for "Object storage". Object storages are high-throughput (tranfer data very fast), but bad for list, random-read, and objects are immutable. The index shipper basically keeping metadata files to manage the objects without having to perform too many operations.

> There are two index formats: TSDB (recommended), BoltDB (deprecated).

### Read path:

1. The querier passes the query to all ingesters for *in-memory* data.
2. The querier lazily loads data *from the backing store* and runs the query against it if ingesters returned no or insufficient data.

### Replication:

- Loki use Memberlist (created by HashiCorp) to keep track of nodes.
- Loki replicates data by having distributors send each log stream to multiple ingesters, based on a configurable replication factor. The ingesters process the data, and if a write to storage is successful on a quorum of instances.

### Configuration:

- **Ingester:** need PVC for WAL.
- **Distributpr:** no need PVC.
- **Querier:** optional PVC for queries cache. 
- **Querier frontend:** no need PVC. 
- **Query scheduler:** no need PVC. 
- **Compactor:** need PVC for WAL and downloaded objects.