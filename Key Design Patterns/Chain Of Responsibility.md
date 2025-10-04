The Chain of Responsibility is a **behavioral design pattern** that lets you pass requests along a chain of potential handlers. Each handler decides either to process the request or to pass it along the chain to the next handler.

The pattern's primary goal is to **decouple the sender of a request from its receiver** by giving multiple objects a chance to handle the request, without the sender knowing which object will ultimately process it.

-----

## 1\. Core Concept and Structure

Imagine a request (e.g., a customer support call, a bank transaction, an approval request) that needs to go through multiple steps or checks.

Instead of the sender knowing all the possible receivers and their capabilities, the request is sent to the *first* handler in a pre-linked chain.

### Key Components:

1.  **Handler (Interface or Abstract Class):**

      * Declares an interface common to all handler objects.
      * Defines a method to handle the request (e.g., `handleRequest()`).
      * Defines a method to set the successor/next handler (e.g., `setNextHandler()`).

2.  **Concrete Handler:**

      * Implements the `Handler` interface.
      * Contains the logic to process a specific type of request.
      * If it can handle the request, it does so (and may stop the chain).
      * If it cannot or should not handle the request, it forwards it to the next handler in the chain via its successor reference.

3.  **Client:**

      * Creates the chain of handlers and links them together.
      * Initiates the request by sending it to the first handler in the chain.

-----

## 2\. Java Example: Leave Approval System

A common example is an employee's leave request that must be approved by different levels of management based on the duration.

  * **Manager** can approve $\le 5$ days.
  * **Director** can approve $\le 15$ days (if the Manager couldn't).
  * **CEO** can approve $\le 30$ days (if the Director couldn't).

### Step 1: The Request Class

This object holds the data that is passed along the chain.

```java
public class LeaveRequest {
    private int days;
    private String employeeName;

    public LeaveRequest(int days, String employeeName) {
        this.days = days;
        this.employeeName = employeeName;
    }

    public int getDays() {
        return days;
    }

    public String getEmployeeName() {
        return employeeName;
    }
}
```

### Step 2: The Handler Interface/Abstract Class

This defines the contract for all approvers and manages the link to the next approver.

```java
public abstract class LeaveApprover {
    protected LeaveApprover nextApprover; // Reference to the next handler

    public void setNextApprover(LeaveApprover nextApprover) {
        this.nextApprover = nextApprover;
    }

    // The method that all concrete handlers must implement
    public abstract void approveLeave(LeaveRequest request);
}
```

### Step 3: Concrete Handlers

These classes implement the specific approval logic.

```java
public class Manager extends LeaveApprover {
    @Override
    public void approveLeave(LeaveRequest request) {
        if (request.getDays() <= 5) {
            System.out.println("Manager approved " + request.getDays() + " days for " + request.getEmployeeName());
        } else {
            // Cannot handle, pass to the next approver
            if (nextApprover != null) {
                System.out.println("Manager cannot approve " + request.getDays() + " days. Forwarding to Director.");
                nextApprover.approveLeave(request);
            }
        }
    }
}

public class Director extends LeaveApprover {
    @Override
    public void approveLeave(LeaveRequest request) {
        if (request.getDays() <= 15) {
            System.out.println("Director approved " + request.getDays() + " days for " + request.getEmployeeName());
        } else {
            // Cannot handle, pass to the next approver
            if (nextApprover != null) {
                System.out.println("Director cannot approve " + request.getDays() + " days. Forwarding to CEO.");
                nextApprover.approveLeave(request);
            }
        }
    }
}

public class CEO extends LeaveApprover {
    @Override
    public void approveLeave(LeaveRequest request) {
        if (request.getDays() <= 30) {
            System.out.println("CEO approved " + request.getDays() + " days for " + request.getEmployeeName());
        } else {
            // Final handler
            System.out.println("Leave request for " + request.getDays() + " days is too long. Denied.");
        }
    }
}
```

### Step 4: Client Code (Building and Using the Chain)

The client builds the chain and sends the requests.

```java
public class Client {
    public static void main(String[] args) {
        // 1. Create the handlers
        LeaveApprover manager = new Manager();
        LeaveApprover director = new Director();
        LeaveApprover ceo = new CEO();

        // 2. Build the chain: Manager -> Director -> CEO
        manager.setNextApprover(director);
        director.setNextApprover(ceo);
        // CEO doesn't set a next approver, so the chain ends there

        // --- Requests ---

        // Request 1: Handled by Manager
        LeaveRequest req1 = new LeaveRequest(3, "Alice");
        System.out.println("--- Processing Request 1 (3 days) ---");
        manager.approveLeave(req1);
        // Output: Manager approved 3 days for Alice

        System.out.println("\n--- Processing Request 2 (10 days) ---");
        // Request 2: Manager forwards, Director handles
        LeaveRequest req2 = new LeaveRequest(10, "Bob");
        manager.approveLeave(req2);
        // Output:
        // Manager cannot approve 10 days. Forwarding to Director.
        // Director approved 10 days for Bob

        System.out.println("\n--- Processing Request 3 (40 days) ---");
        // Request 3: All forward, CEO denies
        LeaveRequest req3 = new LeaveRequest(40, "Charlie");
        manager.approveLeave(req3);
        // Output:
        // Manager cannot approve 40 days. Forwarding to Director.
        // Director cannot approve 40 days. Forwarding to CEO.
        // Leave request for 40 days is too long. Denied.
    }
}
```

-----

## 3\. Advantages and Disadvantages

| Feature | Advantages | Disadvantages |
| :--- | :--- | :--- |
| **Decoupling** | Sender is completely decoupled from the receiver; it only needs to know the first handler. | No guarantee that the request will be handled if a handler fails to forward it or the end of the chain is reached. |
| **Flexibility** | The chain order and participants can be changed dynamically at runtime, or new handlers can be added easily without changing the client or other handlers. | Can introduce performance overhead if the chain is very long, as the request must traverse many objects. |
| **SRP** | Each handler focuses only on its own specific processing logic (Single Responsibility Principle). | Debugging can be tricky since the request's path is not explicit. |
