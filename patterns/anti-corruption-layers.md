# 🛡️ Anti-Corruption Layers (ACL)

In complex, high-traffic systems, the biggest risk to a "Clean Domain" is **Domain Leakage** from external dependencies (3rd-party APIs like Stripe, Twilio, or legacy internal systems). 

An **Anti-Corruption Layer** acts as a translator, ensuring that external models never pollute our internal business logic.

## 1. The Problem: Vendor Coupling
Without an ACL, a change in a vendor’s API (e.g., Stripe changing `customer_id` to `account_token`) requires changes deep inside your core business logic. This creates a "Big Ball of Mud" where the vendor dictates your code structure.

## 2. Our Implementation: The Translation Pattern
We enforce a strict boundary between external data and our internal Domain Entities.

* **Adapters:** Handle the physical communication (HTTP/gRPC) and raw response.
* **Translators (Mappers):** Transform the external JSON/Object into an internal **Domain Entity**.

### Example: Payment Gateway Isolation
Instead of passing a `Stripe::Charge` object throughout our system, we map it to our internal `PaymentResult` entity at the boundary.

```typescript
// ❌ The "Corrupted" Way (Coupled to Stripe)
async process(order: Order, stripeResponse: any) {
  if (stripeResponse.status === 'succeeded') { // Vendor-specific field names
    order.complete(stripeResponse.id);
  }
}

// ✅ The ACL Way (Decoupled)
class StripeAdapter implements PaymentGateway {
  async charge(amount: number): Promise<PaymentResult> {
    const raw = await stripe.charges.create({ ... });
    
    // Translation happens here, at the edge
    return new PaymentResult({
      transactionId: raw.id,
      isSuccess: raw.status === 'succeeded',
      provider: 'stripe'
    });
  }
}