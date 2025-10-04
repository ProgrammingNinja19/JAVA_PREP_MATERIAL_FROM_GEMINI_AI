Abstraction is one of the four fundamental concepts of Object-Oriented Programming (OOP), along with Encapsulation, Inheritance, and Polymorphism.

Here is a detailed explanation of what abstraction is and how it is achieved in Java.

-----

## 1\. What is Abstraction?

**Abstraction** is the process of **hiding the complex implementation details** and showing only the **essential or necessary information** to the user.

In essence, it focuses on **what an object does** rather than **how it does it**.

### Analogy: Driving a Car

Consider driving a car:

  * **You interact with the essential features:** The steering wheel, accelerator pedal, and brake pedal.
  * **You don't need to know the complex implementation:** You don't need to understand how the internal combustion engine works, the complex circuitry, or the transmission system to drive the car.

The car's design provides an **abstract view** (the controls) while **hiding the complex machinery** (the engine and its components). This simplifies the interaction for the user.

### Key Purposes of Abstraction:

  * **Simplification:** It makes complex systems easier to understand, manage, and use by reducing the visible complexity.
  * **Security:** It protects the internal implementation details from unauthorized access or modification.
  * **Maintainability and Flexibility:** Changes to the internal implementation of an abstracted feature do not affect the code that uses that feature, as long as the exposed interface/signature remains the same.

-----

## 2\. How Abstraction is Achieved in Java

In Java, abstraction is primarily achieved using two mechanisms: **Abstract Classes** and **Interfaces**.

### A. Using Abstract Classes (Partial Abstraction)

An **abstract class** is a class that is declared using the `abstract` keyword.

| Feature | Description |
| :--- | :--- |
| **Instantiation** | Cannot be instantiated (you cannot create an object of an abstract class). |
| **Contents** | Can have a mix of: \<ul\>\<li\>**Abstract Methods:** Methods declared with the `abstract` keyword and **no body** (no implementation).\</li\>\<li\>**Concrete (Non-abstract) Methods:** Regular methods with an implementation (a body).\</li\>\<li\>Fields (variables) and constructors.\</li\>\</ul\>|
| **Inheritance** | Abstract classes are meant to be **extended** by other classes (subclasses). |
| **Implementation** | A subclass that extends an abstract class **must** provide implementations (override) for **all** of its abstract methods, *unless* the subclass is also declared as `abstract`. |
| **Degree of Abstraction**| It achieves **partial abstraction** because it can contain both abstract methods (no implementation) and concrete methods (with implementation). |

**Example:**

```java
// Abstract Class
abstract class Vehicle {
    // Abstract method: no implementation here
    abstract void engineStart(); 

    // Concrete method: has an implementation
    void stop() {
        System.out.println("Vehicle stopped.");
    }
}

// Concrete Subclass
class Car extends Vehicle {
    // Must provide implementation for the abstract method
    @Override
    void engineStart() {
        System.out.println("Car engine started with key.");
    }
}
```

### B. Using Interfaces (100% Abstraction)

An **interface** is a blueprint of a class. It defines a contract for the implementing classes.

| Feature | Description |
| :--- | :--- |
| **Declaration** | Declared using the `interface` keyword. |
| **Instantiation** | Cannot be instantiated. |
| **Contents** | Before Java 8, interfaces contained only abstract methods and public static final fields (constants). Since Java 8, they can also have **default** and **static** methods with implementations, and since Java 9, **private** methods. |
| **Methods** | All methods declared in an interface without a body are **implicitly `public` and `abstract`** (you don't need to use the keywords). |
| **Inheritance** | Classes **implement** an interface using the `implements` keyword. |
| **Implementation** | A class that implements an interface **must** provide implementations for **all** of its abstract methods, *unless* the implementing class is declared as `abstract`. |
| **Degree of Abstraction**| Traditionally considered to achieve **100% abstraction** for behavior (method signatures) because the pre-Java 8 design only allowed method declarations without bodies. |

**Example:**

```java
// Interface
interface Drawable {
    // Abstract method (implicitly public abstract)
    void draw(); 
    
    // Constant (implicitly public static final)
    int BORDER_WIDTH = 5; 
}

// Implementing Class
class Circle implements Drawable {
    // Must provide implementation for the abstract method
    @Override
    public void draw() {
        System.out.println("Drawing a circle with border width: " + BORDER_WIDTH);
    }
}
```

### Summary of Differences:

| Feature | Abstract Class | Interface |
| :--- | :--- | :--- |
| **Keyword** | `abstract class` | `interface` |
| **Methods** | Can have `abstract` and concrete (non-abstract) methods. | Can only have abstract methods (plus `default`, `static`, and `private` methods since Java 8/9). |
| **Variables** | Can have non-`final` and non-`static` variables. | Variables are implicitly `public static final`. |
| **Inheritance** | Uses `extends` (Single Inheritance). | Uses `implements` (A class can implement multiple interfaces). |
| **Constructor** | Can have constructors. | Cannot have constructors. |
| **Abstraction Level**| Partial (0 to 100%). | Traditionally 100% (before Java 8/9 features). |
