# Comprehensive Guide to @Transactional Propagation Levels

## Overview of Transaction Propagation
In Spring's `@Transactional` framework, transaction propagation defines how transactions relate to each other when transactional methods call other transactional methods. Each propagation level offers distinct behavior that solves specific architectural challenges.

## Detailed Analysis of Propagation Types

### 1. REQUIRED (Default)
**Definition:** If a transaction exists, the method will use it. If no transaction exists, a new one will be created.

**Technical Details:**
- Default propagation behavior in Spring
- Uses the current transaction context from the caller if available
- Transaction boundaries extend to include all operations in the calling chain
- Any exception in any method can trigger rollback for the entire transaction

**Example Implementation:**
```java
@Transactional(propagation = Propagation.REQUIRED)
public void placeOrder(Order order) {
    validateInventory(order);
    processPayment(order);
    updateInventory(order);
    createShippingLabel(order);
}
```

**Real-World Scenario: E-commerce Order Processing**

When a customer places an order, multiple operations need to occur as a single atomic unit:
1. Payment processing
2. Inventory updates
3. Order creation
4. Email notifications

If any step fails (like payment declined), all changes must be rolled back. `REQUIRED` ensures that if the checkout process already has a transaction, order processing joins it; otherwise, it creates its own.

**When It's Best:**
- For typical business operations that must succeed or fail as a unit
- When method can be called either independently or as part of a larger process
- When you need distributed transaction boundaries across multiple method calls
- Primary choice for most service-layer methods

**Implementation Considerations:**
- Rollback rules apply to all participants in the transaction
- Transaction isolation level and timeout settings come from the outermost transaction definition
- Large transactions may lead to lock contention in high-concurrency environments

---

### 2. SUPPORTS
**Definition:** If a transaction exists, the method will use it. If no transaction exists, the method executes non-transactionally.

**Technical Details:**
- No transaction synchronization if called outside a transaction
- Does not force transaction creation overhead
- Cannot roll back if no transaction exists
- Inherits isolation level and other properties from calling transaction

**Example Implementation:**
```java
@Transactional(propagation = Propagation.SUPPORTS)
public List<SalesReport> generateSalesReports(Date startDate, Date endDate) {
    // Read-only operations that can execute with or without transaction
    return reportRepository.findSalesBetween(startDate, endDate);
}
```

**Real-World Scenario: Reporting or Analytics Service**

A dashboard displays sales metrics that are calculated from database records. These read operations don't require a transaction for data consistency but might be called from a context that already has an active transaction.

**When It's Best:**
- For read-only methods that don't strictly need transactions
- When you want to minimize transaction overhead
- For methods that should adapt to their calling context
- Query operations that are frequently called but don't modify data

**Implementation Considerations:**
- Operations may see partially committed data when running non-transactionally
- Cannot rely on rollback capabilities if called without a transaction
- May see different data consistency behavior depending on calling context

---

### 3. MANDATORY
**Definition:** Requires an existing transaction. Throws an exception if no active transaction exists.

**Technical Details:**
- Throws `IllegalTransactionStateException` if no active transaction
- Acts as a safety check for operations that should never run standalone
- Inherits all transaction characteristics from the caller
- Cannot change transaction attributes like isolation level

**Example Implementation:**
```java
@Transactional(propagation = Propagation.MANDATORY)
public void transferFunds(Account from, Account to, BigDecimal amount) {
    from.debit(amount);
    to.credit(amount);
    auditService.logTransfer(from, to, amount);
}
```

**Real-World Scenario: Financial Transfer Within Larger Transaction**

A funds transfer method should never execute on its own—it must be part of a larger transaction that includes authorization checks, fraud detection, and comprehensive logging. `MANDATORY` enforces this architectural constraint at runtime.

**When It's Best:**
- When a method must only be called within a transaction context
- To prevent accidental non-transactional usage of critical operations
- For operations that are logical "parts" of a larger business process
- To enforce specific architectural constraints

**Implementation Considerations:**
- Creates explicit dependencies between layers
- Ensures critical operations never run without transaction guarantees
- Can simplify debugging by failing fast when architectural rules are violated

