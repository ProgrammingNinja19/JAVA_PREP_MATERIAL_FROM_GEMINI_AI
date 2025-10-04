The **SOLID principles** are a set of five design principles in object-oriented programming, intended to make software designs more understandable, flexible, and maintainable. They were promoted by Robert C. Martin (often referred to as Uncle Bob).

The acronym **SOLID** stands for:

1.  **S**ingle Responsibility Principle (SRP)
2.  **O**pen-Closed Principle (OCP)
3.  **L**iskov Substitution Principle (LSP)
4.  **I**nterface Segregation Principle (ISP)
5.  **D**ependency Inversion Principle (DIP)

Here is a detailed explanation of each principle with examples in Java:

-----

### 1\. Single Responsibility Principle (SRP)

**Definition:** A class should have one, and only one, reason to change.

In simpler terms, a class should have a single, well-defined job or responsibility. If a class has multiple responsibilities, changes to one responsibility might inadvertently affect the others, leading to bugs and making the code harder to maintain and test.

#### Java Example

**Violation of SRP:**
Imagine a `Book` class that handles both the book's properties and saving/loading it to a database.

```java
// Violation of SRP
class Book {
    private String title;
    private String author;

    public Book(String title, String author) {
        this.title = title;
        this.author = author;
    }

    public String getTitle() { return title; }
    public String getAuthor() { return author; }

    // Responsibility 1: Book Properties/Management

    // Responsibility 2: Persistence Management
    public void saveToDatabase() {
        System.out.println("Saving " + title + " by " + author + " to the database.");
        // Database connection and save logic here
    }

    public static Book loadFromDatabase(String title) {
        System.out.println("Loading book " + title + " from the database.");
        // Database connection and load logic here
        return new Book(title, "Some Author");
    }
}
```

**Reason for Violation:** The `Book` class has two responsibilities: managing its data and managing its persistence (database operations). If the persistence logic changes (e.g., switching from a relational database to a NoSQL database or a file system), the `Book` class must change, which violates SRP.

**Adherence to SRP:**
Separate the responsibilities into two distinct classes.

```java
// Adherence to SRP (Responsibility 1: Book Properties/Management)
class Book {
    private String title;
    private String author;

    public Book(String title, String author) {
        this.title = title;
        this.author = author;
    }

    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    
    // Other business logic related only to the Book entity
}

// Adherence to SRP (Responsibility 2: Persistence Management)
class BookRepository {
    public void save(Book book) {
        System.out.println("Saving " + book.getTitle() + " to the database.");
        // Database connection and save logic here
    }

    public Book load(String title) {
        System.out.println("Loading book " + title + " from the database.");
        // Database connection and load logic here
        return new Book(title, "Another Author");
    }
}
```

Now, changes to the persistence logic only affect `BookRepository`, leaving the `Book` class stable.

-----

### 2\. Open-Closed Principle (OCP)

**Definition:** Software entities (classes, modules, functions, etc.) should be **open for extension, but closed for modification.**

This means you should be able to add new functionality without changing the existing, tested source code. This is typically achieved through abstraction (interfaces or abstract classes) and polymorphism.

#### Java Example

**Violation of OCP:**
An `AreaCalculator` class that needs modification every time a new shape is introduced.

```java
// Violation of OCP
class AreaCalculator {
    public double calculateArea(Object shape) {
        if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle) shape;
            return r.width * r.height;
        } else if (shape instanceof Circle) {
            Circle c = (Circle) shape;
            return Math.PI * c.radius * c.radius;
        }
        // PROBLEM: If you add a new shape (e.g., Triangle), you MUST modify this method.
        throw new IllegalArgumentException("Unknown shape");
    }
}

class Rectangle { public double width; public double height; }
class Circle { public double radius; }
```

**Reason for Violation:** The `AreaCalculator` is **closed** to extension (cannot add new shapes without changing it) and **open** for modification (must be modified for new shapes).

**Adherence to OCP:**
Use an interface so that new shapes can be added by implementing the interface, without changing the `AreaCalculator`.

```java
// Base abstraction (open for extension)
interface Shape {
    double area();
}

// Concrete class 1
class Rectangle implements Shape {
    public double width;
    public double height;

    // ... constructor ...

    @Override
    public double area() {
        return width * height;
    }
}

// Concrete class 2 (Adding a new shape doesn't require changing AreaCalculator)
class Circle implements Shape {
    public double radius;

    // ... constructor ...

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

// Adherence to OCP (Closed for modification)
class AreaCalculator {
    // This method is closed for modification. It can handle any new Shape
    // that implements the Shape interface without being changed.
    public double calculateTotalArea(Shape[] shapes) {
        double totalArea = 0;
        for (Shape shape : shapes) {
            totalArea += shape.area(); // Relies on polymorphic behavior
        }
        return totalArea;
    }
}
```

-----

### 3\. Liskov Substitution Principle (LSP)

**Definition:** Subtypes must be substitutable for their base types.

In other words, if a method works with an object of base class $B$, it must also work correctly with an object of derived class $D$ without the calling code knowing the difference. This principle ensures that inheritance is used correctly, and derived classes extend the behavior of the base class without changing its core contract or introducing unexpected side effects.

#### Java Example

**Violation of LSP (Classic Example - Rectangle/Square):**
Let a `Square` be a subclass of `Rectangle`.

