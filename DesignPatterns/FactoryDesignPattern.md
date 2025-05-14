The **Factory Design Pattern** is a creational design pattern that provides a way to create objects without specifying the exact class of the object that will be created. This pattern delegates the responsibility of instantiating objects to a factory class, which allows for greater flexibility and abstraction in the code.

---

### **Key Features of Factory Design Pattern**
1. **Encapsulation of Object Creation**: The client code does not need to know the specific class or the details of object creation.
2. **Abstraction**: The client interacts with an interface or abstract class, while the factory handles the instantiation.
3. **Decoupling**: The client code is decoupled from the specific classes it uses, making the code more flexible and easier to modify or extend.

---

### **Types of Factory Patterns**
1. **Simple Factory**: A basic implementation where a single factory class handles object creation.
2. **Factory Method**: Uses a method (often abstract) in a base class or interface to create objects, allowing subclasses to define the instantiation logic.
3. **Abstract Factory**: Provides an interface for creating families of related or dependent objects without specifying their concrete classes.

---

### **When to Use the Factory Pattern**
- When the exact type of object to be created is determined dynamically at runtime.
- When the creation process involves complex logic that you don't want to expose to the client.
- When you need to centralize object creation to manage dependencies or configurations.

---

### **Implementing Factory Method Pattern in Java**

#### Example: Vehicle Factory

---

#### **Step 1: Define a Common Interface or Abstract Class**
```java
// Common interface for vehicles
public interface Vehicle {
    void start();
}
```

---

#### **Step 2: Implement Concrete Classes**
```java
// Concrete implementation: Car
public class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car is starting...");
    }
}

// Concrete implementation: Bike
public class Bike implements Vehicle {
    @Override
    public void start() {
        System.out.println("Bike is starting...");
    }
}
```

---

#### **Step 3: Create the Factory Class**
```java
// Factory class to create Vehicle objects
public class VehicleFactory {
    public static Vehicle getVehicle(String vehicleType) {
        if (vehicleType == null) {
            return null;
        }
        switch (vehicleType.toLowerCase()) {
            case "car":
                return new Car();
            case "bike":
                return new Bike();
            default:
                throw new IllegalArgumentException("Unknown vehicle type: " + vehicleType);
        }
    }
}
```

---

#### **Step 4: Use the Factory in Client Code**
```java
public class Main {
    public static void main(String[] args) {
        // Create a Car
        Vehicle car = VehicleFactory.getVehicle("car");
        car.start();

        // Create a Bike
        Vehicle bike = VehicleFactory.getVehicle("bike");
        bike.start();
    }
}
```

---

### **Abstract Factory Pattern**
The Abstract Factory pattern is an extension of the Factory Method. It allows creating families of related objects, ensuring they are compatible with each other.

For example, in a GUI application, you might have:
- **Abstract Factory**: `WidgetFactory`
- **Concrete Factories**: `WindowsWidgetFactory` and `MacWidgetFactory`
- **Product Families**: `Button`, `Checkbox`, etc.

---

### **Advantages of Factory Pattern**
1. **Encapsulation**: Encapsulates object creation logic, improving code maintainability.
2. **Decoupling**: Reduces dependencies between client code and concrete classes.
3. **Extensibility**: Easy to add new product types without modifying existing code.

---

### **Disadvantages of Factory Pattern**
1. **Increased Complexity**: Introduces additional classes and methods.
2. **Not Always Necessary**: May be overkill for simple object creation.

---

The Factory Pattern is widely used in frameworks and libraries, such as:
- **JDBC in Java**: The `DriverManager.getConnection()` method acts as a factory for creating `Connection` objects.
- **Spring Framework**: The `BeanFactory` and `ApplicationContext` in Spring are examples of factories for creating and managing beans.