---

### 4. REQUIRES_NEW
**Definition:** Always creates a new transaction, suspending any existing transaction until the new one completes.

**Technical Details:**
- Completely isolated from any calling transaction
- Has its own rollback/commit cycle independent of caller
- Caller's transaction doesn't see changes until this transaction commits
- Resources held by suspended transaction remain locked

**Example Implementation:**
```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void logSecurityEvent(SecurityEvent event) {
    // Always persists, even if calling transaction rolls back
    securityAuditRepository.save(event);
    notificationService.alertIfSuspicious(event);
}
```

**Real-World Scenario: Critical Audit Logging**

During a banking transaction, all security events must be logged regardless of whether the main transaction succeeds. When a suspicious transfer is attempted and fails business validation, the security team still needs a record of the attempt.

**When It's Best:**
- For operations that must succeed even if the caller rolls back
- When you need to isolate failures between independent operations
- For tracking critical events like security audits or compliance logging
- When you need a fresh transaction with different attributes (isolation, timeout)

**Implementation Considerations:**
- Creates transaction overhead with two separate commits
- Can lead to inconsistent application state if not carefully designed
- May encounter deadlocks if both transactions modify the same data
- Perfect for "always commit" operations like error logging or audit trails

---

### 5. NOT_SUPPORTED
**Definition:** Executes non-transactionally, suspending any existing transaction temporarily.

**Technical Details:**
- Explicitly runs without transaction synchronization
- Suspended transactions resume after the method completes
- Changes made are committed immediately regardless of outer transaction outcome
- No rollback capability within the method

**Example Implementation:**
```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
public List<ProductRecommendation> getPersonalizedRecommendations(User user) {
    // Complex, potentially slow read operations
    List<Product> recentlyViewed = browsingHistoryService.getRecentItems(user);
    List<Category> preferredCategories = analyticsService.getPreferredCategories(user);
    return recommendationEngine.combineFactors(recentlyViewed, preferredCategories);
}
```

**Real-World Scenario: Long-Running Read Operations During Checkout**

During a checkout process that has an active transaction, you need to display personalized product recommendations. This complex calculation shouldn't keep transaction resources locked, nor should it be rolled back if checkout fails.

**When It's Best:**
- When you need to perform resource-intensive read operations
- To avoid long-running transactions that cause lock contention
- For operations that don't require transactional integrity
- When reading from external systems or performing cross-system integration

**Implementation Considerations:**
- May see partially committed data from concurrent transactions
- Should be used carefully with write operations as they commit immediately
- Helps reduce database lock duration in high-traffic systems
- Useful for performance optimization in read-heavy scenarios

---

### 6. NEVER
**Definition:** Executes non-transactionally and throws an exception if called within an existing transaction.

**Technical Details:**
- Throws `IllegalTransactionStateException` if a transaction exists
- Strongest form of isolation from transaction context
- Cannot join or inherit any transaction attributes
- Prevents accidental transactional execution

**Example Implementation:**
```java
@Transactional(propagation = Propagation.NEVER)
public boolean isDatabaseConnected() {
    try {
        // Simple lightweight check that must not join transactions
        return jdbcTemplate.queryForObject("SELECT 1", Integer.class) == 1;
    } catch (Exception e) {
        return false;
    }
}
```

**Real-World Scenario: System Health Check**

A monitoring endpoint checks if the application's database connection is working. This operation must be lightweight and never participate in any ongoing transaction to avoid affecting system performance or locking resources.

**When It's Best:**
- For lightweight monitoring or health check methods
- When you want to guarantee a method never runs transactionally
- To prevent unintended side effects in utility methods
- For operations that should be completely isolated from business transactions

**Implementation Considerations:**
- Creates explicit architectural constraints
- Useful for enforcing separation between transactional and non-transactional code paths
- Helps prevent inadvertent participation in long-running transactions
- Can simplify reasoning about transaction boundaries in complex systems

---

