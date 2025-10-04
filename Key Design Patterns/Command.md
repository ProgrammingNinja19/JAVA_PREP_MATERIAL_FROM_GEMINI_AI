The **Command design pattern** is a behavioral pattern that encapsulates a request as an object, thereby allowing you to parameterize clients with different requests, queue or log requests, and support undoable operations.

In essence, it turns a method call into a stand-alone object. This object holds all the information necessary to perform the action, including the method to call, the object that owns the method (the Receiver), and the method's arguments.

## 1\. Core Concept and Intent

The primary goal of the Command pattern is **decoupling**. It separates the object that **invokes** the operation (the Invoker) from the object that **knows how to perform** it (the Receiver).

Imagine a classic restaurant scenario:

1.  **Customer (Client):** Decides what to order.
2.  **Order Slip (Command):** An object that represents the request, e.g., "Cook Steak (medium rare)."
3.  **Waiter (Invoker):** Takes the Order Slip and places it on the order queue. The waiter doesn't know *how* to cook.
4.  **Chef (Receiver):** Takes the Order Slip off the queue and executes the command (the actual cooking).

The waiter (Invoker) can handle *any* request—a steak order, a dessert order, a drink order—because they all share the common "Order Slip" interface. This loose coupling is the power of the pattern.

## 2\. Key Components

| Component | Role | Example (Smart Home) |
| :--- | :--- | :--- |
| **Command (Interface)** | Declares an interface for executing an operation. It typically contains a single method, like `execute()`. | `interface Command { void execute(); }` |
| **ConcreteCommand** | Implements the `Command` interface. It binds a Receiver object and its specific action together. It implements `execute()` by calling the Receiver's action. | `class LightOnCommand` |
| **Receiver** | The object that performs the actual operation. It contains the business logic. Commands only delegate to the Receiver. | `class Light { void turnOn(); void turnOff(); }` |
| **Invoker** | Holds a reference to a `Command` object and decides when to call the `execute()` method. It knows nothing about the concrete Command or Receiver. | `class RemoteControl` (a button) |
| **Client** | Responsible for creating a `ConcreteCommand` object, setting its `Receiver`, and assigning it to the `Invoker`. | The main application logic. |

## 3\. Java Example: A Simple Home Automation System

Let's implement a system where a remote control (Invoker) can turn a light (Receiver) on or off.

### Step 1: The Receiver (The Device)

The object that knows how to perform the actual work.

```java
public class Light {
    private String location;

    public Light(String location) {
        this.location = location;
    }

    public void turnOn() {
        System.out.println(location + " light is ON.");
    }

    public void turnOff() {
        System.out.println(location + " light is OFF.");
    }
}
```

### Step 2: The Command Interface

The contract for all commands.

```java
@FunctionalInterface // Java 8+ optimization for single abstract method
public interface Command {
    void execute();
}
```

### Step 3: Concrete Commands

Each command encapsulates a specific request to a specific receiver.

```java
// Command to turn the light ON
public class LightOnCommand implements Command {
    private Light light; // Reference to the Receiver

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOn(); // Delegating the work to the Receiver
    }
}

// Command to turn the light OFF
public class LightOffCommand implements Command {
    private Light light; // Reference to the Receiver

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOff(); // Delegating the work to the Receiver
    }
}
```

### Step 4: The Invoker (The Remote Control)

This object only knows about the `Command` interface.

```java
public class RemoteControl {
    private Command command; // Slot for a command object

    // Method to set a command (parameterize the invoker)
    public void setCommand(Command command) {
        this.command = command;
    }

    // Method to execute the command when the button is pressed
    public void pressButton() {
        if (command != null) {
            System.out.print("Remote button pressed. -> ");
            command.execute();
        } else {
            System.out.println("Remote button has no command assigned.");
        }
    }
}
```

### Step 5: The Client (The Application)

The client sets up the relationship between the Invoker, Commands, and Receivers.

```java
public class Client {
    public static void main(String[] args) {
        // 1. Create the Receiver object
        Light livingRoomLight = new Light("Living Room");

        // 2. Create the Concrete Command objects, linking them to the Receiver
        Command livingRoomLightOn = new LightOnCommand(livingRoomLight);
        Command livingRoomLightOff = new LightOffCommand(livingRoomLight);

        // 3. Create the Invoker object
        RemoteControl remote = new RemoteControl();

        // --- Use Case 1: Turning the Light ON ---
        remote.setCommand(livingRoomLightOn);
        remote.pressButton();
        // Output: Remote button pressed. -> Living Room light is ON.

        // --- Use Case 2: Turning the Light OFF ---
        remote.setCommand(livingRoomLightOff);
        remote.pressButton();
        // Output: Remote button pressed. -> Living Room light is OFF.

        // **Flexibility Demonstration**
        // The remote can now control any other device (e.g., a fan) without changing the RemoteControl class.
        
        // New Receiver
        Stereo stereo = new Stereo(); 
        // New Command (Client creates and sets it)
        Command stereoVolumeUp = new StereoVolumeUpCommand(stereo);
        
        remote.setCommand(stereoVolumeUp);
        remote.pressButton();
        // Output: Remote button pressed. -> Stereo volume is up!
    }
}
```

## 4\. When to Use the Command Pattern

| Purpose | Benefit |
| :--- | :--- |
| **Undo/Redo Functionality** | A command object can store the state of the system *before* its execution. To undo, you reverse the action using the stored state (`unexecute()` method). A stack of executed commands forms a history for undo/redo. |
| **Asynchronous Operations (Queuing/Threading)** | Commands can be placed in a queue (e.g., a **Thread Pool** queue where commands implement `Runnable`). The Invoker (the thread pool) executes them later when threads are available. |
| **Logging/Auditing** | Commands can be logged to disk before execution, allowing the system to replay all actions after a crash. |
| **Macro/Composite Commands** | You can create a composite command that holds a list of other command objects. Executing the macro command executes all commands in the list sequentially. |
| **Decoupling GUI from Business Logic** | UI buttons/menus (Invokers) only need to know how to call `execute()`. The command objects handle the complex interaction with the business logic (Receivers). |

***Note on Java 8:*** Java's built-in `java.lang.Runnable` and `java.util.concurrent.Callable` interfaces are essentially implementations of the Command pattern. You can often use a **Lambda Expression** as a lightweight `Command` when you only need to execute a simple operation without supporting undo/redo.
