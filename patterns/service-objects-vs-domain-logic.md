# 🏗️ Service Objects vs. Domain Logic

In high-traffic systems, the "Fat Model" or "Fat Controller" anti-pattern leads to a **Big Ball of Mud**. To maintain a clean, testable architecture, we enforce a strict separation between **Domain Entities** and **Service Objects**.

## 1. The Core Philosophy
* **Domain Entities (The "What"):** Represent the state and intrinsic behavior of a single object (an `Invoice` or a `User`).
* **Service Objects (The "How"):** Orchestrate complex actions that involve multiple entities, external APIs, or side effects (e.g., `ProcessPaymentService` or `OnboardUserAccount`).

## 2. When to Use a Service Object
We move logic into a Service Object if it meets any of these criteria:
* The action is complex (calculating dynamic pricing based on multiple external factors).
* The action reaches across multiple domain boundaries.
* The action interacts with external dependencies (sending an email via SendGrid, charging a card via Stripe).
* The action has multiple steps that must be wrapped in a single database transaction.

## 3. Pattern Implementation: The Single Responsibility
A "10/10" Service Object should have exactly **one** public method (usually `.call()` or `.execute()`).

```typescript
// Example: ProcessOrderService
// Responsibilities: Validate stock, Charge Customer, Update Order, Trigger Shipment
export class ProcessOrderService {
  constructor(
    private paymentGateway: PaymentGateway,
    private warehouseApi: WarehouseApi,
    private db: Database
  ) {}

  async call(orderId: string, paymentToken: string): Promise<Result> {
    return await this.db.transaction(async (tx) => {
      const order = await tx.orders.find(orderId);
      
      // 1. Business Logic Gate
      if (!order.isProcessable()) throw new Error("Invalid Order State");

      // 2. External Integration (Handled here, not in the Model)
      const charge = await this.paymentGateway.charge(paymentToken, order.total);

      // 3. State Transition
      order.markAsPaid(charge.id);
      await tx.orders.save(order);

      // 4. Downstream Side Effect
      await this.warehouseApi.notify(order);
    });
  }
}
```

## 4. By enforcing this pattern, we ensure:

* Testability: Service Objects are easily mocked. Domain Entities can be unit tested without a database.

* Scalability: As the system grows, we add new Services rather than bloating existing Models.

* Clarity: A developer can look at the /app/services folder and see exactly what the system does (UpdateSubscription, CancelOrder, GenerateInvoice).


