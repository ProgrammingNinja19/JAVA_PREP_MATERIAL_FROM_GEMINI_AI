Access modifiers (also known as access specifiers) in Java are keywords that set the accessibility or visibility of classes, constructors, methods, and member variables. They are a fundamental tool for achieving **encapsulation** by controlling which parts of your code can access a member.

Java provides **four** main access modifiers, offering different levels of visibility:

| Modifier | Keyword | Accessible from Class | Accessible from Package | Accessible from Subclass (any package) | Accessible from World (other packages) |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **Private** | `private` | **Y** | N | N | N |
| **Default** | *(None)* | **Y** | **Y** | N | N |
| **Protected**| `protected` | **Y** | **Y** | **Y** | N |
| **Public** | `public` | **Y** | **Y** | **Y** | **Y** |

-----

## The Four Access Modifiers Explained

### 1\. `private`

The `private` modifier is the **most restrictive**.

  * **Visibility:** Members declared as `private` are only accessible **within the class where they are declared**. They are completely hidden from all other classes, even classes in the same package.
  * **Purpose:** This is the primary mechanism for **data hiding** and is central to encapsulation. Instance variables (fields) are almost always declared `private` to protect them from direct, unauthorized modification.
  * **Usage:** Typically used for class fields and internal helper methods that should only be called by other methods within that same class.
  * **Example:**
    ```java
    public class MyClass {
        private int sensitiveData; // Accessible only within MyClass
        
        private void internalLogic() {
            // ...
        }
    }
    ```

### 2\. Default (Package-Private)

When you **do not specify any access modifier**, Java applies the **Default** access level (often called **Package-Private**).

  * **Visibility:** Members with default access are accessible **only within their own package**. They are not accessible from any class in a different package.
  * **Purpose:** Used when you want members to be visible to other tightly related classes within the same logical unit (the package), but hidden from the rest of the application.
  * **Usage:** Common for classes or methods that are part of an internal package-level implementation.
  * **Example (Assuming classes are in the same package `com.app.model`):**
    ```java
    package com.app.model;

    class InternalClass { // Default access for the class itself
        String defaultField; // Accessible only within com.app.model
    }
    ```

### 3\. `protected`

The `protected` modifier is a middle ground, primarily used with inheritance.

  * **Visibility:** Members declared as `protected` are accessible in two scenarios:
    1.  Within their **own package** (like default access).
    2.  By **subclasses** (direct or indirect) in **any package**.
  * **Purpose:** It allows a base class (superclass) to share its members with its immediate family (subclasses) while keeping them hidden from the general public (non-subclass classes outside the package).
  * **Usage:** Used for fields or methods that are designed to be overridden or directly used by inheriting classes.
  * **Example:**
    ```java
    public class Parent {
        protected String familySecret; // Accessible in same package AND by subclasses
    }

    // Subclass in a different package can access it
    public class Child extends Parent {
        public void accessSecret() {
            System.out.println(familySecret); // OK, because Child is a subclass
        }
    }
    ```

### 4\. `public`

The `public` modifier is the **least restrictive**.

  * **Visibility:** Members declared as `public` are accessible from **everywhere** in the application—from the same class, the same package, subclasses in other packages, and every other class in the entire project.
  * **Purpose:** It is used to define the public interface or API of a class. These are the components that are intended to be used by other parts of the application or by external developers using your library.
  * **Usage:** Typically used for class definitions, constructors, and public methods (like the getter and setter methods used in encapsulation).
  * **Example:**
    ```java
    public class APIClass { // Public class is visible everywhere
        public static final int MAX_LIMIT = 100;
        
        public String getInfo() { // Public method is callable from everywhere
            return "Some information";
        }
    }
    ```

-----

## Modifiers for Top-Level Classes

An important rule in Java is that **top-level classes** (classes that are not nested inside another class) can only use two access modifiers:

1.  **`public`:** The class is accessible from all packages.
2.  **Default (Package-Private):** The class is accessible only within the package it is declared in.

**Top-level classes cannot be declared as `private` or `protected`.** (Nested or Inner classes, however, can use all four modifiers).
