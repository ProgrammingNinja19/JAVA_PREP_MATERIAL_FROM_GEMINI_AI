The **Singleton Design Pattern** is a creational pattern that ensures a class has only one instance and provides a global point of access to that instance.

It is particularly useful for resources that must be unique, like a configuration manager, a logging utility, or a database connection pool.

-----

## Key Components of the Singleton Pattern

To implement the Singleton pattern, you must restrict the instantiation of a class and provide a way to get the single instance. This is typically achieved using three key components:

1.  **Private Constructor:** This prevents other classes from using the `new` keyword to create instances of the Singleton class.
2.  **Private Static Variable:** This holds the single instance of the class. It is `static` so it belongs to the class (not an object) and `private` to restrict external access.
3.  **Public Static Access Method (e.g., `getInstance()`):** This method provides the global access point to the single instance. It checks if an instance already exists; if not, it creates one and returns it. If it exists, it returns the existing instance.

-----

## Java Example: Lazy Initialization (Non-Thread-Safe)

The simplest form of Singleton uses **Lazy Initialization**, meaning the instance is created only when it's first requested.

### 1\. The Singleton Class (`Logger.java`)

```java
public class Logger {
    // 2. Private static variable to hold the single instance
    private static Logger instance;

    // 1. Private constructor to prevent direct instantiation
    private Logger() {
        System.out.println("Logger instance created.");
    }

    // 3. Public static method to get the instance
    public static Logger getInstance() {
        // Only create the instance if it doesn't exist
        if (instance == null) {
            instance = new Logger();
        }
        return instance;
    }

    // Example method for the Singleton's purpose
    public void log(String message) {
        System.out.println("Log Message: " + message);
    }
}
```

### 2\. Client Code (`Demo.java`)

```java
public class Demo {
    public static void main(String[] args) {
        // Get the first instance
        Logger logger1 = Logger.getInstance();
        logger1.log("Application started.");

        // Get the second instance (will return the same object)
        Logger logger2 = Logger.getInstance();
        logger2.log("User logged in.");

        // Check if both references point to the same object
        if (logger1 == logger2) {
            System.out.println("Both logger1 and logger2 refer to the same instance (Singleton successful).");
        } else {
            System.out.println("Different instances were created (Singleton failed).");
        }
        
        System.out.println("logger1 hashcode: " + logger1.hashCode());
        System.out.println("logger2 hashcode: " + logger2.hashCode());
    }
}
```

### Output

```
Logger instance created.
Log Message: Application started.
Log Message: User logged in.
Both logger1 and logger2 refer to the same instance (Singleton successful).
logger1 hashcode: 1554547901
logger2 hashcode: 1554547901
```

**Explanation of the Output:**

  * The message `"Logger instance created."` appears **only once**, confirming the constructor was called only once.
  * The hash codes for `logger1` and `logger2` are identical, proving they are the same object in memory.

-----

## Thread Safety Consideration

The example above is **not thread-safe**. In a multi-threaded environment, if two threads call `getInstance()` simultaneously when `instance` is `null`, both could pass the `if (instance == null)` check, resulting in **two separate instances** being created, violating the Singleton principle.

To fix this, there are several thread-safe approaches, one of the most popular being the **Double-Checked Locking (DCL)**, which balances thread safety with performance:

### Thread-Safe Singleton with Double-Checked Locking

```java
public class ThreadSafeLogger {
    // 'volatile' ensures visibility across threads
    private static volatile ThreadSafeLogger instance;

    private ThreadSafeLogger() {
        // Prevent instantiation via Reflection (optional, more advanced topic)
        if (instance != null) {
            throw new IllegalStateException("Cannot create another instance of a singleton.");
        }
        System.out.println("ThreadSafeLogger instance created.");
    }

    public static ThreadSafeLogger getInstance() {
        // First check (avoids synchronization overhead once instance is created)
        if (instance == null) {
            // Synchronized block (only executes on the first call)
            synchronized (ThreadSafeLogger.class) {
                // Second check (ensures only one thread creates the instance)
                if (instance == null) {
                    instance = new ThreadSafeLogger();
                }
            }
        }
        return instance;
    }

    public void log(String message) {
        System.out.println("Log Message: " + message);
    }
}
```

The use of `volatile` and the two `null` checks (`if (instance == null)`) within and outside the synchronized block makes this a robust and high-performing thread-safe implementation.
