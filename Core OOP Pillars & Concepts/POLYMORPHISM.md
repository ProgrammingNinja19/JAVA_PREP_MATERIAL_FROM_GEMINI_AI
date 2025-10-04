Polymorphism, one of the four fundamental concepts of Object-Oriented Programming (OOP), literally means "many forms" (from the Greek words *poly* meaning "many" and *morph* meaning "form").

In Java, **polymorphism** is the ability of an object to take on many forms, allowing a single action to be performed in different ways. This enables you to define one interface (method name) and have multiple implementations.

Polymorphism in Java is broadly classified into two main types:

1.  **Compile-time Polymorphism** (or **Static Polymorphism**)
2.  **Runtime Polymorphism** (or **Dynamic Polymorphism**)

-----

## 1\. Compile-time Polymorphism (Static Binding)

This type of polymorphism is resolved during the compilation phase, meaning the compiler knows exactly which method to call based on the method signature.

### How it is achieved: Method Overloading

Method Overloading is the process of defining multiple methods in the **same class** with the **same name** but with **different parameter lists**.

The methods must be differentiated by:

  * **The number of parameters.**
  * **The data types of the parameters.**
  * **The order of the data types of the parameters.**

The return type of the method is *not* used by the compiler to differentiate overloaded methods.

#### Example (Method Overloading):

```java
class Calculator {
    // Method 1: Adds two integers
    public int add(int a, int b) {
        return a + b;
    }

    // Method 2: Same name, different number of parameters
    public int add(int a, int b, int c) {
        return a + b + c;
    }

    // Method 3: Same name, different data types of parameters
    public double add(double a, double b) {
        return a + b;
    }
}
```

When you call `obj.add(5, 10)`, the compiler immediately knows to call Method 1 because of the two integer arguments. This is why it is called **Compile-time** or **Static** binding.

-----

## 2\. Runtime Polymorphism (Dynamic Binding)

This type of polymorphism is resolved dynamically at runtime, as the Java Virtual Machine (JVM) determines which method to call based on the actual object being referred to, not the reference type.

### How it is achieved: Method Overriding

Method Overriding occurs when a **subclass** (child class) provides a specific implementation for a method that is already defined in its **superclass** (parent class). This mechanism relies on **Inheritance**.

Key points for Method Overriding:

  * The method in the subclass must have the **same name**, **same return type**, and **same parameter list** as the method in the superclass.
  * It is achieved using a **superclass reference variable** that refers to a **subclass object** (known as **Upcasting**).

#### Example (Method Overriding):

```java
class Animal {
    // Base class method
    public void makeSound() {
        System.out.println("The animal makes a generic sound");
    }
}

class Dog extends Animal {
    // Subclass method overrides the base class method
    @Override
    public void makeSound() {
        System.out.println("Dog barks");
    }
}

class Cat extends Animal {
    // Subclass method overrides the base class method
    @Override
    public void makeSound() {
        System.out.println("Cat meows");
    }
}

public class RuntimeExample {
    public static void main(String[] args) {
        // Upcasting: The reference type is Animal, but the object type is Dog
        Animal a1 = new Dog();
        // The JVM, at runtime, looks at the actual object (Dog)
        a1.makeSound(); // Output: Dog barks

        // Upcasting: The reference type is Animal, but the object type is Cat
        Animal a2 = new Cat();
        // The JVM, at runtime, looks at the actual object (Cat)
        a2.makeSound(); // Output: Cat meows
    }
}
```

In the example above, the statement `a1.makeSound()` looks like it should call the `Animal` class method, but because the actual object is a `Dog`, the JVM dynamically decides to execute the `Dog`'s implementation. This dynamic decision at runtime is why it is called **Runtime** or **Dynamic** polymorphism.

### The Role of Interfaces and Abstract Classes

Runtime polymorphism is often facilitated and made powerful by using **Interfaces** and **Abstract Classes**. They define a contract (common methods) that all implementing or extending classes must adhere to, allowing them to be treated uniformly through the interface or abstract class reference, while each provides its unique implementation.
