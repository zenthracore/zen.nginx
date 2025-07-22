<p align="center">
  <img src="https://raw.githubusercontent.com/zenthracore/zen.falcon/main/assets/zenthra.png" width="120" alt="ZENTHRA logo" />
</p>

<h1 align="center">zen.nginx</h1>
<p align="center">
  🧘 Alpine-based NGINX with OpenSSL 3.3.4 + Falcon post-quantum crypto + oqs-provider.<br>
  <strong>Silence is infrastructure.</strong>
</p>

---

## 🔐 What is this?

**zen.nginx** is a minimal Alpine Linux container featuring:

- NGINX **1.29.x+**
- OpenSSL **3.3.4** (built July 2025)
- Integrated [oqs-provider](https://github.com/open-quantum-safe/oqs-provider) for OpenSSL 3.x
- Full support for **Falcon512 / Falcon1024** post-quantum TLS certificates
- Ready for strict **mTLS** and Zero Trust edge deployments

---

## 🧰 Use cases

- 🛡️ Serve HTTPS endpoints with **Falcon-based certificates**
- 🧪 **Post-quantum mTLS** reverse proxy for microservices
- ⚙️ Drop-in edge for PQC-enabled zero-trust networks
- 📦 Hardened base for secure, containerized L7 gateways

---

## ⚙️ Quick start

### 🔧 1. Clone and build the image

```bash
git clone https://github.com/zenthracore/zen.nginx.git
cd zen.nginx

# Build the image locally
docker build -t zen.nginx .
```

## 🔐 2. Generate Falcon key and cert
```bash
# Generate Falcon1024 private key
openssl genpkey -algorithm falcon1024 -out server.key

# Generate CSR
openssl req -new -key server.key -out server.csr \
  -subj "/C=UA/ST=Kyiv/L=Kyiv/O=ZENTHRACORE/CN=api.example.com"

# Self-sign certificate
openssl x509 -req -in server.csr -signkey server.key -out server.crt -days 365
```
## 🧘 3. Run the container
```bash
docker run -it --rm \
-v $PWD/nginx.conf:/etc/nginx/nginx.conf:ro \
-v $PWD/server.crt:/etc/nginx/server.crt:ro \
-v $PWD/server.key:/etc/nginx/server.key:ro \
-p 443:443 \
zen.nginx
```
## 🔐 4. Example nginx.conf for Falcon TLS
```bash
events {}
http {
server {
listen 443 ssl;
server_name api.example.com;
ssl_certificate     /etc/nginx/server.crt;
ssl_certificate_key /etc/nginx/server.key;
ssl_protocols       TLSv1.3;
ssl_ciphers         TLS_AES_256_GCM_SHA384;
ssl_prefer_server_ciphers on;
ssl_ecdh_curve      X25519:prime256v1;

    # Optional: enable client certificate auth (mTLS)
    # ssl_verify_client   on;
    # ssl_client_certificate /etc/nginx/client-ca.crt;

    location / {
      return 200 'zen.nginx: post-quantum silence.';
    }
}
}
```
## 🧪 5. Health check
```bash
curl -k --ciphers TLS_AES_256_GCM_SHA384 https://localhost/
# or
openssl s_client -connect localhost:443 -tls1_3
```
## 📄 License
- NGINX: 2-clause BSD
- Falcon: MIT
- oqs-provider: Apache-2.0 + BSD-compatible
- OpenSSL 3.3.4: Apache 2.0
- Everything in this repo: MIT

## 🧘 About ZENTHRACORE
Silence is infrastructure.<br>
ZENTHRACORE is a minimal, security-first, post-quantum stack for zero-trust, adversarial environments.
zen.nginx is its L7 edge.

## 📫 Contact
- 📧 Email: zenthracore@proton.me
- ⚡ Damus (Nostr): npub1msz8ejzagf5z760c74d6svrpmqcd3nq6ffhrq7akesege9dq748sjlul4s