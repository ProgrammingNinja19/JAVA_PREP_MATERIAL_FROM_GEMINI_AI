Inheritance is a fundamental concept in Object-Oriented Programming (OOP) in Java. It is a mechanism that allows a new class to **inherit** properties (fields/attributes) and behaviors (methods) from an existing class.

This establishes an **"is-a" relationship** between the two classes. For example, a `Dog` **is a** type of `Animal`.

### Key Concepts in Inheritance

1.  **Superclass (Parent Class/Base Class):**

      * The existing class whose features are being inherited.

2.  **Subclass (Child Class/Derived Class/Extended Class):**

      * The new class that inherits the features of the superclass.
      * It can use the non-private members of the superclass and also add its own unique fields and methods.

### How Inheritance is Achieved in Java

Inheritance in Java is primarily achieved using the **`extends` keyword**.

#### Syntax

The subclass uses the `extends` keyword followed by the name of the superclass in its class definition:

```java
class SuperClass {
    // Fields and methods of the parent class
}

class SubClass extends SuperClass {
    // New fields and methods unique to the child class
    // It automatically has access to the non-private fields and methods of SuperClass
}
```

#### Example

```java
// Superclass
class Animal {
    void eat() {
        System.out.println("This animal eats food.");
    }
}

// Subclass, which inherits from Animal
class Dog extends Animal {
    void bark() {
        System.out.println("The dog barks.");
    }
}

public class TestInheritance {
    public static void main(String[] args) {
        Dog myDog = new Dog();
        
        // Dog object can call the method from the Animal (Superclass)
        myDog.eat(); 
        
        // Dog object can call its own method
        myDog.bark(); 
    }
}

/*
Output:
This animal eats food.
The dog barks.
*/
```

### Purpose and Advantages of Inheritance

1.  **Code Reusability:**

      * By inheriting, the subclass automatically gets all the non-private code from the superclass. You don't have to write (and debug) the same code again, reducing redundancy.

2.  **Method Overriding:**

      * Inheritance allows a subclass to provide a specific implementation for a method that is already defined in its superclass. This is a key mechanism for achieving **Runtime Polymorphism**.

3.  **Establish a Hierarchy:**

      * It helps to structure and organize the code by defining a clear hierarchy and relationship (the "is-a" relationship) between classes.

4.  **Support for Polymorphism:**

      * Inheritance is necessary for Java's polymorphism features, allowing a superclass reference variable to hold an object of any of its subclasses.

### Important Points about Java Inheritance

  * **Access:** A subclass inherits all `public` and `protected` members of its superclass. It does **not** inherit the `private` members. However, it can often access private members indirectly through public/protected methods of the superclass.
  * **Constructors:** Constructors are **not** inherited. However, the subclass constructor must call a superclass constructor (either explicitly using `super()` or implicitly, Java adds a default `super()` call if you don't).
  * **Single Inheritance:** Java classes support **single inheritance** only. A class can extend only one other class.
      * *Note:* Multiple inheritance of **implementation** is not allowed to avoid the "diamond problem," but Java supports multiple inheritance of **type** through **Interfaces**.
  * **Top Class:** Every class in Java, if it doesn't explicitly extend another class, implicitly extends the `Object` class. The `Object` class is at the top of the Java class hierarchy.
