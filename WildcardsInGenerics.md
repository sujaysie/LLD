### Wildcards in Generics

Wildcards in generics are used to specify unknown types. They are especially useful when you want to specify a type that can be any type or a family of types without locking down a specific one. Wildcards are represented by the `?` symbol and come in three forms:

1. **Unbounded Wildcards (`?`)**
2. **Upper Bounded Wildcards (`? extends Type`)**
3. **Lower Bounded Wildcards (`? super Type`)**

Each of these forms serves a different purpose and is used in different contexts.

### 1. Unbounded Wildcards (`?`)

An unbounded wildcard is used when you want to work with a generic type but don't care about what the actual type is. It represents an unknown type.

**Example:**

```java
import java.util.List;

public class WildcardExample {
    public static void printList(List<?> list) {
        for (Object elem : list) {
            System.out.print(elem + " ");
        }
        System.out.println();
    }
}
```

**Usage:**

```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        List<Integer> intList = Arrays.asList(1, 2, 3, 4, 5);
        List<String> stringList = Arrays.asList("Hello", "World");

        WildcardExample.printList(intList);  // Output: 1 2 3 4 5
        WildcardExample.printList(stringList);  // Output: Hello World
    }
}
```

### 2. Upper Bounded Wildcards (`? extends Type`)

An upper bounded wildcard is used when you want to specify that a type is a subtype of a specific type. It restricts the unknown type to be a specific type or a subclass of that type.

**Syntax:**

```java
public static void processElements(List<? extends Number> list) {
    for (Number num : list) {
        System.out.println(num);
    }
}
```

**Example:**

```java
import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> intList = Arrays.asList(1, 2, 3, 4, 5);
        List<Double> doubleList = Arrays.asList(1.1, 2.2, 3.3);

        processElements(intList);  // Output: 1 2 3 4 5
        processElements(doubleList);  // Output: 1.1 2.2 3.3
    }

    public static void processElements(List<? extends Number> list) {
        for (Number num : list) {
            System.out.println(num);
        }
    }
}
```

In this example, the method `processElements` can accept a list of any type that extends `Number`, including `Integer`, `Double`, etc.

### 3. Lower Bounded Wildcards (`? super Type`)

A lower bounded wildcard is used when you want to specify that a type is a supertype of a specific type. It restricts the unknown type to be a specific type or a superclass of that type.

**Syntax:**

```java
public static void addElements(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    list.add(3);
}
```

**Example:**

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Number> numList = new ArrayList<>();
        addElements(numList);
        System.out.println(numList);  // Output: [1, 2, 3]
    }

    public static void addElements(List<? super Integer> list) {
        list.add(1);
        list.add(2);
        list.add(3);
    }
}
```

In this example, the method `addElements` can accept a list of any type that is a superclass of `Integer`, such as `Number` or `Object`.

### Use Cases for Wildcards

1. **API Flexibility**: Wildcards allow you to write more flexible APIs. For example, if you are writing a method that operates on a list of elements but doesn't need to modify the list, you can use an unbounded wildcard.

2. **Covariance**: Upper bounded wildcards (`? extends Type`) are useful when you want to read from a generic object, such as a list, and you don't want to modify it. This is also known as covariance.

3. **Contravariance**: Lower bounded wildcards (`? super Type`) are useful when you want to write to a generic object, such as adding elements to a list, and you don't care about the specific type. This is also known as contravariance.

### Wildcards vs. Type Parameters

It's important to understand the difference between wildcards and type parameters:

- **Wildcards (`?`)**: Represent an unknown type and are primarily used for flexibility in APIs. Wildcards are used in method parameters.
- **Type Parameters (`<T>`)**: Represent a specific type and are primarily used for defining classes, interfaces, and methods. Type parameters are used when you need a specific type that can be referenced within the class, interface, or method.

**Example:**

Using a wildcard:

```java
public static void printList(List<?> list) {
    for (Object elem : list) {
        System.out.print(elem + " ");
    }
    System.out.println();
}
```

Using a type parameter:

```java
public static <T> void printArray(T[] array) {
    for (T elem : array) {
        System.out.print(elem + " ");
    }
    System.out.println();
}
```

### Conclusion

Wildcards in generics provide a way to handle unknown types and offer flexibility when defining methods that can operate on different types. Understanding the differences between unbounded, upper bounded, and lower bounded wildcards, as well as when to use them, is crucial for writing robust and flexible generic code in Java.