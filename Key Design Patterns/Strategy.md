The Strategy design pattern is a behavioral pattern that allows you to define a family of algorithms, put each of them into a separate class, and make their objects interchangeable. It enables a client to select an algorithm from a family of algorithms at run-time.

This pattern is also known as the **Policy Pattern**.

### Core Components of the Strategy Pattern

1.  **Strategy Interface (or Abstract Class):**

      * Declares a common interface for all supported algorithms.
      * The **Context** uses this interface to call the specific algorithm implemented by a **Concrete Strategy**.

2.  **Concrete Strategy Classes:**

      * Implement the **Strategy Interface** (or inherit from the abstract class).
      * Each class contains a specific implementation of an algorithm.

3.  **Context Class:**

      * Maintains a reference to a **Strategy** object.
      * The **Context** may define an interface that lets the **Strategy** access its data.
      * It delegates the algorithm execution to the referenced **Strategy** object. The client provides the appropriate **Concrete Strategy** to the **Context**, usually through the constructor or a setter method.

### When to Use the Strategy Pattern

  * When you have multiple related algorithms, and you want to switch between them at runtime.
  * When a class has a large conditional statement (`if/else` or `switch`) that selects between different related behaviors. The Strategy pattern replaces these conditionals with delegation.
  * When you want to isolate the algorithm's implementation details from the client code that uses it.
  * When you need different variants of an algorithm, and the client should be able to choose one easily.

-----

### Java Example: Shopping Cart Payment Strategies

Let's illustrate the Strategy pattern by modeling a shopping cart that can use different payment methods (algorithms).

#### 1\. Strategy Interface

Define the common interface for all payment methods.

```java
// Strategy Interface
public interface PaymentStrategy {
    void pay(int amount);
}
```

#### 2\. Concrete Strategy Classes

Implement the payment algorithms.

**A. Credit Card Strategy**

```java
// Concrete Strategy A
public class CreditCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;
    private String cvv;
    private String dateOfExpiry;

    public CreditCardStrategy(String name, String cardNumber, String cvv, String dateOfExpiry) {
        this.name = name;
        this.cardNumber = cardNumber;
        this.cvv = cvv;
        this.dateOfExpiry = dateOfExpiry;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid with Credit Card (ending in " + cardNumber.substring(cardNumber.length() - 4) + ")");
        // Actual credit card processing logic goes here
    }
}
```

**B. PayPal Strategy**

```java
// Concrete Strategy B
public class PaypalStrategy implements PaymentStrategy {
    private String emailId;
    private String password;

    public PaypalStrategy(String emailId, String password) {
        this.emailId = emailId;
        this.password = password;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid using PayPal account: " + emailId);
        // Actual PayPal processing logic goes here
    }
}
```

#### 3\. Context Class

The shopping cart is the context. It delegates the payment process to the selected `PaymentStrategy`.

```java
// Context Class
public class ShoppingCart {
    private List<Item> items;
    private int totalCost;

    public ShoppingCart() {
        this.items = new ArrayList<>();
        this.totalCost = 0;
    }

    public void addItem(Item item) {
        this.items.add(item);
        this.totalCost += item.getPrice();
    }

    public int calculateTotal() {
        // Simple calculation for example purposes
        return totalCost;
    }

    // The core method that uses the Strategy
    public void checkout(PaymentStrategy paymentMethod) {
        int amount = calculateTotal();
        paymentMethod.pay(amount); // Delegation to the selected strategy
    }
    
    // Helper class for items (for completeness)
    private static class Item {
        private String upcCode;
        private int price;

        public Item(String upcCode, int price) {
            this.upcCode = upcCode;
            this.price = price;
        }

        public int getPrice() {
            return price;
        }
    }
}
```

*Note: The `Item` class is included inside `ShoppingCart` for simplicity, but in a real application, it would be a separate class.*

#### 4\. Client Code

The client decides which strategy to use at runtime.

```java
public class StrategyPatternDemo {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        
        // Add items to the cart
        cart.addItem(new ShoppingCart.Item("1234", 50));
        cart.addItem(new ShoppingCart.Item("5678", 120));

        // Total cost is 170

        // Client chooses the first strategy (Paypal)
        PaymentStrategy paypalPayment = new PaypalStrategy("jane.doe@example.com", "mysecurepwd");
        System.out.println("Attempting to pay with PayPal...");
        cart.checkout(paypalPayment);
        
        System.out.println("-------------------------");

        // Client chooses a different strategy for the next transaction
        ShoppingCart newCart = new ShoppingCart();
        newCart.addItem(new ShoppingCart.Item("9999", 200));

        PaymentStrategy creditCardPayment = new CreditCardStrategy("Jane Doe", "1234567890123456", "123", "10/25");
        System.out.println("Attempting to pay with Credit Card...");
        newCart.checkout(creditCardPayment);
    }
}
```

**Output:**

```
Attempting to pay with PayPal...
170 paid using PayPal account: jane.doe@example.com
-------------------------
Attempting to pay with Credit Card...
200 paid with Credit Card (ending in 3456)
```

### Benefits of the Strategy Pattern

1.  **Open/Closed Principle:** The context (e.g., `ShoppingCart`) is *closed* for modification but *open* for extension. You can introduce a new payment method (e.g., `BankTransferStrategy`) without changing the `ShoppingCart` class.
2.  **Encapsulation:** Algorithms are completely encapsulated within their own classes, making them easier to understand, test, and debug.
3.  **Flexibility:** The client can switch between strategies at runtime, making the application highly flexible.
4.  **Avoids Conditional Logic:** It eliminates the need for complex conditional statements (`if/else`, `switch`) in the Context to select the behavior, leading to cleaner code.
