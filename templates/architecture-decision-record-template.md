# 📄 Architecture Decision Record (ADR) Template

This is the standard template for documenting significant technical decisions. Every major change to the system's architecture, data flow, or vendor integration must be captured as an ADR to provide historical context and prevent "Architectural Drift."

---

## ADR-[Number]: [Short, Descriptive Title]

* **Status:** [Proposed | Accepted | Superseded | Deprecated]
* **Date:** YYYY-MM-DD
* **Deciders:** [Name/Role], [Name/Role]
* **Consulted:** [Team/Individual]

### 1. Context and Problem Statement
*What is the specific technical challenge or business requirement we are addressing?*
*Include constraints, technical debt considerations, or performance bottlenecks currently being faced.*

### 2. Decision Drivers
*What are the "Must-Haves" for this solution?*
* (e.g., P99 latency must remain < 100ms)
* (e.g., Must support multi-region deployment)
* (e.g., Developer effort must not exceed 2 sprints)

### 3. Considered Options
*What other paths did we explore?*
* **Option A:** [Description] - [Pros/Cons]
* **Option B:** [Description] - [Pros/Cons]

### 4. Decision Outcome
**Chosen Option:** [Name of Option]
**Reasoning:** *Why was this chosen over the others? How does it satisfy the Decision Drivers?*

### 5. Consequences
*What is the price we pay for this decision?*
* **Positive:** (e.g., "Increased system resilience during vendor downtime")
* **Negative/Risks:** (e.g., "Increased complexity in our CI/CD pipeline")
* **Neutral:** (e.g., "Requires training for Squad B on the new event-bus schema")

---
*“ADRs are the memory of the engineering organization.”*