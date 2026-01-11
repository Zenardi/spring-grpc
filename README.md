- [🚀 Spring Boot 4.0 gRPC Service (Java 25)](#-spring-boot-40-grpc-service-java-25)
  - [🛠 Tech Stack](#-tech-stack)
  - [⚡ gRPC vs. REST: Why this method?](#-grpc-vs-rest-why-this-method)
  - [📦 Getting Started](#-getting-started)
    - [Prerequisites](#prerequisites)
    - [1. Running Locally](#1-running-locally)
    - [2. Running with Docker](#2-running-with-docker)
    - [3. Running with Docker Compose](#3-running-with-docker-compose)
  - [🔐 Advanced: Mutual TLS (mTLS) Setup](#-advanced-mutual-tls-mtls-setup)
    - [Server Configuration (`application.yml`)](#server-configuration-applicationyml)
  - [🧪 Testing the Service](#-testing-the-service)
    - [Via grpcurl (CLI)](#via-grpcurl-cli)
    - [Via Postman](#via-postman)
  - [🏗 Dependencies Used](#-dependencies-used)


# 🚀 Spring Boot 4.0 gRPC Service (Java 25)

This repository contains a high-performance gRPC service scaffolded for 2026. It utilizes **Java 25 Virtual Threads** (Project Loom) and the **Official Spring gRPC** starter to deliver a scalable, "Zero Trust" microservice architecture.

## 🛠 Tech Stack

* **Java 25 (LTS):** Utilizing refined Virtual Threads for high-concurrency without thread-starvation.
* **Spring Boot 4.0:** The latest standard for native cloud integration.
* **Official Spring gRPC:** Integrated via `spring-grpc-spring-boot-starter`.
* **Multiplexing:** Configured to run both REST and gRPC over **Port 8080** using HTTP/2.

---

## ⚡ gRPC vs. REST: Why this method?

| Feature | gRPC (Modern) | REST / JSON (Legacy) |
| --- | --- | --- |
| **Payload** | **Binary (Protobuf)** - ~40% smaller | **Text (JSON)** - Bulky & Verbose |
| **Protocol** | **HTTP/2** (Multiplexed) | **HTTP/1.1** (Sequential) |
| **Data Contract** | **Strict (`.proto`)**. Self-documenting | **Loose**. Needs external Swagger/OpenAPI |
| **Efficiency** | Extremely low CPU/Memory overhead | High parsing overhead |

---

## 📦 Getting Started

### Prerequisites

* **JDK 25** (e.g., Eclipse Temurin).
* **Docker** & **Docker Compose**.
* **grpcurl** (for CLI testing).

### 1. Running Locally

1. **Generate Java Stubs:**
```bash
./mvnw clean compile

```


2. **Start the Application:**
```bash
./mvnw spring-boot:run

```



### 2. Running with Docker

The project uses a **multi-stage build** to separate the JDK (build-time) from the JRE (runtime) for security and size.

```bash
docker build -t grpc-service:latest .
docker run -p 8080:8080 grpc-service:latest

```

### 3. Running with Docker Compose

Launches the app, a PostgreSQL database, and Prometheus for observability.

```bash
docker compose up -d --build

```

---

## 🔐 Advanced: Mutual TLS (mTLS) Setup

For production, we use **mTLS** where both the client and server must present certificates. This is configured using **Spring SSL Bundles**.

### Server Configuration (`application.yml`)

```yaml
server:
  ssl:
    bundle: "server-bundle"
    client-auth: "need" # Forces client to provide a valid certificate

spring:
  ssl:
    bundle:
      jks:
        server-bundle:
          keystore:
            location: "classpath:server.p12"
            password: "changeit"
          truststore:
            location: "classpath:truststore.p12"

```

---

## 🧪 Testing the Service

Since gRPC and REST share **Port 8080**, ensure you use the `-plaintext` flag only for local development.

### Via grpcurl (CLI)

```bash
grpcurl -plaintext -d '{"name": "John Doe"}' localhost:8080 com.example.grpc.Greeter/SayHello

```

### Via Postman

1. New -> **gRPC Request**.
2. Enter `localhost:8080`.
3. Under "Service Definition", select **Server Reflection**.
4. Invoke `SayHello`.

---

## 🏗 Dependencies Used

* `spring-grpc-spring-boot-starter`: Native Spring integration.
* `grpc-services`: Enables Server Reflection and gRPC Health Checks.
* `protobuf-maven-plugin`: Compiles `.proto` files into Java classes during the Maven `generate-sources` phase.

