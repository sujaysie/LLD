The **Singleton Design Pattern** is a creational design pattern that ensures a class has only one instance and provides a global access point to that instance. It is often used when exactly one object is needed to coordinate actions across the system, like in logging, configuration management, or thread pools.

### **Key Features of Singleton Pattern:**
1. **Single Instance:** Ensures only one instance of the class exists.
2. **Global Access Point:** Provides a way to access the single instance.
3. **Controlled Instantiation:** Prevents other classes from creating new instances.

---

### **Implementing Singleton in Java**

#### 1. **Eager Initialization** (Simple but inflexible)
```java
public class Singleton {
    // Create the single instance at the time of class loading
    private static final Singleton INSTANCE = new Singleton();

    // Private constructor to prevent instantiation
    private Singleton() {}

    // Public method to provide access to the instance
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```
- **Pros:** Simple and thread-safe.
- **Cons:** Instance is created even if it might not be used.

---

#### 2. **Lazy Initialization** (Better resource management, but not thread-safe)
```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
- **Pros:** Instance is created only when needed.
- **Cons:** Not thread-safe.

---

#### 3. **Thread-Safe Singleton** (With synchronization)
```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
- **Pros:** Thread-safe.
- **Cons:** Synchronization can be a performance bottleneck.

---

#### 4. **Double-Checked Locking** (Efficient thread-safe implementation)
```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
- **Pros:** Thread-safe and efficient.
- **Cons:** Requires the `volatile` keyword (introduced in Java 1.5).

---

#### 5. **Bill Pugh Singleton Implementation** (Best practice)
```java
public class Singleton {
    private Singleton() {}

    // Inner static helper class
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```
- **Pros:** Lazy initialization, thread-safe, efficient.
- **Cons:** Slightly more complex.

---

### **Ensuring True Singleton**
To make a class *truly singleton*, you need to:
1. **Private Constructor:** Prevent instantiation from outside the class.
2. **Static Instance:** Use a static variable to hold the single instance.
3. **Global Access Point:** Provide a static method to access the instance.
4. **Handle Serialization:** Use `readResolve()` to prevent breaking the singleton during deserialization.
   ```java
   protected Object readResolve() {
       return getInstance();
   }
   ```
5. **Prevent Cloning:** Override the `clone()` method to prevent creating a new instance.
   ```java
   @Override
   protected Object clone() throws CloneNotSupportedException {
       throw new CloneNotSupportedException("Singleton cannot be cloned");
   }
   ```