# 📋 System Design Review Checklist

This checklist is used during Architectural Review Meetings to ensure that new services or features adhere to our standards for **Technical Density** and **Operational Excellence**.

## 1. Domain & Boundaries
- [ ] **Service Objects:** Is business logic isolated from controllers/models?
- [ ] **Bounded Context:** Does this service own its data? Are we avoiding "Distributed Joins"?
- [ ] **ACL:** If integrating with a 3rd party, is there a translation layer to protect our Domain?

## 2. Resilience & Scale
- [ ] **Failure Modes:** What happens if this service's database is down? What is the fallback?
- [ ] **Retries & Idempotency:** Are all write operations idempotent? Do we have exponential backoff?
- [ ] **Circuit Breakers:** Are downstream calls wrapped to prevent cascading failures?

## 3. Data & Consistency
- [ ] **Consistency Model:** Is "Eventual Consistency" acceptable here? If not, how are we handling transactions?
- [ ] **Outbox Pattern:** If we emit events, are we using a transactional outbox to ensure delivery?
- [ ] **PII/Security:** Is sensitive data encrypted at rest and in transit?

## 4. Observability
- [ ] **Tracing:** Are we passing `correlation-id` headers through all service calls?
- [ ] **Metrics:** Do we have alerts for P99 latency and Error Rates (Golden Signals)?
- [ ] **Logs:** Are logs structured (JSON) and searchable for debugging?