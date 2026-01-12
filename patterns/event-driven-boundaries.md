# 📡 Event-Driven Boundaries

In a high-traffic microservice architecture, synchronous communication (REST/gRPC) between services creates tight coupling and cascading failures. We enforce **Event-Driven Boundaries** to ensure temporal decoupling and system resilience.

## 1. The Core Principle: "Tell, Don't Ask"
Instead of Service A calling Service B and waiting for a response (blocking), Service A performs its action and emits a **Domain Event** (e.g., `OrderPaid`). Service B listens to this event and acts independently.

## 2. Preventing Cascading Failures
Synchronous chains create a single point of failure. If the "Email Service" is down, the "Checkout Service" should not fail.
* **Temporal Decoupling:** The producer doesn't care if the consumer is online at the exact moment the event is sent.
* **Backpressure Handling:** Message brokers (Kafka/RabbitMQ) act as a buffer, allowing consumers to process events at their own pace during traffic spikes.



## 3. The Transactional Outbox Pattern
To ensure data consistency without distributed transactions (which are slow and brittle), we implement the **Outbox Pattern**.
1. We save the Business Entity and the Domain Event in the **same database transaction**.
2. A separate relay process reads the outbox table and publishes the event to the broker.
3. This guarantees **At-Least-Once Delivery** and prevents "Ghost Events" (where an event is sent but the database rollback happened).

## 4. Implementation Standards
* **Thin Events vs. Fat Events:** We prefer "Thin Events" (containing only IDs) to avoid stale data. Consumers can query the "Source of Truth" if they need more details.
* **Idempotency:** All event consumers must be idempotent. Processing the same event twice (due to network retries) must not result in duplicate side effects (e.g., double-charging a customer).
* **Schema Registry:** We use a Schema Registry (e.g., Avro/Protobuf) to ensure that producers don't break consumers with incompatible message changes.

---
*“Events are the facts of the past; let the system react to history, not wait for the future.”*