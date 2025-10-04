The **Builder** is a **creational design pattern** that is used to construct a complex object step-by-step. It separates the construction of a complex object from its representation, allowing the same construction process to create different variations of the object.

It is particularly useful when an object has a large number of optional parameters, which, if handled via a traditional constructor, would lead to the "telescoping constructor" anti-pattern (too many constructors with varying parameter counts).

### Key Components

1.  **Product (The Complex Object):** The object being constructed. In the typical implementation, it's often made **immutable** (fields are `final` and there are no public setters).
2.  **Builder (The Construction Tool):** A static nested class inside the Product class (most common implementation) that holds the parameters and provides step-by-step methods to configure the Product.
3.  **`build()` Method:** The final method in the Builder that is called to create and return the fully configured Product object. The Product's private constructor accepts the Builder instance.

### Problem Solved by Builder Pattern

Imagine a `User` class with many potential fields: `firstName`, `lastName` (required), `age`, `phone`, `address`, `email` (optional).

1.  **Telescoping Constructors:** You would need multiple overloaded constructors for every possible combination of optional parameters, quickly becoming unmanageable and error-prone.
    ```java
    // Example of telescoping constructors (bad practice)
    public User(String firstName, String lastName) { ... }
    public User(String firstName, String lastName, int age) { ... }
    public User(String firstName, String lastName, int age, String phone) { ... }
    // ... and so on
    ```
2.  **JavaBeans (Setters):** Using a no-arg constructor and public setters makes the object mutable, and its state can be inconsistent until all setters are called. It also requires the client to call all necessary setters manually.
    ```java
    // Example of JavaBeans pattern (allows mutability and inconsistency)
    User user = new User(); // State is inconsistent initially
    user.setFirstName("John");
    user.setLastName("Doe");
    user.setAge(30); // Forgot to set phone/address
    ```

The Builder pattern solves both issues by providing a clear, readable, and flexible way to construct an immutable object.

### Java Example: `User` Object Builder

Here is the standard implementation of the Builder pattern in Java using a static nested Builder class.

```java
// 1. The Product Class (User) - Immutable
public class User {

    // Required fields (final)
    private final String firstName;
    private final String lastName;

    // Optional fields (final)
    private final int age;
    private final String phone;
    private final String address;

    // Private constructor accepts the Builder object
    // This is the only way to create a User, ensuring immutability
    private User(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.phone = builder.phone;
        this.address = builder.address;
    }

    // Getters only (no public setters)
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
    public String getPhone() { return phone; }
    public String getAddress() { return address; }

    @Override
    public String toString() {
        return "User [firstName=" + firstName + ", lastName=" + lastName + 
               ", age=" + age + ", phone=" + phone + ", address=" + address + "]";
    }

    // 2. The Builder Class (Static Nested Class)
    public static class Builder {
        
        // Same fields as User
        // Required fields (must be passed to Builder's constructor)
        private final String firstName;
        private final String lastName;

        // Optional fields (with default values, if any)
        private int age = 0;
        private String phone = null;
        private String address = null;

        // Constructor for required parameters
        public Builder(String firstName, String lastName) {
            if (firstName == null || lastName == null) {
                throw new IllegalArgumentException("First name and Last name are required.");
            }
            this.firstName = firstName;
            this.lastName = lastName;
        }

        // Methods for setting optional parameters (Fluent Interface)
        // Each method returns 'this' (the Builder object) for chaining
        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public Builder phone(String phone) {
            this.phone = phone;
            return this;
        }

        public Builder address(String address) {
            this.address = address;
            return this;
        }
        
        // 3. The 'build' method creates the final User object
        public User build() {
            // Optional: Perform final validation here before creation
            if (age < 0) {
                 throw new IllegalStateException("Age cannot be negative.");
            }
            return new User(this);
        }
    }
}

// Client Code Example
public class BuilderDemo {
    public static void main(String[] args) {
        
        // Example 1: Creating a User with all optional fields
        User user1 = new User.Builder("John", "Doe")
            .age(30)
            .phone("123-456-7890")
            .address("123 Main St")
            .build();
            
        System.out.println("User 1: " + user1);
        
        // Example 2: Creating a User with only required fields and one optional field
        User user2 = new User.Builder("Jane", "Smith")
            .address("456 Oak Ave")
            // .age() and .phone() are omitted
            .build();
            
        System.out.println("User 2: " + user2);
        
        // Example 3: Creating a User with only required fields
        User user3 = new User.Builder("Bob", "Johnson")
            .build();
            
        System.out.println("User 3: " + user3);
        
        // This process is clear, readable, and the resulting objects are immutable.
    }
}
```

### Advantages of the Builder Pattern

  * **Readability and Clarity:** The process of object creation is self-documenting. The method names clearly state what parameter is being set.
  * **Immutability:** The final object (`User`) can be made immutable (by making its fields `final` and having a private constructor), as its properties are set entirely within the Builder before construction.
  * **Flexibility:** You can create objects with any combination of optional parameters without creating numerous constructors.
  * **Control over Construction:** The `build()` method is a single point of control, allowing for validation of all parameters just before the object is created.
  * **Fluent Interface:** The method chaining (e.g., `.age(30).phone("...").build()`) creates a fluent and intuitive API.
