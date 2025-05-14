The **Decorator Design Pattern** is a **structural design pattern** that allows behavior to be added to individual objects, dynamically, without affecting the behavior of other objects from the same class. This is achieved by using wrapper objects, also known as decorators.

---

### **Key Features of the Decorator Pattern**
1. **Dynamic Behavior Addition**: Adds functionality dynamically to objects at runtime.
2. **Wrapper Objects**: The decorator wraps an object and provides additional behaviors.
3. **Open/Closed Principle**: Extends functionality without modifying existing code.
4. **Composition Over Inheritance**: Achieves behavior extension without subclassing.

---

### **When to Use the Decorator Pattern?**
- When you need to add responsibilities to individual objects and not to the entire class.
- When you want to avoid a large number of subclasses to support combinations of functionalities.
- When you need a flexible alternative to subclassing for extending functionality.

---

### **Components of the Decorator Pattern**
1. **Component Interface**: Defines the common interface for the objects and decorators.
2. **Concrete Component**: The base implementation of the component interface that can be decorated.
3. **Decorator (Abstract)**: Implements the component interface and contains a reference to a component object.
4. **Concrete Decorators**: Extend the decorator to add specific functionality.

---

### **Implementing Decorator Pattern in Java**

#### Example: Coffee Shop (Adding Decorators to a Coffee Order)

---

#### **Step 1: Define the Component Interface**
```java
// Component Interface
public interface Coffee {
    String getDescription();
    double getCost();
}
```

---

#### **Step 2: Implement the Concrete Component**
```java
// Concrete Component
public class SimpleCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Simple Coffee";
    }

    @Override
    public double getCost() {
        return 5.0;
    }
}
```

---

#### **Step 3: Create the Abstract Decorator**
```java
// Abstract Decorator
public abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee;

    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    @Override
    public String getDescription() {
        return coffee.getDescription();
    }

    @Override
    public double getCost() {
        return coffee.getCost();
    }
}
```

---

#### **Step 4: Add Concrete Decorators**
```java
// Concrete Decorator: Milk
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Milk";
    }

    @Override
    public double getCost() {
        return coffee.getCost() + 1.5;
    }
}

// Concrete Decorator: Sugar
public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Sugar";
    }

    @Override
    public double getCost() {
        return coffee.getCost() + 0.5;
    }
}

// Concrete Decorator: Whipped Cream
public class WhippedCreamDecorator extends CoffeeDecorator {
    public WhippedCreamDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Whipped Cream";
    }

    @Override
    public double getCost() {
        return coffee.getCost() + 2.0;
    }
}
```

---

#### **Step 5: Use the Decorator in Client Code**
```java
public class Main {
    public static void main(String[] args) {
        // Start with Simple Coffee
        Coffee coffee = new SimpleCoffee();
        System.out.println(coffee.getDescription() + " -> $" + coffee.getCost());

        // Add Milk
        coffee = new MilkDecorator(coffee);
        System.out.println(coffee.getDescription() + " -> $" + coffee.getCost());

        // Add Sugar
        coffee = new SugarDecorator(coffee);
        System.out.println(coffee.getDescription() + " -> $" + coffee.getCost());

        // Add Whipped Cream
        coffee = new WhippedCreamDecorator(coffee);
        System.out.println(coffee.getDescription() + " -> $" + coffee.getCost());
    }
}
```

---

### **Output**
```
Simple Coffee -> $5.0
Simple Coffee, Milk -> $6.5
Simple Coffee, Milk, Sugar -> $7.0
Simple Coffee, Milk, Sugar, Whipped Cream -> $9.0
```

---

### **Advantages of the Decorator Pattern**
1. **Open/Closed Principle**: New behavior can be added without altering existing code.
2. **Dynamic Composition**: Objects can be decorated and combined dynamically at runtime.
3. **Reusability**: Decorators can be reused and stacked in different combinations.

---

### **Disadvantages of the Decorator Pattern**
1. **Complexity**: Adding too many decorators can make the code harder to read and maintain.
2. **Performance Overhead**: The pattern can introduce significant overhead due to the delegation of method calls.

---

### **Real-World Examples of the Decorator Pattern**
1. **Java I/O Streams**:
   - `BufferedReader` wraps a `Reader` to add buffering capabilities.
   - Example:
     ```java
     Reader reader = new BufferedReader(new FileReader("file.txt"));
     ```

2. **GUI Frameworks**:
   - Adding scrollbars or borders to GUI components using decorators.

3. **Logging Libraries**:
   - Wrapping loggers with additional functionality like timestamping or formatting.

---

The **Decorator Pattern** is a powerful and flexible way to extend object behavior dynamically without modifying existing code, adhering to key design principles like composition and the Open/Closed principle.