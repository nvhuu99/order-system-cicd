# TLS handshake — files usage and where each generated file is used

This diagram and notes show the **TLS (server-side) handshake** flow and **exactly when/where** the files created by your OpenSSL commands are used:

```bash
# 1️⃣ Generate private key and certificate
openssl req -x509 -newkey rsa:2048 -keyout server.key -out server.crt \
  -days 365 -nodes -subj "/CN=localhost"

# 2️⃣ Create PKCS12 keystore (Spring Boot compatible)
openssl pkcs12 -export \
  -in server.crt \
  -inkey server.key \
  -out server.p12 \
  -name grpc-server \
  -passout pass:changeit
```

---

## 1) Visual flow (simplified)

```
[Generate files]                  [Server process]                      [Client]
   server.key   ←─────────┐         (e.g., Spring Boot / Nginx)         
   server.crt   ←─────────┼─────────starts with server key/cert─────────>
   server.p12   ←─────────┘                                             
                                                                         
1. Client ---> Server : ClientHello (supported ciphers, random)
                              |
2. Server ---> Client : ServerHello (chosen cipher)
                       + Certificate (server.crt)  <-- server.crt sent here
                       + (ServerKeyExchange / Signed data)  <-- signed using server.key
                              |
3. Client verifies server.crt against its truststore
       - If server.crt is self-signed, client must have server.crt in truststore or fail
                              |
4. Client ---> Server : (Encrypted PreMasterSecret)  <-- encrypted using server public key
                              |
5. Server uses private key (server.key) to decrypt and both derive session keys
                              |
6. TLS handshake complete -> Encrypted Application Data (gRPC over HTTP/2)

Notes:
- server.key is *never* sent. It's used to perform signatures and decryption.
- server.crt is advertised to clients so they can authenticate the server.
- server.p12 is a container (keystore) including both server.key and server.crt for Java apps.
```

---

## 2) Where each file is used (mapping)

* **`server.key`**

  * Role: Private key used by the server to sign handshake messages and to decrypt the client's key exchange information.
  * Used at runtime by: **Server TLS stack** (Nginx, Spring Boot, gRPC server, Envoy). Never given to clients.
  * Example usage: `ssl_certificate_key /etc/nginx/certs/server.key;`

* **`server.crt`**

  * Role: Public certificate (contains server public key and identity information). Sent to client during the TLS handshake in the `Certificate` message.
  * Used at runtime by: Server TLS stack to present to clients; can also be used by clients as a *trust anchor* if self-signed.
  * Example usage: `ssl_certificate /etc/nginx/certs/server.crt;`

* **`server.p12`**

  * Role: PKCS#12 keystore bundling the private key and certificate in a single file with a password. Convenient for Java (Spring Boot) and other JVM apps.
  * Used at runtime by: Java servers (Spring Boot) as `server.ssl.key-store`.
  * Example usage in `application.yml`:

```yaml
server:
  ssl:
    enabled: true
    key-store: classpath:server.p12
    key-store-password: changeit
    key-store-type: PKCS12
```

---

## 3) Practical examples — where to mount/use the files

### A) Spring Boot (Java) — use `server.p12`

* Put `server.p12` on the classpath or mount into the container and point `server.ssl.key-store` to it.
* Spring Boot will load the private key and certificate from the PKCS12 and operate HTTPS (or gRPCS if your server supports it).

### B) Nginx — use `server.crt` + `server.key`

```nginx
server {
  listen 443 ssl http2;
  server_name my-grpc.example.com;

  ssl_certificate /etc/nginx/certs/server.crt;
  ssl_certificate_key /etc/nginx/certs/server.key;

  location / {
    grpc_pass grpc://backend:50051;  # or grpcs://backend:50051 for re-encrypt
  }
}
```

### C) gRPC client (trusting a self-signed cert)

* Client must be configured to trust `server.crt` (or the CA that signed it). For a self-signed cert you usually add `server.crt` to the client's trust store or pass it as a trust manager.

Example (Netty-based Java client):

```java
ManagedChannel channel = NettyChannelBuilder.forAddress("localhost", 9090)
  .sslContext(GrpcSslContexts.forClient().trustManager(new File("server.crt")).build())
  .build();
```

---

## 4) Important notes & failure modes

* **If client does NOT trust `server.crt`:** TLS handshake fails at client verification step → connection refused before any HTTP/gRPC data.
* **`server.key` must be kept secret.** If leaked, attacker can impersonate the server.
* **`server.p12` contains the private key;** protect it with a strong password and limited access.
* **In Kubernetes:** you typically store certs in `kubernetes.io/tls` Secrets (for `server.crt` + `server.key`) or as generic secrets (for `server.p12`) and mount into pods.

Example k8s secret (TLS):

```bash
kubectl create secret tls my-grpc-tls --cert=server.crt --key=server.key -n myns
```

Example k8s secret (PKCS12):

```bash
kubectl create secret generic my-grpc-keystore --from-file=server.p12 -n myns
```

---

## 5) Quick checklist for deploying this in k8s

* Decide where TLS terminates (Ingress vs backend service).
* If terminating at backend, mount `server.p12` (Spring) or `server.key`+`server.crt` (Nginx) into pods.
* If using self-signed certs, ensure clients (or sidecars) trust `server.crt`.
* Use `cert-manager` / CA for automation in production rather than manually generated self-signed certs.

---

If you want, I can:

* produce a **PNG/SVG export** of this diagram,
* or produce a **small Kubernetes manifest** that mounts the `server.p12` into a Spring Boot Deployment and exposes it via Service/Ingress.

Which one would you like next?


---
---
---

### DeekSeek:

https://mermaid.live/edit

```
flowchart TD
    subgraph A [Step 1: Generate Certificate & Private Key]
        A1[openssl req command] --> A2[Generates server.key<br>PRIVATE KEY]
        A1 --> A3[Generates server.crt<br>CERTIFICATE]
        
        A2 --> A4[Used by:<br>- Server TLS termination<br>- Nginx]
        A3 --> A5[Used by:<br>- Clients for verification<br>- Trust store]
    end

    subgraph B [Step 2: Create PKCS12 Keystore]
        B1[openssl pkcs12 command] --> B2[Generates server.p12<br>KEYSTORE FILE]
        
        A2 -.-> |input| B1
        A3 -.-> |input| B1
        
        B2 --> B3[Used by:<br>- Spring Boot applications<br>- Java clients/servers]
    end

    subgraph C [TLS Handshake Process]
        C1[Client connects] --> C2[Server presents server.crt]
        C2 --> C3[Client verifies with<br>trusted certificates]
        C3 --> C4[Secure gRPC connection<br>established]
    end

    A4 -.-> C2
    A5 -.-> C3
```

### ChatGPT:

https://mermaid.live/edit

```
sequenceDiagram
    participant Client
    participant Server
    participant Files

    Client->>Server: ClientHello (ciphers, random)
    Server->>Files: Load certificate & private key (server.p12 OR server.crt + server.key)
    Files-->>Server: Provide server.crt (public) and server.key for signing/decryption
    Server->>Client: ServerHello + Certificate (server.crt) + Signature (signed with server.key)
    Client->>Client: Verify server.crt against truststore (must trust self-signed cert)
    Client->>Server: EncryptedPreMasterSecret (encrypted using server public key from server.crt)
    Server->>Files: Use server.key to decrypt PreMasterSecret
    Server-->>Client: Finished (both derive session keys)
    Client->>Server: Encrypted Application Data (gRPC over HTTP/2)
```