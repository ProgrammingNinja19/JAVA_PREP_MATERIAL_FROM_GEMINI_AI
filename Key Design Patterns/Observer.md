The Observer design pattern is a **behavioral design pattern** that defines a **one-to-many dependency** between objects so that when one object (the **Subject**) changes its state, all its dependents (the **Observers**) are notified and updated automatically.

This pattern is a crucial tool for achieving **loose coupling** in an application, as the Subject doesn't need to know the concrete class of its Observers; it only interacts with them through a common Observer interface.

## Key Participants in the Observer Pattern

1.  **Subject (or Publisher / Observable):**

      * Maintains a list of registered `Observer` objects.
      * Provides methods for attaching (`register`/`subscribe`) and detaching (`unregister`/`unsubscribe`) `Observer` objects.
      * Notifies all registered observers when its internal state changes by calling their `update()` method.

2.  **Observer (or Subscriber / Listener):**

      * Defines an interface for objects that should be notified of changes in the Subject.
      * Typically includes an `update()` method, which the Subject calls to notify the Observer of a state change.

3.  **ConcreteSubject:**

      * The actual object whose state is being observed.
      * Stores the state of interest to the `ConcreteObserver` objects.
      * Implements the `Subject` interface's notification methods.

4.  **ConcreteObserver:**

      * Implements the `Observer` interface.
      * Registers itself with a `ConcreteSubject`.
      * Stores the state that should remain consistent with the Subject's state.
      * Implements the `update()` method to take action upon receiving a notification (e.g., fetching the new state from the Subject and displaying it).

## Java Example: Stock Market Monitoring

Imagine a stock market system where various investors want to be notified immediately when the price of a specific stock changes.

### 1\. The Observer Interface

This interface defines the contract for all objects that want to receive updates.

```java
// Observer Interface
public interface Investor {
    void update(String stockSymbol, double newPrice);
}
```

### 2\. The Subject Interface

This interface defines the methods for managing observers and notifying them.

```java
// Subject Interface
public interface Stock {
    void registerInvestor(Investor investor);
    void unregisterInvestor(Investor investor);
    void notifyInvestors(String stockSymbol, double newPrice);
}
```

### 3\. The Concrete Subject (StockMarket)

This class maintains the list of observers and the subject's state (stock price).

```java
import java.util.ArrayList;
import java.util.List;

public class StockMarket implements Stock {
    private List<Investor> investors = new ArrayList<>();

    @Override
    public void registerInvestor(Investor investor) {
        investors.add(investor);
        System.out.println("Investor registered: " + investor);
    }

    @Override
    public void unregisterInvestor(Investor investor) {
        investors.remove(investor);
        System.out.println("Investor unregistered: " + investor);
    }

    @Override
    public void notifyInvestors(String stockSymbol, double newPrice) {
        System.out.println("\nStock price change detected for " + stockSymbol + ". Notifying investors...");
        for (Investor investor : investors) {
            investor.update(stockSymbol, newPrice);
        }
    }

    // Method to simulate a state change
    public void setStockPrice(String stockSymbol, double newPrice) {
        // ... Logic for setting the price ...
        notifyInvestors(stockSymbol, newPrice); // Triggers the notification
    }
}
```

### 4\. The Concrete Observer (IndividualInvestor)

This class implements the `Investor` interface and defines the specific action to take on an update.

```java
public class IndividualInvestor implements Investor {
    private String name;

    public IndividualInvestor(String name) {
        this.name = name;
    }

    @Override
    public void update(String stockSymbol, double newPrice) {
        System.out.println(name + " received update: " + stockSymbol + " is now $" + newPrice);
        // An investor might place a buy/sell order based on the new price here
    }
    
    @Override
    public String toString() {
        return name;
    }
}
```

### 5\. Client Code (Demonstration)

This demonstrates how the Subject and Observers interact.

```java
public class ObserverDemo {
    public static void main(String[] args) {
        // 1. Create the Subject (Stock Market)
        StockMarket stockMarket = new StockMarket();

        // 2. Create the Observers (Investors)
        Investor alice = new IndividualInvestor("Alice");
        Investor bob = new IndividualInvestor("Bob");

        // 3. Register the Observers with the Subject
        stockMarket.registerInvestor(alice);
        stockMarket.registerInvestor(bob);

        // 4. Simulate a State Change in the Subject (Price change)
        stockMarket.setStockPrice("GOOGL", 1500.50);

        // 5. Unregister an Observer
        stockMarket.unregisterInvestor(alice);

        // 6. Simulate another State Change
        stockMarket.setStockPrice("GOOGL", 1510.75);
    }
}
```

**Output:**

```
Investor registered: Alice
Investor registered: Bob

Stock price change detected for GOOGL. Notifying investors...
Alice received update: GOOGL is now $1500.5
Bob received update: GOOGL is now $1500.5
Investor unregistered: Alice

Stock price change detected for GOOGL. Notifying investors...
Bob received update: GOOGL is now $1510.75
```

## Benefits of the Observer Pattern

  * **Loose Coupling:** The Subject is loosely coupled to its Observers. It only knows that they implement the `Observer` interface, not their concrete class. This makes the system flexible and easy to maintain.
  * **Open/Closed Principle:** You can introduce new types of observers (e.g., a "TradingBotInvestor") without changing the Subject code (`StockMarket`). The `StockMarket` is *closed* for modification but *open* for extension.
  * **Run-time Flexibility:** Observers can be added or removed dynamically at runtime, allowing the dependency structure to change as the application runs.