```java
// Base class
class Rectangle {
    protected int width;
    protected int height;

    public void setWidth(int width) { this.width = width; }
    public void setHeight(int height) { this.height = height; }
    public int getArea() { return width * height; }
}

// Subclass - Square
class Square extends Rectangle {
    @Override
    public void setWidth(int width) {
        this.width = width;
        this.height = width; // Square constraint
    }

    @Override
    public void setHeight(int height) {
        this.width = height; // Square constraint
        this.height = height;
    }
}

// Test method
class LspViolator {
    public static void testArea(Rectangle r) {
        r.setWidth(5);
        r.setHeight(4);
        // We expect area to be 5 * 4 = 20
        if (r.getArea() != 20) {
            System.out.println("LSP Violation! Expected 20, got " + r.getArea());
        } else {
            System.out.println("Area correct: " + r.getArea());
        }
    }

    public static void main(String[] args) {
        testArea(new Rectangle()); // Works fine: 5 * 4 = 20
        testArea(new Square());    // Fails: sets both to 4 or 5, resulting in 16 or 25
    }
}
```

**Reason for Violation:** The `Square` subtype cannot be substituted for the `Rectangle` base type in the `testArea` function without altering the program's correctness. The act of setting the width/height on a `Square` changes the other dimension, violating the client's expectation of the base `Rectangle` type.

**Adherence to LSP:**
The best way to fix this is to not use inheritance, as a Square is not fundamentally a **subtype** of a Rectangle from a geometric $get/set$ perspective. Instead, both should implement a common `Shape` interface, or the $set$ methods should not be part of the contract.

-----

### 4\. Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use.

This principle suggests that instead of one large, "fat" interface, it is better to have multiple smaller, client-specific interfaces. Segregating interfaces makes the code more robust and prevents a class from having to implement methods it doesn't need or use.

#### Java Example

**Violation of ISP:**
A single, large `Worker` interface.

```java
// Violation of ISP (Fat Interface)
interface Worker {
    void work();
    void eat();
    void manageTeam(); // Not all workers manage teams
}

class Robot implements Worker {
    @Override public void work() { /* ... */ }
    @Override public void eat() { 
        // Forced to implement, but robots don't eat.
        throw new UnsupportedOperationException("Robots don't eat!");
    }
    @Override public void manageTeam() { 
        // Forced to implement, but robots don't manage teams.
        throw new UnsupportedOperationException("Robots don't manage teams!");
    }
}
```

**Reason for Violation:** The `Robot` class is a client of the `Worker` interface but is forced to implement methods (`eat()`, `manageTeam()`) that are irrelevant to its nature, often resulting in dummy or exception-throwing implementations.

**Adherence to ISP:**
Segregate the interface into smaller, role-specific ones.

```java
// Adherence to ISP (Segregated Interfaces)
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Manageable {
    void manageTeam();
}

class HumanWorker implements Workable, Eatable {
    @Override public void work() { /* ... */ }
    @Override public void eat() { /* ... */ }
}

class Robot implements Workable {
    @Override public void work() { /* ... */ }
    // Robot only implements what it needs (Workable) and is not forced
    // to implement Eatable or Manageable.
}
```

-----

### 5\. Dependency Inversion Principle (DIP)

**Definition:**

1.  **High-level modules should not depend on low-level modules. Both should depend on abstractions.**
2.  **Abstractions should not depend on details. Details should depend on abstractions.**

This principle aims to reduce coupling between modules. Instead of a high-level class depending directly on a concrete low-level class, both should depend on an interface or abstract class (the abstraction). This is the core concept behind **Dependency Injection**.

#### Java Example

**Violation of DIP:**
A high-level class (`Car`) directly depends on a concrete low-level class (`PetrolEngine`).

```java
// Violation of DIP
class PetrolEngine { // Low-level module (concrete detail)
    public void start() {
        System.out.println("Petrol Engine started.");
    }
}

class Car { // High-level module
    private final PetrolEngine engine; // Direct dependency on concrete class

    public Car() {
        this.engine = new PetrolEngine(); // Creates its own dependency
    }

    public void start() {
        engine.start();
        System.out.println("Car is running.");
    }
}
```

**Reason for Violation:** The `Car` (high-level) is tightly coupled to `PetrolEngine` (low-level). If we wanted to use a `DieselEngine` or an `ElectricEngine`, we would have to modify the `Car` class.

**Adherence to DIP:**
Introduce an abstraction (interface) and inject the dependency.

```java
// Abstraction (Engine interface)
interface Engine {
    void start();
}

// Low-level module 1 (Detail) depends on abstraction
class PetrolEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Petrol Engine started.");
    }
}

// Low-level module 2 (New Detail) depends on abstraction
class ElectricEngine implements Engine {
    @Override
    public void start() {
        System.out.println("Electric Motor engaged.");
    }
}

// High-level module depends on abstraction (Engine)
class Car {
    private final Engine engine; // Dependency on abstraction

    // Dependency Injection via constructor
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
        System.out.println("Car is running.");
    }
}

class Main {
    public static void main(String[] args) {
        // We can now inject any concrete Engine implementation without
        // changing the Car class.
        Car petrolCar = new Car(new PetrolEngine());
        petrolCar.start();

        Car electricCar = new Car(new ElectricEngine());
        electricCar.start();
    }
}
```

The `Car` class now depends on the `Engine` interface (abstraction), and the low-level modules (`PetrolEngine`, `ElectricEngine`) also depend on this same abstraction. This inverts the dependency, making the system loosely coupled and highly flexible.
