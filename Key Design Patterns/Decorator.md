The Decorator design pattern is a **structural** pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class.

It provides a flexible alternative to subclassing for extending functionality. Instead of creating a multitude of subclasses to handle every possible combination of features, you wrap the object with one or more "decorator" objects.

The pattern adheres to the **Open/Closed Principle**: classes should be *open for extension* but *closed for modification*.

### Key Components of the Decorator Pattern

1.  **Component (Interface/Abstract Class):** Defines the interface for objects that can be decorated. Both the concrete components and the decorators must implement this.
2.  **Concrete Component:** The original object (the core functionality) to which new responsibilities or features can be attached. It implements the Component interface.
3.  **Decorator (Abstract Decorator):** An abstract class that implements the Component interface. It holds a reference to a Component object (the object being decorated). It acts as the base class for all concrete decorators.
4.  **Concrete Decorators:** Classes that extend the abstract Decorator and add specific, new responsibilities to the component. They perform their own behavior, then typically delegate the rest of the work to the wrapped component.

### Java Example: Coffee Shop

Let's imagine a coffee shop where you start with a basic coffee and can add various optional toppings (decorators) like Milk, Sugar, or Caramel. We want to calculate the total cost and get a description of the final order.

#### 1\. Component Interface: `Coffee`

```java
public interface Coffee {
    String getDescription();
    double getCost();
}
```

#### 2\. Concrete Component: `BasicCoffee`

```java
public class BasicCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Basic Coffee";
    }

    @Override
    public double getCost() {
        return 5.0;
    }
}
```

#### 3\. Decorator Abstract Class: `CoffeeDecorator`

```java
public abstract class CoffeeDecorator implements Coffee {
    // Reference to the object being decorated
    protected Coffee decoratedCoffee; 

    public CoffeeDecorator(Coffee decoratedCoffee) {
        this.decoratedCoffee = decoratedCoffee;
    }

    // Delegates the call to the wrapped object by default
    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription();
    }

    // Delegates the call to the wrapped object by default
    @Override
    public double getCost() {
        return decoratedCoffee.getCost();
    }
}
```

#### 4\. Concrete Decorators: `MilkDecorator` and `CaramelDecorator`

```java
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Milk"; // Adds Milk to the description
    }

    @Override
    public double getCost() {
        return super.getCost() + 1.5; // Adds the cost of Milk
    }
}
```

```java
public class CaramelDecorator extends CoffeeDecorator {
    public CaramelDecorator(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Caramel"; // Adds Caramel to the description
    }

    @Override
    public double getCost() {
        return super.getCost() + 2.0; // Adds the cost of Caramel
    }
}
```

#### 5\. Client Code (Demonstration)

```java
public class CoffeeShop {
    public static void main(String[] args) {
        // 1. A basic coffee
        Coffee simpleCoffee = new BasicCoffee();
        System.out.println("Order 1: " + simpleCoffee.getDescription() + " - $" + simpleCoffee.getCost());
        // Output: Basic Coffee - $5.0

        System.out.println("-------------------------");

        // 2. A coffee decorated with Milk
        Coffee milkCoffee = new MilkDecorator(new BasicCoffee());
        System.out.println("Order 2: " + milkCoffee.getDescription() + " - $" + milkCoffee.getCost());
        // Output: Basic Coffee, Milk - $6.5

        System.out.println("-------------------------");

        // 3. A coffee decorated with Milk and then Caramel (dynamically layered)
        Coffee customizedCoffee = new BasicCoffee();
        customizedCoffee = new MilkDecorator(customizedCoffee); // Wrap basic coffee with milk
        customizedCoffee = new CaramelDecorator(customizedCoffee); // Wrap milk coffee with caramel
        
        System.out.println("Order 3: " + customizedCoffee.getDescription() + " - $" + customizedCoffee.getCost());
        // Output: Basic Coffee, Milk, Caramel - $8.5
    }
}
```

### Benefits of the Decorator Pattern

1.  **Flexibility:** You can mix and match decorators in any order and combination at runtime to create complex objects with unique behaviors.
2.  **Open/Closed Principle:** You can extend an object's functionality without modifying its core class or other existing decorators. You just add a new `ConcreteDecorator` class.
3.  **Avoids Class Explosion:** Without the decorator pattern, if you had $N$ base components and $M$ features, you might need $N \times 2^M$ subclasses (e.g., `BasicCoffee`, `MilkCoffee`, `CaramelCoffee`, `MilkCaramelCoffee`, etc.). The decorator pattern avoids this unmanageable class hierarchy.

### Real-World Use in Java

The most classic and widely-used example of the Decorator Pattern in Java is in the **Java I/O library**:

  * The abstract **Component** is the `InputStream`, `OutputStream`, `Reader`, or `Writer` class.
  * **Concrete Components** are classes like `FileInputStream` or `StringReader` (the source of the data).
  * **Concrete Decorators** are classes like `BufferedInputStream`, `DataInputStream`, or `GZIPInputStream`. These classes wrap an existing `InputStream` and add new functionality (like buffering, reading complex data types, or compression/decompression).
