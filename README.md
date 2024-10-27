# Kafka_Tutorial

Apache Kafka is an open-source software platform used for handling real-time data feeds. Think of it like a messaging system where different applications can send ("produce") and receive ("consume") streams of data in real time. It’s especially useful when you need to process or analyze large amounts of data quickly and continuously, like tracking website activity, processing payments, or monitoring sensor data. Kafka is highly scalable, fault-tolerant, and can handle massive amounts of data, making it popular for big data and streaming applications.

## What is a messaging system
A messaging system is a simple exchange of messages between two or more persons, devices, etc. A publish-subscribe messaging system allows a sender to send/write the message and a receiver to read that message. In Apache Kafka, a sender is known as a producer who publishes messages, and a receiver is known as a consumer who consumes that message by subscribing it.

## What is Streaming process
A streaming process is the processing of data in parallelly connected systems. This process allows different applications to limit the parallel execution of the data, where one record executes without waiting for the output of the previous record. Therefore, a distributed streaming platform enables the user to simplify the task of the streaming process and parallel execution. 

# Apache Kafka Core APIs

Apache Kafka provides a set of core APIs that make it a powerful platform for real-time data streaming, processing, and integration across applications and systems. This document provides an overview of each core API and how they are used within Kafka.

---

## Table of Contents

- [Producer API](#producer-api)
- [Consumer API](#consumer-api)
- [Streams API](#streams-api)
- [Connector API](#connector-api)
- [Admin API](#admin-api)

---

## Producer API

The **Producer API** allows applications to publish (or "produce") streams of records to Kafka topics.

- **Purpose**: Send data to Kafka.
- **Use Cases**: Useful for applications or services that need to log events, such as ride requests, user activity, system logs, or other data streams.

```javascript
// producer.js
import { Kafka } from 'kafkajs';

const kafka = new Kafka({
  clientId: 'my-app',
  brokers: ['localhost:9092'], // replace with your broker address
});

const producer = kafka.producer();

const runProducer = async () => {
  await producer.connect();
  await producer.send({
    topic: 'test-topic',
    messages: [
      { key: 'key1', value: 'Hello KafkaJS user!' },
    ],
  });
  await producer.disconnect();
};

runProducer().catch(console.error);

```

## Consumer API
The **Consumer API** enables applications to subscribe to topics and consume (or "process") streams of records.

- **Purpose**: Read data from Kafka.
- **Use Cases**: Analytics engines, data processing applications, or any service that needs to read real-time data from Kafka topics.

```javascript
// consumer.js
import { Kafka } from 'kafkajs';

const kafka = new Kafka({
  clientId: 'my-app',
  brokers: ['localhost:9092'], // replace with your broker address
});

const consumer = kafka.consumer({ groupId: 'test-group' });

const runConsumer = async () => {
  await consumer.connect();
  await consumer.subscribe({ topic: 'test-topic', fromBeginning: true });

  await consumer.run({
    eachMessage: async ({ topic, partition, message }) => {
      console.log(`Received message: ${message.value.toString()}`);
    },
  });
};

runConsumer().catch(console.error);

```

## Streams API
The **Streams API** is a client library for building real-time data processing applications within Kafka.

- **Purpose**: Process, transform, filter, aggregate, and analyze data as it flows through Kafka.
- **Use Cases**: Real-time analytics, data transformation pipelines, and event-driven microservices.


```javascript
// streamProcessor.js
import { Kafka } from 'kafkajs';

const kafka = new Kafka({
  clientId: 'my-app',
  brokers: ['localhost:9092'], // replace with your broker address
});

const consumer = kafka.consumer({ groupId: 'test-group' });
const producer = kafka.producer();

const runStreamProcessor = async () => {
  await producer.connect();
  await consumer.connect();
  await consumer.subscribe({ topic: 'input-topic', fromBeginning: true });

  await consumer.run({
    eachMessage: async ({ topic, partition, message }) => {
      const processedMessage = message.value.toString().toUpperCase(); // Example transformation
      await producer.send({
        topic: 'output-topic',
        messages: [
          { value: processedMessage },
        ],
      });
    },
  });
};

runStreamProcessor().catch(console.error);

```

## Connector API
The **Connector** API provides connectivity with external systems to pull data into Kafka or push data from Kafka to other systems.

- **Purpose**: Integrate Kafka with databases, storage systems, or analytics platforms.
- **Use Cases**: Connectors for databases, data lakes, NoSQL stores, and other external systems, often using Kafka Connect.


```javascript
// connector.js
import fetch from 'node-fetch';

const createConnector = async () => {
  const response = await fetch('http://localhost:8083/connectors', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      name: 'my-connector',
      config: {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSource",
        "tasks.max": "1",
        "topic": "test-topic",
        "file": "/path/to/input-file.txt",
      },
    }),
  });

  if (response.ok) {
    console.log('Connector created successfully!');
  } else {
    console.error('Failed to create connector:', await response.text());
  }
};

createConnector().catch(console.error);

```

## Admin API
The **Admin API** provides management and monitoring capabilities for Kafka.

- **Purpose**: Manage Kafka resources, including topics, brokers, and configurations.
- **Use Cases**: Create, delete, and configure Kafka topics, manage broker metadata, and monitor the environment.


```javascript

// admin.js
import { Kafka } from 'kafkajs';

const kafka = new Kafka({
  clientId: 'my-app',
  brokers: ['localhost:9092'], // replace with your broker address
});

const admin = kafka.admin();

const manageTopics = async () => {
  await admin.connect();
  
  // Create a new topic
  await admin.createTopics({
    topics: [
      { topic: 'new-topic', numPartitions: 1, replicationFactor: 1 },
    ],
  });

  console.log('Topic created successfully!');

  // List all topics
  const topics = await admin.listTopics();
  console.log('Existing topics:', topics);

  await admin.disconnect();
};

manageTopics().catch(console.error);

```