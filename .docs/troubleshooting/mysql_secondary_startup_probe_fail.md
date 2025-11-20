 kubectl describe pod/mysql-secondary-0
Name:             mysql-secondary-0
Namespace:        infras
Priority:         0
Service Account:  mysql
Node:             dev-worker3/172.19.0.4
Start Time:       Mon, 17 Nov 2025 21:04:02 +0700
Labels:           app.kubernetes.io/component=secondary
                  app.kubernetes.io/instance=mysql
                  app.kubernetes.io/managed-by=Helm
                  app.kubernetes.io/name=mysql
                  app.kubernetes.io/part-of=mysql
                  app.kubernetes.io/version=9.4.0
                  apps.kubernetes.io/pod-index=0
                  controller-revision-hash=mysql-secondary-77f7dff6b6
                  helm.sh/chart=mysql-14.0.3
                  statefulset.kubernetes.io/pod-name=mysql-secondary-0
Annotations:      checksum/configuration: bd5f57284a9494060d1116c46635162b1fca67054feff04305e4398ab0fffcd8
Status:           Running
IP:               10.244.2.6
IPs:
  IP:           10.244.2.6
Controlled By:  StatefulSet/mysql-secondary
Init Containers:
  preserve-logs-symlinks:
    Container ID:    containerd://368b2a959ed4dff5d4bb9bc24cf0e64489009a4f00a32282cb04b77d1121d73a
    Image:           docker.io/bitnamilegacy/mysql:9.4.0-debian-12-r1
    Image ID:        docker.io/bitnamilegacy/mysql@sha256:ec13e229247a737f7149b7f255d8f2d9c72da861f8bf263b22091bf131540da3
    Port:            <none>
    Host Port:       <none>
    SeccompProfile:  RuntimeDefault
    Command:
      /bin/bash
    Args:
      -ec
      #!/bin/bash

      . /opt/bitnami/scripts/libfs.sh
      # We copy the logs folder because it has symlinks to stdout and stderr
      if ! is_dir_empty /opt/bitnami/mysql/logs; then
        cp -r /opt/bitnami/mysql/logs /emptydir/app-logs-dir
      fi

    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Tue, 18 Nov 2025 08:58:02 +0700
      Finished:     Tue, 18 Nov 2025 08:58:02 +0700
    Ready:          True
    Restart Count:  1
    Limits:
      cpu:     100m
      memory:  1Gi
    Requests:
      cpu:        100m
      memory:     1Gi
    Environment:  <none>
    Mounts:
      /emptydir from empty-dir (rw)
Containers:
  mysql:
    Container ID:    containerd://55443d011f05a4a9873a7be79a32d5fe8d6f8fd0e974b8b59e362bc11c4618aa
    Image:           docker.io/bitnamilegacy/mysql:9.4.0-debian-12-r1
    Image ID:        docker.io/bitnamilegacy/mysql@sha256:ec13e229247a737f7149b7f255d8f2d9c72da861f8bf263b22091bf131540da3
    Port:            3306/TCP
    Host Port:       0/TCP
    SeccompProfile:  RuntimeDefault
    State:           Running
      Started:       Tue, 18 Nov 2025 10:03:24 +0700
    Last State:      Terminated
      Reason:        Completed
      Exit Code:     0
      Started:       Tue, 18 Nov 2025 10:00:43 +0700
      Finished:      Tue, 18 Nov 2025 10:03:23 +0700
    Ready:           False
    Restart Count:   24
    Limits:
      cpu:     100m
      memory:  1Gi
    Requests:
      cpu:     100m
      memory:  1Gi
    Liveness:  exec [/bin/bash -ec password_aux="${MYSQL_MASTER_ROOT_PASSWORD:-}"
if [[ -f "${MYSQL_MASTER_ROOT_PASSWORD_FILE:-}" ]]; then
    password_aux=$(cat "$MYSQL_MASTER_ROOT_PASSWORD_FILE")
fi
mysqladmin status -uroot -p"${password_aux}"
] delay=5s timeout=1s period=10s #success=1 #failure=3
    Readiness:  exec [/bin/bash -ec password_aux="${MYSQL_MASTER_ROOT_PASSWORD:-}"
if [[ -f "${MYSQL_MASTER_ROOT_PASSWORD_FILE:-}" ]]; then
    password_aux=$(cat "$MYSQL_MASTER_ROOT_PASSWORD_FILE")
fi
mysqladmin ping -uroot -p"${password_aux}" | grep "mysqld is alive"
] delay=5s timeout=1s period=10s #success=1 #failure=3
    Startup:  exec [/bin/bash -ec password_aux="${MYSQL_MASTER_ROOT_PASSWORD:-}"
if [[ -f "${MYSQL_MASTER_ROOT_PASSWORD_FILE:-}" ]]; then
    password_aux=$(cat "$MYSQL_MASTER_ROOT_PASSWORD_FILE")
fi
mysqladmin ping -uroot -p"${password_aux}" | grep "mysqld is alive"
] delay=15s timeout=1s period=10s #success=1 #failure=15
    Environment:
      BITNAMI_DEBUG:                    false
      MYSQL_REPLICATION_MODE:           slave
      MYSQL_MASTER_HOST:                mysql-primary
      MYSQL_MASTER_PORT_NUMBER:         3306
      MYSQL_MASTER_ROOT_USER:           root
      MYSQL_PORT:                       3306
      MYSQL_REPLICATION_USER:           replicator
      MYSQL_ENABLE_SSL:                 no
      MYSQL_MASTER_ROOT_PASSWORD_FILE:  /opt/bitnami/mysql/secrets/mysql-root-password
      MYSQL_REPLICATION_PASSWORD_FILE:  /opt/bitnami/mysql/secrets/mysql-replication-password
    Mounts:
      /bitnami/mysql from data (rw)
      /opt/bitnami/mysql/conf from empty-dir (rw,path="app-conf-dir")
      /opt/bitnami/mysql/conf/my.cnf from config (rw,path="my.cnf")
      /opt/bitnami/mysql/logs from empty-dir (rw,path="app-logs-dir")
      /opt/bitnami/mysql/secrets/ from mysql-credentials (rw)
      /opt/bitnami/mysql/tmp from empty-dir (rw,path="app-tmp-dir")
      /tmp from empty-dir (rw,path="tmp-dir")
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       False
  ContainersReady             False
  PodScheduled                True
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  data-mysql-secondary-0
    ReadOnly:   false
  config:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      mysql-secondary
    Optional:  false
  mysql-credentials:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  mysql-secrets
    Optional:    false
  empty-dir:
    Type:        EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:   <unset>
