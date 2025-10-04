The **Adapter Design Pattern** is a structural design pattern that allows the interface of an existing class to be used as another interface clients expect. It acts as a bridge between two incompatible interfaces, making them work together without modifying their existing code.

Think of a universal travel adapter: it allows you to plug a device's standard electrical plug (the **Adaptee**) into a completely different wall socket (the **Target Interface**) by converting the physical connection.

## Components of the Adapter Pattern

1.  **Target Interface:** The interface that the client code expects to work with. The client calls methods on this interface.
2.  **Adaptee:** The existing class or object with the incompatible interface that needs to be adapted. It has the required functionality, but its methods/signature do not match the Target Interface.
3.  **Adapter:** A class that implements the **Target Interface** and holds an instance of the **Adaptee** (or extends the Adaptee, depending on the implementation type). It is responsible for translating the requests from the client (using the Target Interface) into calls to the Adaptee's methods.
4.  **Client:** The class that uses the Target Interface to interact with other components.

## Types of Adapter Implementations in Java

There are two primary ways to implement the Adapter pattern in Java:

1.  **Object Adapter:** Uses **composition**. The Adapter class implements the **Target Interface** and holds an instance of the **Adaptee** class. This is the more flexible and preferred approach in Java.
2.  **Class Adapter:** Uses **inheritance**. The Adapter class extends the **Adaptee** class and implements the **Target Interface**. This approach is less common in Java because it requires the Adaptee to be a class (not an interface) and prevents the adapter from extending another class.

-----

## Java Example (Object Adapter)

Let's imagine a scenario where we have an existing system that works with a `NewPlug` interface, but we want to use a legacy device that only works with a `OldSocket` class.

### 1\. Target Interface (The Plug the client expects)

The client code is built to expect this interface:

```java
// Target Interface
public interface NewPlug {
    void supplyPower();
}
```

### 2\. Adaptee (The existing, incompatible class)

This is the class we want to use, but its method signature doesn't match `NewPlug`.

```java
// Adaptee
public class OldSocket {
    public void giveElectricity() {
        System.out.println("Old Socket is giving electricity.");
    }
}
```

### 3\. Adapter (The Bridge)

The adapter implements the `NewPlug` interface (the client's requirement) and contains an instance of the `OldSocket`.

```java
// Adapter (Object Adapter - uses Composition)
public class SocketAdapter implements NewPlug {
    private OldSocket oldSocket;

    // Constructor to inject the Adaptee
    public SocketAdapter(OldSocket oldSocket) {
        this.oldSocket = oldSocket;
    }

    // The Adapter implements the Target method...
    @Override
    public void supplyPower() {
        // ... and translates the call to the Adaptee's method
        System.out.println("Adapter converting the NewPlug request to OldSocket call...");
        oldSocket.giveElectricity();
        System.out.println("Adapter finished conversion.");
    }
}
```

### 4\. Client Code (Demonstration)

The client code only sees and interacts with the `NewPlug` interface. It doesn't know or care that a legacy `OldSocket` is powering it behind the scenes.

```java
// Client
public class Client {
    public static void main(String[] args) {
        // 1. The existing, incompatible system (Adaptee)
        OldSocket legacySocket = new OldSocket();

        // 2. Create the Adapter, wrapping the Adaptee
        NewPlug adapter = new SocketAdapter(legacySocket);

        // 3. The client calls the Target Interface method
        //    (This is now possible thanks to the adapter)
        System.out.println("Client calling supplyPower() on the NewPlug interface...");
        adapter.supplyPower();
    }
}
```

**Output of the Client:**

```
Client calling supplyPower() on the NewPlug interface...
Adapter converting the NewPlug request to OldSocket call...
Old Socket is giving electricity.
Adapter finished conversion.
```

## When to Use the Adapter Pattern

  * **Integrating Third-Party Libraries:** When a third-party library provides valuable functionality but has an interface that doesn't fit your current design.
  * **Reusing Legacy Code:** To reuse an existing class in a new system that requires a different interface, without having to modify the original class.
  * **Creating a Unified Interface:** When you have several existing classes with similar functions but different method signatures, and you want to provide a single, unified interface for clients to interact with.
