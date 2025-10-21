Sure! Below is a complete example of how to create and use a **self-signed certificate** for a **gRPC over TLS (gRPCS)** server using **Spring Boot**. I’ll also include how to test it from a gRPC client. You can adapt the same certificate for Nginx if needed.

---

# 🔐 Step 1 – Generate a Self-Signed Certificate

Run the following command to create:

* `server.key` → Private Key
* `server.crt` → Self-signed Certificate
* `server.p12` → PKCS12 keystore for Spring Boot

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

Resulting files:

```
server.key   ← Private key
server.crt   ← Certificate
server.p12   ← Keystore for Spring Boot
```

---

# 🚀 Step 2 – Spring Boot Configuration (gRPC Server over TLS)

```yaml
# application.yml
grpc:
  server:
    port: 9090
    security:
      enabled: true
      certificate-chain: classpath:server.crt
      private-key: classpath:server.key

server:
  ssl:
    enabled: true
    key-store: classpath:server.p12
    key-store-password: changeit
    key-store-type: PKCS12
```

---

# 🛠 Step 3 – Example gRPC Service (Java)

```java
@GrpcService
public class HelloService extends HelloServiceGrpc.HelloServiceImplBase {
    @Override
    public void sayHello(HelloRequest request, StreamObserver<HelloReply> responseObserver) {
        HelloReply reply = HelloReply.newBuilder()
            .setMessage("Hello " + request.getName())
            .build();
        responseObserver.onNext(reply);
        responseObserver.onCompleted();
    }
}
```

---

# 🧪 Step 4 – gRPC Client Test (Trusting Self-Signed Cert)

```java
ManagedChannel channel = NettyChannelBuilder.forAddress("localhost", 9090)
        .sslContext(GrpcSslContexts.forClient()
            .trustManager(new File("server.crt"))  // Self-signed trust
            .build())
        .build();

HelloServiceGrpc.HelloServiceBlockingStub stub =
        HelloServiceGrpc.newBlockingStub(channel);

HelloReply response = stub.sayHello(
        HelloRequest.newBuilder().setName("Test").build()
);

System.out.println(response.getMessage());
channel.shutdown();
```

---

# 🧰 Bonus: Use Certificate with Nginx (Reverse Proxy)

```nginx
server {
    listen 443 ssl http2;
    server_name localhost;

    ssl_certificate     /etc/nginx/certs/server.crt;
    ssl_certificate_key /etc/nginx/certs/server.key;

    location / {
        grpc_pass grpcs://127.0.0.1:9090;
    }
}
```

---

# ⚠ Important Notes

| Environment         | Should You Use Self-Signed?                |
| ------------------- | ------------------------------------------ |
| Local Dev / Testing | ✅ Yes                                      |
| Internal Network    | ⚠ Maybe                                    |
| Production          | ❌ No (use Let’s Encrypt or CA certificate) |

---

Would you like a **ready-to-run Gradle/Maven Spring Boot gRPCS starter project**? I can generate the entire structure for you in one message. Just say:
👉 *“Generate a gRPC TLS Spring Boot project”*