QoS Class:       Guaranteed
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                  From     Message
  ----     ------     ----                 ----     -------
  Normal   Created    53m (x6 over 66m)    kubelet  Created container: mysql
  Normal   Started    53m (x6 over 66m)    kubelet  Started container mysql
  Warning  BackOff    16m (x79 over 50m)   kubelet  Back-off restarting failed container mysql in pod mysql-secondary-0_infras(d4d51205-9dc0-40a9-ab87-2234ee6c8edb)
  Normal   Pulled     11m (x14 over 66m)   kubelet  Container image "docker.io/bitnamilegacy/mysql:9.4.0-debian-12-r1" already present on machine
  Normal   Killing    70s (x17 over 63m)   kubelet  Container mysql failed startup probe, will be restarted
  Warning  Unhealthy  50s (x255 over 66m)  kubelet  Startup probe failed: mysqladmin: [Warning] Using a password on the command line interface can be insecure.
mysqladmin: connect to server at 'localhost' failed
error: 'Access denied for user 'root'@'localhost' (using password: YES)'


$ klogs mysql-secondary-0
Defaulted container "mysql" out of: mysql, preserve-logs-symlinks (init)
mysql 03:18:06.21 INFO  ==> 
mysql 03:18:06.21 INFO  ==> Welcome to the Bitnami mysql container
mysql 03:18:06.21 INFO  ==> Subscribe to project updates by watching https://github.com/bitnami/containers
mysql 03:18:06.21 INFO  ==> NOTICE: Starting August 28th, 2025, only a limited subset of images/charts will remain available for free. Backup will be available for some time at the 'Bitnami Legacy' repository. More info at https://github.com/bitnami/containers/issues/83267
mysql 03:18:06.22 INFO  ==>
mysql 03:18:06.31 INFO  ==> ** Starting MySQL setup **
mysql 03:18:06.42 INFO  ==> Validating settings in MYSQL_*/MARIADB_* env vars
mysql 03:18:06.51 INFO  ==> Initializing mysql database
mysql 03:18:06.61 WARN  ==> The mysql configuration file '/opt/bitnami/mysql/conf/my.cnf' is not writable. Configurations based on environment variables will not be applied for this file.
mysql 03:18:06.61 INFO  ==> Using persisted data
mysql 03:18:06.62 INFO  ==> Running mysql_upgrade
mysql 03:18:06.81 INFO  ==> Starting mysql in background
2025-11-18T03:18:06.917237Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2025-11-18T03:18:08.319560Z 0 [System] [MY-010116] [Server] /opt/bitnami/mysql/bin/mysqld (mysqld 9.4.0) starting as process 45
2025-11-18T03:18:08.319581Z 0 [System] [MY-015590] [Server] MySQL Server has access to 8 logical CPUs.
2025-11-18T03:18:08.319592Z 0 [System] [MY-015590] [Server] MySQL Server has access to 1073741824 bytes of physical memory.
2025-11-18T03:18:08.321803Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2025-11-18T03:18:08.414003Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2025-11-18T03:18:09.762821Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2025-11-18T03:18:11.012388Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2025-11-18T03:18:11.012468Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2025-11-18T03:18:11.087111Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/opt/bitnami/mysql/tmp' in the path is accessible to all OS users. Consider choosing a different directory.
2025-11-18T03:18:11.214838Z 0 [System] [MY-010931] [Server] /opt/bitnami/mysql/bin/mysqld: ready for connections. Version: '9.4.0'  socket: '/opt/bitnami/mysql/tmp/mysql.sock'  port: 3306  Source distribution.
find: '/docker-entrypoint-startdb.d/': No such file or directory
mysql 03:18:12.82 INFO  ==> Stopping mysql
2025-11-18T03:18:12.829513Z 0 [System] [MY-013172] [Server] Received SHUTDOWN from user <via user signal>. Shutting down mysqld (Version: 9.4.0).
2025-11-18T03:18:14.016319Z 0 [System] [MY-010910] [Server] /opt/bitnami/mysql/bin/mysqld: Shutdown complete (mysqld 9.4.0)  Source distribution.
2025-11-18T03:18:14.016357Z 0 [System] [MY-015016] [Server] MySQL Server - end.
mysql 03:18:14.83 INFO  ==> ** MySQL setup finished! **

