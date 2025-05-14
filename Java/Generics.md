Generics in Java are a mechanism that allows you to write classes, interfaces, and methods with a placeholder for types. This provides stronger type checks at compile time and reduces the need for typecasting, making the code safer and easier to read. Generics are widely used in the Java Collections Framework, but they can be used in any part of your Java code.

### Why Use Generics?

1. **Type Safety**: Generics provide compile-time type checking, which catches potential errors early in the development process.
2. **Code Reusability**: You can write a single class or method that works with different data types.
3. **Elimination of Casts**: Generics reduce the need for explicit type casting.

### Generic Classes

A generic class is defined with one or more type parameters. Here's the syntax:

```java
public class GenericClass<T> {
    private T data;

    public GenericClass(T data) {
        this.data = data;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

**Example:**

```java
public class Main {
    public static void main(String[] args) {
        GenericClass<Integer> intObj = new GenericClass<>(5);
        System.out.println("Integer Value: " + intObj.getData());

        GenericClass<String> stringObj = new GenericClass<>("Hello");
        System.out.println("String Value: " + stringObj.getData());
    }
}
```

### Generic Methods

A generic method can be defined with type parameters in the method signature. Here's the syntax:

```java
public class GenericMethodExample {
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }
}
```

**Example:**

```java
public class Main {
    public static void main(String[] args) {
        Integer[] intArray = {1, 2, 3, 4, 5};
        String[] stringArray = {"Hello", "World"};

        GenericMethodExample.printArray(intArray);  // Output: 1 2 3 4 5
        GenericMethodExample.printArray(stringArray);  // Output: Hello World
    }
}
```

### Bounded Type Parameters

You can restrict the types that can be used as type arguments using bounded type parameters. This is done using the `extends` keyword.

**Example:**

```java
public class GenericBoundedType<T extends Number> {
    private T data;

    public GenericBoundedType(T data) {
        this.data = data;
    }

    public void display() {
        System.out.println("Data: " + data);
    }
}
```

**Usage:**

```java
public class Main {
    public static void main(String[] args) {
        GenericBoundedType<Integer> intObj = new GenericBoundedType<>(5);
        intObj.display();  // Output: Data: 5

        // The following will cause a compile-time error because String is not a subtype of Number
        // GenericBoundedType<String> stringObj = new GenericBoundedType<>("Hello");
    }
}
```

### Generic Interfaces

You can also define interfaces with generics.

**Example:**

```java
public interface GenericInterface<T> {
    void setValue(T value);
    T getValue();
}

public class GenericInterfaceImpl<T> implements GenericInterface<T> {
    private T value;

    @Override
    public void setValue(T value) {
        this.value = value;
    }

    @Override
    public T getValue() {
        return value;
    }
}
```

**Usage:**

```java
public class Main {
    public static void main(String[] args) {
        GenericInterfaceImpl<String> obj = new GenericInterfaceImpl<>();
        obj.setValue("Hello");
        System.out.println("Value: " + obj.getValue());  // Output: Value: Hello
    }
}
```

### Wildcards in Generics

Wildcards are used to refer to an unknown type. The `?` symbol represents a wildcard in generics. There are three types of wildcards:

1. **Unbounded Wildcard**: `?`
2. **Bounded Wildcard with Upper Bound**: `? extends Type`
3. **Bounded Wildcard with Lower Bound**: `? super Type`

**Example:**

```java
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
public class Main {
    public static void main(String[] args) {
        List<Integer> intList = Arrays.asList(1, 2, 3, 4, 5);
        List<String> stringList = Arrays.asList("Hello", "World");

        WildcardExample.printList(intList);  // Output: 1 2 3 4 5
        WildcardExample.printList(stringList);  // Output: Hello World
    }
}
```

### Conclusion

Generics in Java provide a powerful way to write flexible, reusable, and type-safe code. By understanding how to use generic classes, methods, interfaces, bounded type parameters, and wildcards, you can leverage the full potential of Java generics in your applications.