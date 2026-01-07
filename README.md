# Kafka Spring Boot – Dummy Producer & Consumer Microservices (Kafka 4.x.x)

## 📌 Project Overview

This repository contains a **fundamental Kafka learning project** built using **Spring Boot** and **Apache Kafka 4.x.x (KRaft mode)**.

The primary goal of this project is to understand **end-to-end Kafka implementation** by building:

* A **Kafka Producer microservice**
* A **Kafka Consumer microservice**
* Local **Kafka 4.x.x single-node setup (KRaft, no Zookeeper)**
* Topic creation, message publishing, and consumption
* Centralized logging using **Log4j2**

This project is intentionally designed as a **dummy / foundational project** to focus on **Kafka core concepts and Spring Boot integration**, not business complexity.

---

## 🎯 Learning Objectives

By working through this project, you will learn:

* Kafka 4.x.x architecture and **KRaft mode** basics
* How Kafka works internally (topics, partitions, brokers, controllers)
* How to configure Kafka 4.x.x locally on macOS
* How Spring Boot integrates with Kafka
* How to publish messages using `KafkaTemplate`
* How to consume messages using `@KafkaListener`
* Asynchronous communication between microservices
* Centralized logging using Log4j2

---

## 🧩 High-Level Architecture

```
Client (REST API)
        │
        ▼
Kafka Producer Service (Spring Boot)
        │
        ▼
   Kafka Topic (demo-topic)
        │
        ▼
Kafka Consumer Service (Spring Boot)
        │
        ▼
     Logs / Console Output
```

---

## 🛠️ Tech Stack

* **Java 17+**
* **Spring Boot**
* **Spring Kafka**
* **Apache Kafka 4.1.x (KRaft Mode)**
* **Log4j2**
* **Maven**
* **macOS (local setup)**

---

## 📁 Logical Project Structure

```
producer-service
 ├── controller
 │    └── KafkaController.java
 ├── service
 │    └── KafkaProducerService.java
 ├── resources
 │    ├── application.yaml
 │    └── log4j2.xml

consumer-service
 ├── service
 │    └── KafkaConsumerService.java
 ├── resources
 │    ├── application.yaml
 │    └── log4j2.xml
```

---

## 🔹 Kafka Producer Service

### KafkaController

* Exposes REST APIs to publish messages
* Acts as an entry point for clients

### KafkaProducerService

* Uses `KafkaTemplate<String, String>`
* Publishes messages to Kafka topic (`demo-topic`)
* Handles producer-side logging

---

## 🔹 Kafka Consumer Service

### KafkaConsumerService

* Uses `@KafkaListener`
* Subscribes to `demo-topic`
* Consumes messages asynchronously
* Logs consumed messages using Log4j2

---

## ⚙️ Kafka 4.1.x Local Setup (macOS – KRaft Mode)

> Kafka 4.x.x runs **without Zookeeper** and uses **KRaft (Kafka Raft Metadata mode)**.

### Step 1️⃣ Download Kafka

* Download Kafka **4.1.x** from the Apache Kafka website
* Extract it locally

---

### Step 2️⃣ Configure `server.properties`

Navigate to the `config` folder and ensure the following properties exist in `server.properties`:

```
process.roles=broker,controller
node.id=1

controller.quorum.voters=1@localhost:9093

listeners=PLAINTEXT://localhost:9092,CONTROLLER://localhost:9093
listener.security.protocol.map=PLAINTEXT:PLAINTEXT,CONTROLLER:PLAINTEXT
controller.listener.names=CONTROLLER

log.dirs=/tmp/kafka-logs
```

⚠️ **Important**:

* `log.dirs` path **must exist** and be **writable**

---

### Step 3️⃣ Generate Cluster ID

```bash
./bin/kafka-storage.sh random-uuid
```

📌 Copy the generated **Cluster ID**.

---

### Step 4️⃣ Format Kafka Storage (Standalone Mode)

Since this is a **single-node local setup**, use **standalone mode**:

```bash
sudo ./bin/kafka-storage.sh format \
  --standalone \
  -t <CLUSTER_ID> \
  -c ./config/server.properties
```

✔️ This will:

* Initialize a **single-node KRaft cluster**
* Create `meta.properties` inside `log.dirs`
* Automatically configure controller metadata

---

### Step 5️⃣ Start Kafka Server

```bash
sudo ./bin/kafka-server-start.sh ./config/server.properties
```

Kafka is now running on:

* Broker → `localhost:9092`
* Controller → `localhost:9093`

---

## 🧵 Create Kafka Topic

### Step 1️⃣ Create Topic

```bash
./bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --create \
  --topic demo-topic \
  --partitions 3 \
  --replication-factor 1
```

### Step 2️⃣ Verify Topic

```bash
./bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --describe \
  --topic demo-topic
```

---

## 🧪 Test Kafka via CLI (Before Spring Boot)

### Step 1️⃣ Start Kafka Consumer (CLI)

```bash
./bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic demo-topic \
  --from-beginning
```

---

### Step 2️⃣ Start Kafka Producer (New Terminal)

```bash
./bin/kafka-console-producer.sh \
  --bootstrap-server localhost:9092 \
  --topic demo-topic
```

Type messages:

```
Hello Kafka
Kafka 4 is awesome
Spring Boot integration
```

👉 Messages should instantly appear in the consumer terminal.

---

## ▶️ Run Spring Boot Services

Start services using:

```bash
mvn spring-boot:run
```

* Start **Producer Service first**
* Then start **Consumer Service**

---

## 🔍 End-to-End Flow

1. Client calls Producer REST API
2. Producer publishes message to `demo-topic`
3. Kafka broker stores message
4. Consumer service listens and consumes
5. Logs are printed via Log4j2

---

## 🚀 Future Enhancements

* Multiple consumer groups
* Retry & Dead Letter Topics (DLQ)
* Avro / JSON Schema
* Exactly-once semantics
* Dockerized Kafka setup
* Centralized log aggregation (ELK)

---

## 📚 Best Suited For

* Kafka beginners
* Spring Boot developers
* Backend interview preparation
* Hands-on Kafka learning

---

## 🧠 Author Notes

This project is intentionally kept **simple and educational** to build strong fundamentals in **Kafka 4.x.x and Spring Boot integration** before moving to production-grade architectures.

---

⭐ Feel free to fork, experiment, and extend this project!