mysql 03:18:15.11 INFO  ==> ** Starting MySQL **
2025-11-18T03:18:15.217613Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2025-11-18T03:18:16.513811Z 0 [System] [MY-010116] [Server] /opt/bitnami/mysql/bin/mysqld (mysqld 9.4.0) starting as process 1
2025-11-18T03:18:16.513830Z 0 [System] [MY-015590] [Server] MySQL Server has access to 8 logical CPUs.
2025-11-18T03:18:16.513841Z 0 [System] [MY-015590] [Server] MySQL Server has access to 1073741824 bytes of physical memory.
2025-11-18T03:18:16.515892Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2025-11-18T03:18:16.521017Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2025-11-18T03:18:17.914670Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2025-11-18T03:18:19.024181Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2025-11-18T03:18:19.024234Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2025-11-18T03:18:19.027877Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/opt/bitnami/mysql/tmp' in the path is accessible to all OS users. Consider choosing a different directory.
2025-11-18T03:18:19.215155Z 0 [System] [MY-010931] [Server] /opt/bitnami/mysql/bin/mysqld: ready for connections. Version: '9.4.0'  socket: '/opt/bitnami/mysql/tmp/mysql.sock'  port: 3306  Source distribution.
2025-11-18T03:18:24.211125Z 9 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:18:34.212026Z 10 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:18:44.210212Z 11 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:18:54.208660Z 12 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:19:04.211011Z 13 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:19:14.212175Z 14 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:19:24.208077Z 15 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:19:34.309847Z 16 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:19:44.206049Z 17 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:19:54.280991Z 18 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:20:04.304271Z 19 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:20:14.207054Z 20 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:20:24.306425Z 21 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:20:34.204416Z 22 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:20:44.280395Z 23 [Warning] [MY-013360] [Server] Plugin sha256_password reported: ''sha256_password' is deprecated and will be removed in a future release. Please use caching_sha2_password instead'
2025-11-18T03:20:44.318439Z 0 [System] [MY-013172] [Server] Received SHUTDOWN from user <via user signal>. Shutting down mysqld (Version: 9.4.0).
2025-11-18T03:20:45.106366Z 0 [System] [MY-010910] [Server] /opt/bitnami/mysql/bin/mysqld: Shutdown complete (mysqld 9.4.0)  Source distribution.
2025-11-18T03:20:45.106550Z 0 [System] [MY-015016] [Server] MySQL Server - end.



https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_default_authentication_plugin


### DEBUG:

- container chạy từ 03:18:15 -> 03:20:44 ~ 149 seconds, và fail với lỗi startupProbe là "Access denied"
- log của container có ghi lỗi liên quan đến auth plugin
=> do dùng plugin "sha256_password" thay vì "caching_sha2_password", nên bị  "Access denied", dẫn đến fail startupProbe, dẫn đến kubelet restart container.

- nhưng mặc định mysql 9.4 đã là "caching_sha2_password", và mình ko set plugin "sha256_password".
  nhưng tại sao nó lại dùng "sha256_password" => ko rõ lí do

- cần thử set values.auth.authenticationPolicy = "caching_sha2_password,," để loại bỏ lỗi "Access denied"
- nhưng trước tiên phải tái hiện được lỗi này, bằng cách giư nguyên configurations và complete-reinstall
=> Không tái hiện được lỗi plugin "sha256_password" khi tạo lại cluster, hoạt động bình thường

..

- Một lần khác lỗi, bị "Access denied" mà không có lỗi plugin "sha256_password"
=> Thử tăng startupProbe timeout từ 10s -> 120s, sau đó hoạt động bình thường