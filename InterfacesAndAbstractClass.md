With the introduction of default methods in interfaces, the distinction between abstract classes and interfaces in Java has blurred somewhat, but key differences remain. Here’s a breakdown of the differences between the two:

1. **Multiple Inheritance**:
    - **Interfaces**: Java allows a class to implement multiple interfaces. This supports a form of multiple inheritance.
    - **Abstract Classes**: A class can extend only one abstract class due to Java's single inheritance model.

2. **Default Methods**:
    - **Interfaces**: Can have default methods with implementation, which allows them to provide some behavior while still requiring implementing classes to provide the rest.
    - **Abstract Classes**: Can have fully implemented methods. This has always been the case.

3. **Instance Variables**:
    - **Interfaces**: Can only have static and final (constant) variables.
    - **Abstract Classes**: Can have instance variables and these can be non-final, allowing the state to be maintained.

4. **Constructors**:
    - **Interfaces**: Cannot have constructors.
    - **Abstract Classes**: Can have constructors which can be used to initialize state.

5. **Type of Methods**:
    - **Interfaces**: Methods in interfaces are implicitly public. However, since Java 9, interfaces can also have private methods.
    - **Abstract Classes**: Methods in abstract classes can have any access modifier (public, protected, private).

6. **Use Case**:
    - **Interfaces**: Ideal for defining a contract that multiple classes can implement, even if they are not related to each other.
    - **Abstract Classes**: Best suited when classes are closely related and share common state or behavior.

7. **Inheritance Design**:
    - **Interfaces**: Used to define capabilities that can be added to a class, regardless of where that class sits in the class hierarchy.
    - **Abstract Classes**: Used to share code among closely related classes.

### Example:

**Interface with default method:**
```java
interface Vehicle {
    void start();
    default void stop() {
        System.out.println("Vehicle is stopping");
    }
}

class Car implements Vehicle {
    public void start() {
        System.out.println("Car is starting");
    }
}

class Bicycle implements Vehicle {
    public void start() {
        System.out.println("Bicycle is starting");
    }
}
```

**Abstract class:**
```java
abstract class Animal {
    String name;
    
    Animal(String name) {
        this.name = name;
    }
    
    abstract void makeSound();
    
    void sleep() {
        System.out.println(name + " is sleeping");
    }
}

class Dog extends Animal {
    Dog(String name) {
        super(name);
    }
    
    void makeSound() {
        System.out.println("Woof");
    }
}

class Cat extends Animal {
    Cat(String name) {
        super(name);
    }
    
    void makeSound() {
        System.out.println("Meow");
    }
}
```

In summary, while interfaces have gained some capabilities that abstract classes traditionally held, the choice between using an interface or an abstract class depends on the design requirements of the application. Interfaces are preferred for defining capabilities that can be mixed into any class, while abstract classes are better for sharing common code and state among related classes.