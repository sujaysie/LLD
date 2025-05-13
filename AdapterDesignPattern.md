The **Adapter Design Pattern** is a **structural design pattern** that allows two incompatible interfaces or systems to work together. It acts as a bridge between the two, enabling classes with incompatible interfaces to interact without modifying their existing code.

---

### **Key Features of the Adapter Pattern**
1. **Compatibility**: Converts the interface of a class into another interface that the client expects.
2. **Reusability**: Allows the use of existing classes even if their interfaces are incompatible with the current system.
3. **Decoupling**: Decouples the client code from the implementation of the adapted class.

---

### **When to Use the Adapter Pattern?**
- When you want to integrate a class or a system that has an incompatible interface with your application.
- When you want to reuse existing classes without modifying their source code.
- When you need to work with legacy code or third-party libraries that don't match your application's expected interface.

---

### **Components of the Adapter Pattern**
1. **Target Interface**: Defines the domain-specific interface that the client expects to use.
2. **Adaptee**: The class with the existing or incompatible interface that needs to be adapted.
3. **Adapter**: A class that implements the target interface and translates requests from the client to the Adaptee.
4. **Client**: The object that interacts with the Target interface.

---

### **Implementing the Adapter Pattern in Java**

#### Example: Power Adapter
Imagine a scenario where a client needs a `EuropeanSocketInterface`, but we only have a `USASocket` class. We use an adapter to bridge the gap.

---

#### **Step 1: Define the Target Interface**
```java
// Target Interface
public interface EuropeanSocketInterface {
    void plugIn();
}
```

---

#### **Step 2: Implement the Adaptee**
```java
// Adaptee with an incompatible interface
public class USASocket {
    public void connect() {
        System.out.println("Connected using a USA socket.");
    }
}
```

---

#### **Step 3: Create the Adapter**
```java
// Adapter that bridges USASocket and EuropeanSocketInterface
public class SocketAdapter implements EuropeanSocketInterface {
    private USASocket usaSocket;

    public SocketAdapter(USASocket usaSocket) {
        this.usaSocket = usaSocket;
    }

    @Override
    public void plugIn() {
        // Translate the EuropeanSocketInterface's plugIn() call to USASocket's connect() method
        usaSocket.connect();
    }
}
```

---

#### **Step 4: Client Code**
```java
public class Main {
    public static void main(String[] args) {
        // Adaptee
        USASocket usaSocket = new USASocket();

        // Adapter
        EuropeanSocketInterface adapter = new SocketAdapter(usaSocket);

        // Client interacts with the Target Interface
        adapter.plugIn();
    }
}
```

---

### **Types of Adapter Patterns**
1. **Class Adapter** (Uses Inheritance):
   - The adapter inherits from both the target interface and the adaptee.
   - Not commonly used in Java due to single inheritance limitation.

2. **Object Adapter** (Uses Composition):
   - The adapter contains a reference to the adaptee and translates requests.
   - More flexible and commonly used in Java.

---

### **Advantages of Adapter Pattern**
1. **Reusability**: Allows the reuse of existing classes without modification.
2. **Flexibility**: Promotes loose coupling between the client and the adaptee.
3. **Interoperability**: Enables interoperability between incompatible interfaces.

---

### **Disadvantages of Adapter Pattern**
1. **Increased Complexity**: Adds extra classes and layers to the system.
2. **Overhead**: May introduce slight overhead due to delegation in the adapter.

---

### **Real-World Examples of Adapter Pattern**
1. **InputStreamReader and OutputStreamWriter in Java**:
   - These classes act as adapters between byte streams (`InputStream`/`OutputStream`) and character streams (`Reader`/`Writer`).
   ```java
   InputStream inputStream = new FileInputStream("file.txt");
   Reader reader = new InputStreamReader(inputStream);
   ```

2. **Legacy System Integration**:
   - Adapters are used to connect new systems with old legacy systems.

3. **Adapters in GUIs**:
   - Adapter patterns are used to adapt data models to graphical user interface components like lists or tables.

---

The Adapter Pattern is a powerful way to integrate mismatched interfaces seamlessly, improving code maintainability and flexibility.