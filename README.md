# Event-Driven Architecture with Kafka and Spring Cloud Stream

Watch the tutorial video here: [![Watch the video](https://img.youtube.com/vi/8uY7JE_X_Fw/0.jpg)](https://www.youtube.com/watch?v=8uY7JE_X_Fw)

## Overview
This project demonstrates how to build an event-driven architecture using Apache Kafka as a message broker and Spring Cloud Stream for seamless integration in a Spring Boot environment. The focus is on producing, consuming, and processing real-time streaming data with Kafka and Kafka Streams, supported by practical examples and a live data visualization frontend.

---

## Features
- **Kafka Broker Setup**: Easily start Kafka and Zookeeper using Docker Compose, or manually without Docker if preferred.
- **Basic Kafka Operations**: Use Kafka console producer and consumer to send and receive messages on topics.
- **Spring Boot Integration**:
  - Create REST controllers to publish events (PageEvent) to Kafka topics.
  - Implement Kafka consumers using Spring Cloud Stream functional programming model.
  - Develop suppliers (producers) that generate streaming events periodically.
- **Kafka Streams Processing**:
  - Real-time stream processing and analytics on incoming events.
  - Filter, map, group, and aggregate streaming data using Kafka Streams API.
  - Windowed computations to analyze data over sliding time intervals.
- **Real-time Data Visualization**:
  - A simple HTML frontend using Smoothie Charts to display live page visit counts for two pages (`P1` and `P2`).
  - Server-Sent Events (SSE) for pushing analytics results from backend to frontend in real time.

---

## Technologies Used
- Java 21
- Spring Boot (latest version)
- Spring Cloud Stream with Kafka Binder
- Apache Kafka & Kafka Streams
- Docker & Docker Compose (recommended for Kafka setup)
- Smoothie Charts (JavaScript library for time series visualization)
- REST API and Server-Sent Events (SSE)

---

## Getting Started

### Prerequisites
- Docker & Docker Compose installed (recommended)
- Java 21 SDK
- Maven or Gradle build tool
- IDE such as IntelliJ IDEA

### Running the Application
1. Clone the repository
   ```bash
   git clone https://github.com/chaymaAitB/01-event-driven-kafka-spring-cloud-stream.git
   ```
2. Import the project into your IDE.
3. Ensure dependencies for `spring-cloud-stream`, `spring-kafka`, and `kafka-streams` are included.
4. Configure `application.properties` with Kafka topic names and binder settings.
5. Run the Spring Boot application.

---

## Key Components

### PageEvent Model
A data class representing a page visit event:
- `pageName`: Name of the visited page (e.g., "P1", "P2").
- `user`: User identifier (e.g., "U1", "U2").
- `visitDate`: Timestamp of the visit.
- `duration`: Duration in milliseconds.

### REST Controller
Provides an endpoint to publish `PageEvent` messages to Kafka topics:
- Endpoint example: `/publish?name=P1&topic=T2`
- Uses `StreamBridge` to send messages to Kafka topic dynamically.

### Consumer and Supplier Beans
- **Consumer**: A Spring bean consuming `PageEvent` messages from configured Kafka topic.
- **Supplier**: Produces `PageEvent` messages periodically (default every 1 second), simulating a live event stream.

### Kafka Streams Processor
- A function that consumes a stream of `PageEvent` messages.
- Applies filters (e.g., duration > 100 ms), maps keys and values.
- Groups events by page name and performs windowed aggregations (e.g., count visits in last 5 seconds).
- Outputs results to a different Kafka topic for analytics.

### Interactive Query and Analytics Endpoint
- Uses Kafka Streams Interactive Query to read the state store with aggregated results.
- Exposes an endpoint `/analytics` that streams real-time analytics data to frontend clients using Server-Sent Events.

### Frontend Visualization
- Simple HTML page with Smoothie Charts for real-time graphing.
- Subscribes to `/analytics` endpoint via SSE, updates charts with live visit counts for pages P1 and P2.

---

## Configuration Highlights
- `spring.cloud.stream.bindings` for binding inputs/outputs to Kafka topics.
- `spring.cloud.stream.function.definition` to specify consumer/supplier/processor bean names.
- Kafka Streams properties such as `commit.interval.ms` to control aggregation commit frequency.
- Window size and duration configured for time-based stream processing.

---

## Running the Application

To run the Kafka and Spring Cloud Stream application effectively, the first step is to start the Kafka broker and Zookeeper, preferably using Docker and Docker Compose for ease of setup and consistency. The provided `docker-compose.yml` file configures containers for Kafka and Zookeeper, exposing necessary ports (e.g., 9092 for Kafka). You can launch this environment by navigating to the project root directory and running:

```bash
docker-compose up -d
```

This command initializes the containers in detached mode. Once Kafka and Zookeeper are running, you can verify their status with:

```bash
docker ps
```

Next, start the Spring Boot application. The application uses Java 21 and Spring Cloud Stream dependencies configured to connect with Kafka. Running the Spring Boot app (e.g., through your IDE or via `mvn spring-boot:run`) will bring up the REST controller, the Kafka producers (supplier), consumers, and stream processors.

Make sure your `application.properties` file is correctly configured to bind the Kafka topics to their respective functions such as producers, consumers, and stream processors. The application exposes endpoints such as `/publish` to publish page event messages and `/analytics` to stream real-time analytics data to the frontend.

---

## Testing the Application

Testing involves multiple steps to ensure the Kafka messaging and stream processing pipeline works as intended:

1. **Kafka Console Producer and Consumer:**  
   Start by manually testing Kafka topics using Kafka’s console tools inside the Kafka container. Run commands like:

   ```bash
   docker exec --interactive --tty broker kafka-console-producer --bootstrap-server broker:9092 --topic R1
   ```

   and

   ```bash
   docker exec --interactive --tty broker kafka-console-consumer --bootstrap-server broker:9092 --topic R1 
   ```
   
   or

   ```bash
   docker exec --interactive --tty broker kafka-console-consumer --bootstrap-server broker:9092 --topic R1 --from-beginning
   ```
   
   This ensures Kafka is correctly receiving and delivering messages.

   ![Demo Screenshot](images/Img1.png)

3. **REST Controller Testing:**  
   Use your browser or tools like Postman to invoke the REST endpoint:

   ```
   http://localhost:8080/publish?name=P1&amp;topic=T2
   ```

   This triggers the publishing of a `PageEvent` to the Kafka topic `T2`. You can verify the message reception by running a Kafka console consumer on topic `T2`.
   
   ![Demo Screenshot](images/Img2.png)
   
5. **Spring Cloud Stream Consumer:**  
   The application includes a consumer function that automatically subscribes to topics and processes incoming messages. Monitoring the application logs or console output confirms consumption of messages and correct handling.

6. **Supplier and Stream Processing:**  
   The supplier simulates real-time event generation, publishing messages at configured intervals (default 1 second, adjustable via properties). Kafka Streams performs real-time analytics such as counting page visits within sliding windows. You can observe these processed results in topics like `T6`.

   ![Demo Screenshot](images/Img3.png)
   
8. **Real-Time Frontend Visualization:**  
   By accessing the `/analytics` endpoint, the backend streams analytics data using Server-Sent Events (SSE). The provided static `index.html` page subscribes to this stream and displays live charts of page visit counts using the Smoothie.js library. This visual feedback loop confirms the end-to-end pipeline is functional.

   🧠 Analytics page
   ![Analytics Screenshot](images/Img4.png)  
👉 [Open Analytics](http://localhost:8080/analytics)

   🏠 Home Page
   ![Home Page Screenshot](images/Img5.png)  
👉 [Open Home Page](http://localhost:8080/index.html)

---

## Summary
This project serves as a comprehensive practical exercise to understand event-driven microservices architecture using Kafka and Spring Cloud Stream. It covers Kafka setup, producing and consuming messages, real-time stream processing with Kafka Streams, and live visualization of analytics data. The approach is broker-agnostic at the code level due to Spring Cloud Stream abstraction, enabling flexibility for future extensions.

