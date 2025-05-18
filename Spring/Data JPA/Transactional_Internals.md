Absolutely! Let's go **in-depth** into how `@Transactional` works internally in **Spring Framework**, covering the mechanics from **annotation to transaction commit/rollback**, with supporting class names, flow, and edge cases.

---

## 🧵 1. Transactional Setup in Spring

When you annotate a method or class with `@Transactional`, you're telling Spring to manage the transaction boundaries (start, commit, rollback) for you.

```java
@Transactional
public void transferMoney(...) {
   // operations on DB
}
```

But **how does this actually work** under the hood?

---

## 🧠 2. How Spring Handles `@Transactional`

### Step-by-step Flow:

| Step | What Happens                                | Internals                                                                                                |
| ---- | ------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| 1️⃣  | `@EnableTransactionManagement` is used      | Activates Spring’s transaction management capability using `TransactionManagementConfigurationSelector`. |
| 2️⃣  | AOP Proxy is created                        | Spring creates a proxy (JDK Dynamic Proxy or CGLIB) around the bean.                                     |
| 3️⃣  | Call to transactional method is intercepted | The proxy intercepts the method call using `TransactionInterceptor`.                                     |
| 4️⃣  | Transaction begins                          | `TransactionInterceptor` gets `PlatformTransactionManager` and starts a transaction.                     |
| 5️⃣  | Method is invoked                           | Actual method runs inside the transaction boundary.                                                      |
| 6️⃣  | Outcome determines commit/rollback          | Based on exception type, it commits or rolls back via `PlatformTransactionManager`.                      |

---

## 🧩 3. Key Classes and Their Role

### 📦 `@Transactional`

* Just metadata. Doesn't do anything by itself.
* Spring scans for this using `TransactionAttributeSource`.

### 📦 `TransactionInterceptor` (core logic)

* Intercepts the method call.
* Delegates to `TransactionManager` to handle the transaction.
* Uses `TransactionAttributeSource` to read annotation details.

### 📦 `TransactionAttributeSource`

* Interface that reads the `@Transactional` metadata from method/class.

### 📦 `PlatformTransactionManager`

* Interface that actually manages the transaction lifecycle:

  * `beginTransaction()`
  * `commit()`
  * `rollback()`
* Examples:

  * `DataSourceTransactionManager` (JDBC)
  * `JpaTransactionManager` (Hibernate/JPA)

---

## 🔄 4. Internal Call Stack (Simplified)

```text
@YourServiceProxy
    |
    ---> TransactionInterceptor.invoke()
              |
              ---> AbstractPlatformTransactionManager.getTransaction()
                        |
                        ---> DataSourceUtils.getConnection()
              |
              ---> invoke actual method
              |
              ---> commit() or rollback()
```

---

## 🚨 5. Rollback Rules

By default, Spring only rolls back on **unchecked exceptions** (`RuntimeException` and `Error`).

### To change this:

```java
@Transactional(rollbackFor = Exception.class)
public void yourMethod() throws Exception {
   ...
}
```

---

## 🔁 6. Propagation Behavior (Core Concept)

Defines how transactions relate to each other.

| Propagation     | Behavior                                               |
| --------------- | ------------------------------------------------------ |
| `REQUIRED`      | Joins existing or creates a new transaction (default). |
| `REQUIRES_NEW`  | Suspends existing and creates a new one.               |
| `NESTED`        | Creates nested transaction if supported.               |
| `NOT_SUPPORTED` | Executes non-transactionally, suspends if any.         |
| `MANDATORY`     | Must run inside existing transaction or throws error.  |
| `NEVER`         | Must not run inside transaction or throws error.       |
| `SUPPORTS`      | Joins if one exists, else runs without.                |

---

## 🧪 7. Example with Dry Run

### Code:

```java
@Transactional
public void processOrder() {
   saveOrder();     // DB insert
   chargePayment(); // Might fail
}
```

### Dry Run:

| Step | Operation                         | Transaction Status      |
| ---- | --------------------------------- | ----------------------- |
| 1    | Proxy intercepts `processOrder()` | Transaction Started     |
| 2    | `saveOrder()`                     | Runs in transaction     |
| 3    | `chargePayment()`                 | Throws exception        |
| 4    | Exception caught                  | Transaction Rolled Back |

---

## ⚠️ 8. Common Pitfalls

| Problem                             | Explanation                                                   |
| ----------------------------------- | ------------------------------------------------------------- |
| Internal method calls               | Bypass proxy → No transaction triggered. Use AOP-aware calls. |
| Not using Spring beans              | Spring only proxies beans it manages.                         |
| Using `final` methods               | CGLIB proxies can't override them → no transaction.           |
| Checked exceptions not rolling back | Must explicitly configure `rollbackFor`.                      |

---

## 🧵 9. Spring Boot Auto-Configuration Help

Spring Boot auto-configures:

* `DataSourceTransactionManager` for JDBC.
* `JpaTransactionManager` if `EntityManager` is available.

```java
@Bean
public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
    return new JpaTransactionManager(emf);
}
```

---

## 📌 10. Real Source Code References

* **Annotation**: [`org.springframework.transaction.annotation.Transactional`](https://github.com/spring-projects/spring-framework/blob/main/spring-tx/src/main/java/org/springframework/transaction/annotation/Transactional.java)
* **Interceptor**: `org.springframework.transaction.interceptor.TransactionInterceptor`
* **Transaction Manager**: `org.springframework.transaction.PlatformTransactionManager`
* **DataSource**: `org.springframework.jdbc.datasource.DataSourceTransactionManager`

---

## ✅ Summary

| Topic                      | Summary                                                         |
| -------------------------- | --------------------------------------------------------------- |
| Proxy-based AOP            | Spring wraps your beans with proxies to intercept method calls. |
| TransactionInterceptor     | Core class that manages transaction lifecycle.                  |
| PlatformTransactionManager | Executes begin/commit/rollback using DB resources.              |
| Rollback Strategy          | By default, only unchecked exceptions trigger rollback.         |
| Propagation & Isolation    | Control transaction relationships and DB consistency.           |

---

Let me know if you want:

* An **internal method call** demo and how to fix it.
* How it compares to **programmatic transaction management**.
* How it works in **Spring WebFlux** with `@Transactional`.

Just say the word.
