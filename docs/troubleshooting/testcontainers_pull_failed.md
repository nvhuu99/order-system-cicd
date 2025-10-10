### Testcontainers failed to pull docker image:

    When running a Jenkins built with k8s pod agent, and you're using DinD (Docker in Docker). It might be timeout to pull an image for some reason (networks, disk I/O, ...).
    
### Solution:


    1. Cache the by creating a PVC and mount to /var/lib/docker to the DinD container.
    2. Pre-pull all the images instead of pulling when the build runs.

### Example setup:

    Create PVC & Pod with these template:

        ```
        apiVersion: v1
        kind: PersistentVolume
        metadata:
        name: dind-cache
        namespace: integration
        labels:
            cache-for: dind
        spec:
        capacity:
            storage: 8Gi
        accessModes:
            - ReadWriteOnce
        storageClassName: standard
        local:
            path: /mnt/data/dind-cache
        nodeAffinity:
            required:
            nodeSelectorTerms:
                - matchExpressions:
                    - key: kubernetes.io/hostname
                    operator: In
                    values:
                        - kind-worker

        ---

        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
        name: dind-cache
        namespace: integration
        spec:
        accessModes:
            - ReadWriteOnce
        storageClassName: standard
        resources:
            requests:
            storage: 8Gi
        selector:
            matchLabels:
            cache-for: dind
        
        ---

        apiVersion: v1
        kind: Pod
        metadata:
        name: dind
        spec:

        affinity:
            nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                - key: kubernetes.io/hostname
                    operator: In
                    values:
                    - kind-worker

        volumes:
        - name: maven-cache
            persistentVolumeClaim:
            claimName: maven-cache
        - name: dind-cache
            persistentVolumeClaim:
            claimName: dind-cache

        containers:
        - name: dind
            image: docker:dind
            resources:
            requests: { cpu: "50m", memory: "128Mi" }
            limits: { cpu: "100m", memory: "256Mi" }
            env:
            - name: DOCKER_TLS_CERTDIR
                value: ""
            command: ["dockerd-entrypoint.sh"]
            args: ["--host=tcp://0.0.0.0:2375"]
            volumeMounts:
            - name: dind-cache
                mountPath: /var/lib/docker
            securityContext:
            privileged: true
            runAsUser: 0
        ```

    Pre-pull images:

        kubectl exec -it dind -- sh
            docker pull <repo>:<tag>
    
    After pre-pull, your built will be able to run without pulling images, and avoid timeout issue.
     