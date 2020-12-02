---
title: "go 1.15+ GRPC 自签CA"
date: 2020-11-26T13:45:33+08:00
cover: /images/default3.jpg

tags: [
	"go1.15+",
    "grpc",
    "CA",
    "openssl"
]

categories: [
    "Go",
]
---

x509: certificate relies on legacy Common Name field, use SANs or...
<!--more-->

```
2020/11/26 11:02:58 client.Search err: rpc error: code = Unavailable desc = connection error: 
 desc = "transport: authentication handshake failed: x509: certificate relies on legacy Common Name field,
  use SANs or temporarily enable Common Name matching with GODEBUG=x509ignoreCN=0"
```

# CA

- openssl genrsa -out ca.key 2048
- openssl req -new -x509 -days 7200 -key ca.key -out ca.pem
```
Country Name (2 letter code) [AU]:cn
State or Province Name (full name) []:
Locality Name (eg, city) []:
Organization Name (eg, company) []:
Organizational Unit Name (eg, section) []:
Common Name (eg, fully qualified host name) []:go-grpc-example
Email Address []:
```

# OpenSSL config
- cp ***/openssl.cnf ***/go-grpc-example/conf/openssl.cnf
- 修改 openssl.cnf [ CA_default ] copy_extensions = copy
- 修改 openssl.cnf [ req ] req_extensions = v3_req # The extensions to add to a certificate request
- 修改 openssl.cnf [ v3_req ] subjectAltName = @alt_names
- 修改 openssl.cnf
```
[ alt_names ]
DNS.1 = go-grpc-example
```

# Server
- openssl genpkey -algorithm RSA -out server.key
- openssl req -new -nodes -key server.key -out server.csr -days 3650 -subj "/C=cn/CN=go-grpc-example" -config ./openssl.cnf -extensions v3_req
- openssl x509 -req -days 3650 -in server.csr -out server.pem -CA ca.pem -CAkey ca.key -CAcreateserial -extfile  ./openssl.cnf -extensions v3_req

# Client
- openssl genpkey -algorithm RSA -out client.key
- openssl req -new -nodes -key client.key -out client.csr -days 3650 -subj "/C=cn/CN=go-grpc-example" -config ./openssl.cnf -extensions v3_req
- openssl x509 -req -days 3650 -in client.csr -out client.pem -CA ca.pem -CAkey ca.key -CAcreateserial -extfile  ./openssl.cnf -extensions v3_req


## 膜拜大佬
- [Go 1.15 以上版本的 GRPC 通信，使用自签CA、Server、Client证书和双向认证](https://segmentfault.com/a/1190000038212054)