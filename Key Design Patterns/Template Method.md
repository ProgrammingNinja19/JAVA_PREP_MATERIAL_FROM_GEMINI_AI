The **Template Method** is a behavioral design pattern that defines the **skeleton of an algorithm** in an operation (the "template method"), deferring some steps to subclasses. It lets subclasses redefine certain steps of an algorithm without changing the algorithm's overall structure.

## Detailed Explanation

### 1\. Intent and Concept

  * **Intent:** To implement the invariant (unchanging) parts of an algorithm once in a base class and leave it up to subclasses to implement the behavior that can vary.
  * **The "Template Method":** This is a non-abstract method (often marked as `final` in Java to prevent overriding) in the abstract base class. It defines the sequence of calls to the individual steps that make up the complete algorithm. The order of these steps is fixed and controlled by the base class.
  * **Algorithm Steps (Primitive Operations):** These are the individual methods called by the template method.
      * **Abstract Methods:** These steps **must** be implemented by concrete subclasses, as they represent the varying parts of the algorithm.
      * **Concrete Methods:** These steps have a default implementation in the abstract class and represent the invariant parts of the algorithm, common to all subclasses.
      * **Hook Methods:** These are methods with an empty or default implementation in the abstract class. Subclasses can **optionally** override them to inject custom behavior at specific points in the algorithm.

### 2\. Structure (Participants)

1.  **Abstract Class:**
      * Defines the **template method** (the overall algorithm skeleton), usually declared `final`.
      * Declares the primitive operations (steps) of the algorithm, which can be:
          * `abstract` (to be implemented by subclasses).
          * Concrete (with default implementation).
          * **Hook methods** (concrete with empty or basic implementation, optionally overridden by subclasses).
2.  **Concrete Class:**
      * Implements the abstract primitive operations to provide subclass-specific behavior for the varying steps of the algorithm.
      * Can optionally override hook methods.

### 3\. Benefits

  * **Code Reusability:** Common, invariant parts of the algorithm are implemented once in the abstract base class, avoiding code duplication.
  * **Inversion of Control (Hollywood Principle):** The base class controls the overall flow of the algorithm ("Don't call us, we'll call you"). The subclasses only provide the implementation details for specific steps, but the base class dictates when those steps are executed.
  * **Consistency:** The structure of the algorithm is enforced and remains consistent across all subclasses.

-----

## Java Example: Building a House

Let's imagine a process for building a house. The overall sequence of steps is always the same, but the materials (and thus the implementation of some steps) vary.

### 1\. Abstract Class (HouseTemplate)

This class defines the fixed structure of the house-building algorithm.

```java
// Abstract Class: defines the template method and the steps
public abstract class HouseTemplate {

    // The Template Method: defines the fixed algorithm skeleton
    // Declared 'final' to prevent subclasses from changing the sequence of steps
    public final void buildHouse() {
        buildFoundation();
        buildPillars();
        buildWalls();
        buildRoof();
        // Optional step/hook method
        installWindows();
        System.out.println("House is built successfully.\n");
    }

    // Concrete Step (Invariant) - same for all houses
    private void buildFoundation() {
        System.out.println("Building foundation with cement, iron rods, and sand.");
    }

    // Abstract Steps (Variant) - must be implemented by subclasses
    protected abstract void buildPillars();
    protected abstract void buildWalls();
    protected abstract void buildRoof();
    
    // Hook Method (Optional Step) - can be overridden, default is to do nothing
    protected void installWindows() {
        // Default implementation does nothing or a standard action
    }
}
```

### 2\. Concrete Subclasses

These classes provide specific implementations for the abstract steps.

**A. WoodenHouse**

```java
public class WoodenHouse extends HouseTemplate {

    @Override
    protected void buildPillars() {
        System.out.println("Building Pillars with Wood.");
    }

    @Override
    protected void buildWalls() {
        System.out.println("Building Wooden Walls.");
    }

    @Override
    protected void buildRoof() {
        System.out.println("Building Wooden Roof.");
    }
    
    // Choosing to not override installWindows(), using the default (no windows)
}
```

**B. GlassHouse**

```java
public class GlassHouse extends HouseTemplate {

    @Override
    protected void buildPillars() {
        System.out.println("Building Pillars with Steel coating.");
    }

    @Override
    protected void buildWalls() {
        System.out.println("Building Glass Walls.");
    }

    @Override
    protected void buildRoof() {
        System.out.println("Building Glass Roof.");
    }
    
    // Overriding the hook method for specific behavior
    @Override
    protected void installWindows() {
        System.out.println("Installing large panoramic windows.");
    }
}
```

### 3\. Client Code (Main Method)

The client code interacts only with the template method of the abstract class.

```java
public class HouseBuilder {
    public static void main(String[] args) {
        
        System.out.println("--- Building a Wooden House ---");
        HouseTemplate woodenHouse = new WoodenHouse();
        // The client only calls the template method
        woodenHouse.buildHouse(); 

        System.out.println("--- Building a Glass House ---");
        HouseTemplate glassHouse = new GlassHouse();
        // The client only calls the template method
        glassHouse.buildHouse();
    }
}
```

**Output:**

```
--- Building a Wooden House ---
Building foundation with cement, iron rods, and sand.
Building Pillars with Wood.
Building Wooden Walls.
Building Wooden Roof.
House is built successfully.

--- Building a Glass House ---
Building foundation with cement, iron rods, and sand.
Building Pillars with Steel coating.
Building Glass Walls.
Building Glass Roof.
Installing large panoramic windows.
House is built successfully.
```
