The **Strategy Design Pattern** is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable. This pattern allows the algorithm to vary independently from the clients that use it.

---

### **Key Features of the Strategy Pattern**
1. **Encapsulation of Algorithms**: Each algorithm is encapsulated in a separate class.
2. **Interchangeability**: Algorithms can be swapped in and out dynamically.
3. **Open/Closed Principle**: The pattern promotes the principle by allowing new algorithms to be added without modifying existing code.
4. **Context Class**: A context class controls which strategy is used and provides a unified interface to the client.

---

### **When to Use the Strategy Pattern?**
- When you have multiple algorithms or behaviors that a class needs to support, and you want to avoid using conditional statements (`if-else` or `switch-case`) to select the behavior.
- When you want to make the system easily extensible to include new behaviors without altering existing code.
- When different behaviors or algorithms are applied at runtime.

---

### **Components of the Strategy Pattern**
1. **Strategy Interface**: Common interface for all supported algorithms.
2. **Concrete Strategies**: Implementations of the strategy interface, representing specific algorithms or behaviors.
3. **Context**: Maintains a reference to a strategy object and interacts with it through the strategy interface.

---

### **Implementing Strategy Pattern in Java**

#### Example: Payment Strategy
Imagine an e-commerce application where users can pay using different payment methods (e.g., Credit Card, PayPal, Bitcoin).

#### **Step 1: Define the Strategy Interface**
```java
public interface PaymentStrategy {
    void pay(int amount);
}
```

#### **Step 2: Implement Concrete Strategies**
```java
// Credit Card Payment Strategy
public class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    private String cardHolderName;

    public CreditCardPayment(String cardNumber, String cardHolderName) {
        this.cardNumber = cardNumber;
        this.cardHolderName = cardHolderName;
    }

    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card.");
    }
}

// PayPal Payment Strategy
public class PayPalPayment implements PaymentStrategy {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal.");
    }
}

// Bitcoin Payment Strategy
public class BitcoinPayment implements PaymentStrategy {
    private String walletAddress;

    public BitcoinPayment(String walletAddress) {
        this.walletAddress = walletAddress;
    }

    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Bitcoin.");
    }
}
```

#### **Step 3: Create the Context Class**
```java
public class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    // Set the payment strategy dynamically
    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    // Perform payment
    public void checkout(int amount) {
        if (paymentStrategy == null) {
            throw new IllegalStateException("Payment strategy is not set.");
        }
        paymentStrategy.pay(amount);
    }
}
```

#### **Step 4: Client Code**
```java
public class Main {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();

        // Pay using Credit Card
        cart.setPaymentStrategy(new CreditCardPayment("1234-5678-9876-5432", "John Doe"));
        cart.checkout(100);

        // Pay using PayPal
        cart.setPaymentStrategy(new PayPalPayment("johndoe@example.com"));
        cart.checkout(200);

        // Pay using Bitcoin
        cart.setPaymentStrategy(new BitcoinPayment("1A2B3C4D5E6F"));
        cart.checkout(300);
    }
}
```

---

### **Advantages of Strategy Pattern**
1. **Flexibility**: New strategies can be added without modifying existing code.
2. **Eliminates Conditional Logic**: Avoids complex conditional statements for algorithm selection.
3. **Reusability**: Strategies can be reused across different contexts.
4. **Separation of Concerns**: Encapsulates algorithms, promoting cleaner code.

---

### **Disadvantages of Strategy Pattern**
1. **Increased Complexity**: More classes are introduced, which can make the codebase larger.
2. **Client Awareness**: The client must know about the available strategies to select one.

---

This implementation allows you to add more payment methods (strategies) without modifying existing code, adhering to the **Open/Closed Principle**.