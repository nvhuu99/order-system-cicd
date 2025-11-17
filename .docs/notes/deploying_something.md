### Cheatsheet:

What should you consider when deploying a technology?

- Looks for helm charts: production ready? abandoned or moved? community or org-supported?
- Pay attention to app version, chart version
- Understand the infrastructure: 
    - What are the components?
    - How are they communicating? (the read/write pattern of the components)
- Resource requests and limits: don't guess, monitor and adjust
- Storage type:  
- Production environment:
    - use immutable image tags
    - securing traffic: tls, or mtls
    - network policies: default to deny
- Pod/Topology affinity:
    * first, just deploy without affinity. Then describe the pod to see the preset, and change if needed
    - nodes, availability zones affinity
    - pod affinity: replications should be on separate nodes using anti affinity
- Collect metrics: usually Prometheus sidecar 
- Deployment modes: standalone or cluster
- Replication models?
- Update mode: usually RollingUpdate
- Data backup and restoring: somes just need to keep the PVC, some must export data

### Troubleshooting:

- Pod crash:
    + which container crashed: check containers last exist reason