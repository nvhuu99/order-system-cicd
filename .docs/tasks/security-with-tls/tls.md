https://www.poodlescan.com/posts/ssl-https-tls-explained/

### Basics:

**Private key & Public key:** private key is used to encrypt the data that can only be decrypt by the public key. Therefore, parties will send their publickey to the others for secured communications. (In reverse, if the public-key is used to encrypt data, then only the private-key can decrypt it)

**Symmetric Encryption:** very fast, even large data. But only use 1 key for both encr/decr, and thus not very secure if the key is leak.

**Asymmetric Encryption:** consumes high compute resources, slow for large data. Use 2 keys (private, public). Suitable for symetric-key exchanges.

**Server Certificate:**

  ```
  +-----------------------------------------------------+
  | Server Certificate:                                 |
  +-----------------------------------------------------+
  | + Some informations                                 |
  | + The server's public key                           |                         
  | + The CA signature (signed by the CA private key)   |
  +-----------------------------------------------------+
  ```

---

### STEP 1 - CONNECTION ESTABLISH:

**Client Hello:** Client sends the options for which cipher suites to use, and which version of TLS are supported.

**Server Hello:** Server selects the options, and returns it. Then, server sends their certificate (the server.crt file).

### STEP 2 - CERTIFICATE VERIFICATION:

**Summary**: the client request the root CA of the server certificate to check the certificate. If the CA approve it, send back the hash-value of the certificate that got approve. Ofcourse, the hash-value must first be encrypted by the CA private key that can only be decr using the CA public that the client stores in the "trusted root store". 

### STEP 3 - KEY EXCHANGE:

The goal of this step is that `the client` must safely sends to the server the `symmetric-key` that they both will use for `Step 4 - Data Transmitting`. This key also called `session-key` or `ephemeral-key`.

**Steps:**

1. Server sends certificate (with its public key) to the client
2. Client generates a random session key (pre-master secret)
3. Client encrypts this session key with the server’s public key (Only the server can decrypt with private-key)
4. Client sends encrypted session key to server
5. Server decrypts it with its private key → both now share the session key
6. Both sides derive symmetric encryption keys from the session key

### STEP 4 - DATA TRANSMITTING:

**summary:** At this step, all data from-to server/client are encrypted by the session-key, and also decrypted by it. Since only the client, and server have the session-key, the data transmittion is secured.

---

### Example of self-sign certificate

```
# 1. Generate private key
openssl genrsa -out server.key 2048

# 2. Create CSR (Certificate Signing Request)
openssl req -new -key server.key -out server.csr

# 3. Self-sign cert (or send CSR to a CA like Let’s Encrypt)
openssl x509 -req -in server.csr -signkey server.key -out server.crt -days 365
```