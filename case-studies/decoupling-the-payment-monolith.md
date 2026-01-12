# 🛠️ Case Study: Decoupling the Payment Monolith

### The Challenge
Our core Payment logic was a 5,000-line "Fat Model" inside a monolithic Rails/Django application. It was tightly coupled to three different vendor SDKs, making it impossible to test without network calls and causing frequent deployment bottlenecks.

### The Strategy
We applied the **Technical Density** patterns to migrate to a modular, resilient architecture:

1. **Extraction via Service Objects:** We moved orchestrations (Auth -> Charge -> Log) into dedicated Service Objects.
2. **Implementing the ACL:** We wrapped Stripe and Adyen in Adapters. This allowed us to swap providers during a vendor outage in under 5 minutes via a feature flag.
3. **Transition to Event-Driven:** Instead of the Monolith "telling" the Invoice service to generate a PDF, we emitted an `Order.Paid` event.

### The Impact
- **Test Velocity:** Unit test suite time dropped from 20 mins to 4 mins by removing DB dependencies.
- **Resilience:** Successfully handled a 4x traffic spike during Black Friday with 0% dropped transactions due to event-driven buffering.
- **Maintainability:** New payment methods can now be added in 1 sprint instead of 4.