### 7. NESTED
**Definition:** Creates a nested transaction if a transaction exists, using savepoints for partial rollback capability. Acts like REQUIRED if no transaction exists.

**Technical Details:**
- Uses JDBC savepoints to create rollback segments within a larger transaction
- Child transaction can roll back without affecting the parent
- Parent transaction failure rolls back the nested transaction too
- Only supported by specific datasource implementations (like JDBC with savepoint support)

**Example Implementation:**
```java
@Transactional(propagation = Propagation.REQUIRED)
public BatchResult processBatch(List<Invoice> invoices) {
    BatchResult result = new BatchResult();
    
    for (Invoice invoice : invoices) {
        try {
            processInvoice(invoice); // Uses NESTED propagation
            result.addSuccess(invoice);
        } catch (Exception e) {
            result.addFailure(invoice, e);
            // Only this invoice processing is rolled back
        }
    }
    
    return result;
}

@Transactional(propagation = Propagation.NESTED)
private void processInvoice(Invoice invoice) {
    validateInvoice(invoice);
    applyPayment(invoice);
    updateAccountBalance(invoice.getAccount());
    notifyCustomer(invoice);
}
```

**Real-World Scenario: Batch Processing with Partial Success**

When importing hundreds of invoices from a legacy system, you want to process as many as possible without failing the entire batch if individual invoices have issues. `NESTED` allows each invoice to be processed in its own savepoint, capturing failures without rolling back successfully processed items.

**When It's Best:**
- For batch operations where partial failure is acceptable
- When you want finer-grained control over rollback segments
- For implementing retry logic within a larger transaction
- When implementing all-or-nothing semantics for a subset of operations

**Implementation Considerations:**
- Not supported by all transaction managers (works with JDBC but not JTA)
- Not as cleanly isolated as REQUIRES_NEW
- Provides an elegant middle ground between full isolation and full participation
- Perfect for scenarios where parent operation should continue despite failures in sub-operations

---

## Comparison Matrix

| Propagation Type | New TX if none exists | Uses existing TX | Can roll back existing TX | Isolation level | Main use case |
|------------------|:---------------------:|:---------------:|:-------------------------:|:---------------:|--------------|
| REQUIRED         | ✅                    | ✅              | ✅                        | From parent     | Standard business operations |
| SUPPORTS         | ❌                    | ✅              | ✅                        | From parent     | Read operations |
| MANDATORY        | ❌                    | ✅              | ✅                        | From parent     | Enforcing architectural constraints |
| REQUIRES_NEW     | ✅                    | ❌ (suspends)   | ❌                        | Own settings    | Independent operations |
| NOT_SUPPORTED    | ❌                    | ❌ (suspends)   | ❌                        | N/A             | Performance-critical reads |
| NEVER            | ❌                    | ❌ (errors)     | ❌                        | N/A             | Utility/monitoring functions |
| NESTED           | ✅                    | ✅ (savepoint)  | ✅ (segment only)         | From parent     | Batch processing with partial success |

## Best Practices for Transaction Design

1. **Keep transactions short-lived**
   - Long-running transactions increase lock contention
   - Consider breaking up complex processes into multiple transactions

2. **Choose appropriate propagation for your use case**
   - Default to REQUIRED unless you have specific requirements
   - Use REQUIRES_NEW sparingly due to overhead

3. **Be mindful of transaction boundaries**
   - Transaction boundaries should align with business operation boundaries
   - Consider service layer methods as primary transaction demarcation points

4. **Watch for proxy-related issues**
   - @Transactional only works through Spring proxies
   - Self-invocation (calling a @Transactional method from within the same class) bypasses the proxy

5. **Consider read-only transactions**
   - Use `@Transactional(readOnly = true)` for queries to allow optimizations
   - Combine with appropriate propagation for maximum benefit

6. **Be aware of isolation levels**
   - Different propagation behaviors interact with isolation levels
   - Nested and REQUIRES_NEW allow different isolation for different operation segments

7. **Test transaction boundaries**
   - Verify that rollbacks occur as expected in failure scenarios
   - Ensure proper cleanup of resources in all paths
