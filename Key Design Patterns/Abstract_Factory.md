The **Abstract Factory** is a **creational design pattern** that provides an interface for creating **families of related or dependent objects** without specifying their concrete classes.

It is often described as a "factory of factories" because its primary job is to create a full set of abstract factory objects, and each concrete factory then produces a family of products that belong to a single theme or variant.

### Core Concepts and Intent

1.  **Families of Products:** The key difference from the Factory Method pattern is that the Abstract Factory focuses on creating *multiple*, *related* products that are designed to work together.
2.  **Consistency:** It guarantees that the client always uses compatible products from the same family. For example, a modern chair and a modern table must always be created together, never a modern chair and a Victorian table.
3.  **Decoupling from Concrete Classes:** The client code interacts only with the abstract interfaces for both the factory and the products. This allows you to swap entire product families (e.g., switch from a "Dark Theme" family to a "Light Theme" family) simply by instantiating a different concrete factory object, without changing any client code that uses the products.

### Structure (Components)

| Component | Role |
| :--- | :--- |
| **Abstract Product** (e.g., `Chair`, `Table`) | Interfaces or abstract classes that declare the methods for a specific type of product. |
| **Concrete Product** (e.g., `ModernChair`, `VictorianTable`) | Concrete implementations of the Abstract Products, grouped by a specific variant (family). |
| **Abstract Factory** (e.g., `FurnitureFactory`) | An interface or abstract class that declares a set of creation methods, one for each Abstract Product in the family. |
| **Concrete Factory** (e.g., `ModernFactory`, `VictorianFactory`) | Implementations of the Abstract Factory. Each one produces a full set of Concrete Products belonging to a single variant (family). |
| **Client** | Code that uses the Abstract Factory and Abstract Products. It does not know which concrete classes are being used. |

-----

### Java Example: Cross-Platform GUI Elements

Imagine we are building a GUI application that needs to look native on different operating systems (Windows and Mac).

#### 1\. Abstract Products

Define interfaces for the different GUI components (the products).

```java
// Abstract Product A
interface Button {
    void paint();
}

// Abstract Product B
interface Checkbox {
    void paint();
}
```

#### 2\. Concrete Products

Implement the products for the specific variants (Windows and Mac).

```java
// Concrete Product A1 (Windows Family)
class WinButton implements Button {
    @Override
    public void paint() {
        System.out.println("Rendering a Windows-style Button.");
    }
}

// Concrete Product B1 (Windows Family)
class WinCheckbox implements Checkbox {
    @Override
    public void paint() {
        System.out.println("Rendering a Windows-style Checkbox.");
    }
}

// Concrete Product A2 (Mac Family)
class MacButton implements Button {
    @Override
    public void paint() {
        System.out.println("Rendering a Mac-style Button.");
    }
}

// Concrete Product B2 (Mac Family)
class MacCheckbox implements Checkbox {
    @Override
    public void paint() {
        System.out.println("Rendering a Mac-style Checkbox.");
    }
}
```

#### 3\. Abstract Factory

Define the interface for creating the family of products.

```java
// Abstract Factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

#### 4\. Concrete Factories

Implement the Abstract Factory for each specific platform.

```java
// Concrete Factory 1 (Windows Family Factory)
class WinFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WinButton(); // Creates Windows-specific product
    }

    @Override
    public Checkbox createCheckbox() {
        return new WinCheckbox(); // Creates Windows-specific product
    }
}

// Concrete Factory 2 (Mac Family Factory)
class MacFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacButton(); // Creates Mac-specific product
    }

    @Override
    public Checkbox createCheckbox() {
        return new MacCheckbox(); // Creates Mac-specific product
    }
}
```

#### 5\. Client Code (Application)

The client code uses the factory interface to create products. It is completely unaware of which concrete factory or product implementation it is using.

```java
public class Application {
    private final GUIFactory factory;
    private Button button;
    private Checkbox checkbox;

    // The client accepts an Abstract Factory object in its constructor
    public Application(GUIFactory factory) {
        this.factory = factory;
    }

    // Business logic that creates and uses the products
    public void createUI() {
        this.button = factory.createButton();
        this.checkbox = factory.createCheckbox();
    }

    public void render() {
        System.out.println("Application UI Rendering:");
        button.paint();
        checkbox.paint();
    }
}

public class AbstractFactoryDemo {
    public static void main(String[] args) {
        GUIFactory factory;
        String os = "Mac"; // This could be determined at runtime from a config file or System property

        if (os.equalsIgnoreCase("Windows")) {
            factory = new WinFactory();
        } else {
            factory = new MacFactory();
        }

        // The Application (Client) is created with a concrete factory, but only uses the interface
        Application app = new Application(factory);
        app.createUI();
        app.render();
    }
}
```

**Output (for `os = "Mac"`):**

```
Application UI Rendering:
Rendering a Mac-style Button.
Rendering a Mac-style Checkbox.
```

If you change the `os` variable to `"Windows"`, the application will create and render Windows-style components without changing the `Application` or `render()` methods. You simply swap the entire product family by swapping the **Concrete Factory**.
