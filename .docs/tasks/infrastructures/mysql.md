### Refs:

- [Bitnami MySQL Helm Chart](https://github.com/bitnami/charts/tree/main/bitnami/mysql)

### Chart features:

- Modes: `standalone` or `replication`
- Support many K8s features: `poddisruptionbudget`, `networkPolicy`
- Password update: config the `passwordUpdateJob` and then running `helm upgrade`

### Deploying:

#### Replication Mode:

- Master (or Primary)

#### Notes:

- Pod Affinity: by default, no preset 

### Backup & restore:

- Periodically dump data
