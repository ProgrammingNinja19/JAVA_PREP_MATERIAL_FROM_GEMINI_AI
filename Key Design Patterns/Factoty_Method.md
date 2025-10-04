The Factory Method is a **creational design pattern** that defines an interface or abstract class for creating an object, but lets subclasses decide which class to instantiate. The pattern defers object instantiation to subclasses.

In essence, it replaces direct object construction (using the `new` operator) with calls to a special **factory method**.

### Core Concepts and Intent

1.  **Decoupling:** It decouples the client code (the code that uses the object) from the concrete product classes. The client only interacts with the product interface or abstract class.
2.  **Open/Closed Principle:** It allows a system to be open for extension but closed for modification. You can introduce new product types by creating new concrete creators without altering the existing client or creator code.
3.  **Deferring Instantiation:** The object creation logic is delegated to subclasses. The superclass (Creator) defines the structure of the product and the general process that uses the product, but leaves the specific creation step to its subclasses (Concrete Creators).

### Structure (Components)

| Component | Role |
| :--- | :--- |
| **Product** | An interface or abstract class defining the methods that the objects created by the factory will implement. |
| **ConcreteProduct** | Actual classes that implement the **Product** interface. These are the objects that the factory method creates. |
| **Creator** | An abstract class or interface that declares the **Factory Method**, which returns an object of type **Product**. It may also contain logic that relies on the product returned by the factory method. |
| **ConcreteCreator** | Subclasses of **Creator** that override the **Factory Method** to return an instance of a specific **ConcreteProduct**. |

-----

### Java Example: Vehicle Production

Let's illustrate the Factory Method pattern with an example for creating different types of vehicles (Product).

#### 1\. Product Interface

This interface defines the common contract for all vehicles.

```java
// 1. Product Interface
interface Vehicle {
    void assemble();
}
```

#### 2\. Concrete Products

These are the specific classes the factory will instantiate.

```java
// 2. Concrete Product A
class Car implements Vehicle {
    @Override
    public void assemble() {
        System.out.println("Assembling a Car: Adding four wheels and an engine.");
    }
}

// 2. Concrete Product B
class Bike implements Vehicle {
    @Override
    public void assemble() {
        System.out.println("Assembling a Bike: Adding two wheels and pedals.");
    }
}
```

#### 3\. Creator Abstract Class

This abstract class defines the general manufacturing process and declares the abstract factory method.

```java
// 3. Creator Abstract Class
abstract class VehicleFactory {

    // The core business logic method (may use the factory method)
    public Vehicle orderVehicle() {
        Vehicle vehicle = createVehicle(); // <-- The Factory Method call
        System.out.println("Starting production of a new vehicle...");
        vehicle.assemble();
        System.out.println("Vehicle production completed.");
        return vehicle;
    }

    // The Factory Method - subclasses MUST implement this to decide which
    // ConcreteProduct to return.
    public abstract Vehicle createVehicle();
}
```

#### 4\. Concrete Creators

These subclasses implement the `createVehicle()` factory method to return a specific product.

```java
// 4. Concrete Creator A
class CarFactory extends VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Car();
    }
}

// 4. Concrete Creator B
class BikeFactory extends VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Bike();
    }
}
```

#### 5\. Client Code (Usage)

The client code uses the creator classes to get the product, but it only relies on the abstract `Vehicle` and `VehicleFactory` types.

```java
public class FactoryMethodDemo {
    public static void main(String[] args) {
        // Client uses the CarFactory
        VehicleFactory carProducer = new CarFactory();
        Vehicle myCar = carProducer.orderVehicle(); // CarFactory decides to create a Car

        System.out.println("\n---");

        // Client uses the BikeFactory
        VehicleFactory bikeProducer = new BikeFactory();
        Vehicle myBike = bikeProducer.orderVehicle(); // BikeFactory decides to create a Bike
    }
}
```

**Output:**

```
Starting production of a new vehicle...
Assembling a Car: Adding four wheels and an engine.
Vehicle production completed.

---
Starting production of a new vehicle...
Assembling a Bike: Adding two wheels and pedals.
Vehicle production completed.
```

### Advantages

  * **Loose Coupling:** The client code is decoupled from the concrete product classes (`Car`, `Bike`). It only interacts with the `Vehicle` interface.
  * **Single Responsibility Principle:** The code for creating the product is moved to one specific location (the factory method), isolating it from the main business logic.
  * **Extensibility (Open/Closed Principle):** To add a new vehicle type (e.g., `Truck`), you only need to create a new `Truck` concrete product and a corresponding `TruckFactory` concrete creator. You do **not** need to modify the existing `Vehicle`, `Car`, `Bike`, or `VehicleFactory` code.
