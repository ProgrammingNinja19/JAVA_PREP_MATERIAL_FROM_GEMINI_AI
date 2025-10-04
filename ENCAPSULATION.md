Encapsulation is one of the four fundamental principles of Object-Oriented Programming (OOP), and it is a core concept in Java.

Here is a detailed explanation of what encapsulation is and how it is achieved in Java

## What is Encapsulation

Encapsulation is the mechanism of bundling the data (variables) and the code (methods) that operate on that data into a single unit, typically a class.

In simpler terms, it's like a protective wrapper that keeps the internal details of an object hidden from the outside world and provides a controlled interface for interaction.

The two main goals of encapsulation are

1.  Data Hiding It protects the internal state of an object from being accessed or modified directly by external code. This prevents accidental or unintended data corruption.
2.  Controlled Access It provides methods (often called getters and setters) to access and modify the data in a controlled manner. This allows the class to enforce business rules or validation logic before any changes are made.

### Benefits of Encapsulation

   Data IntegritySecurity By controlling access via methods, you can ensure that data is always valid and in a consistent state (e.g., ensuring a person's age is never negative).
   Flexibility and Maintainability You can change the internal implementation of a class (e.g., change a data type or add complex logic) without affecting the external code that uses the class, as long as the public methods' signatures remain the same.
   Read-Only or Write-Only Access You can create fields that are read-only (by providing only a getter method) or write-only (by providing only a setter method).

-----

## How is Encapsulation Achieved in Java

Encapsulation in Java is primarily achieved through a two-step process using access modifiers

### 1. Declare the Variables as `private` (Data Hiding)

The instance variables (fields) of the class are declared using the `private` access modifier. This ensures that these variables cannot be directly accessed or modified from outside the class.

### 2. Provide Public Getter and Setter Methods (Controlled Access)

For any variable that needs to be accessed or modified from an external class, you must provide public methods

   Getter Method (Accessor) A public method used to read the value of a private variable. It is typically named `getVariableName()`.
   Setter Method (Mutator) A public method used to modify or write a new value to a private variable. It is typically named `setVariableName(new_value)`. This is where you can include validation logic.

### Example in Java

Let's illustrate with a simple `Student` class

```java
public class Student {
     1. Declare the variables as private (Data Hiding)
    private String name;
    private int age;

     2. Provide public Getter and Setter methods (Controlled Access)

     Getter for 'name'
    public String getName() {
        return name;
    }

     Setter for 'name'
    public void setName(String newName) {
        this.name = newName;
    }

     Getter for 'age'
    public int getAge() {
        return age;
    }

     Setter for 'age' with Validation Logic
    public void setAge(int newAge) {
         Enforcing a business rulevalidation Age must be positive
        if (newAge  0) {
            this.age = newAge;
        } else {
            System.out.println(Invalid age provided. Age must be positive.);
        }
    }
}
```

### How an External Class Interacts

```java
public class Main {
    public static void main(String[] args) {
        Student student1 = new Student();

         Direct access is prevented by 'private'
         student1.age = -5;  This line would cause a compilation error!

         Controlled access using setter method
        student1.setName(Alice);

         The setter enforces the rule (validation in action)
        student1.setAge(25);       Valid change age is set to 25
        student1.setAge(-10);      Invalid change prints error message, age remains 25

         Controlled retrieval using getter methods
        String name = student1.getName();
        int age = student1.getAge();

        System.out.println(Student Name  + name);  Output Student Name Alice
        System.out.println(Student Age  + age);    Output Student Age 25
    }
}
```
