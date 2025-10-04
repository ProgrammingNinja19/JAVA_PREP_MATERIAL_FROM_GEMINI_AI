The $\text{equals()}$ and $\text{hashCode()}$ methods in Java are fundamental for object comparison and are inherited by every class from the $\text{java.lang.Object}$ class. When creating your own classes, you often need to override the default implementations to define **logical equality** for your objects.

## 1. The $\text{equals(Object obj)}$ Method

### Purpose
The $\text{equals()}$ method is used to determine if two objects are logically equal.

### Default Implementation
The default implementation in $\text{Object}$ class simply checks for **reference equality** (i.e., whether the two object references point to the exact same object in memory, using the $\text{==}$ operator).

### Contract (Rules for Overriding)
When you override $\text{equals()}$, your new implementation **must** satisfy the following properties, which define an equivalence relation:

1.  **Reflexive:** For any non-null reference value $x$, $x.\text{equals}(x)$ must return $\text{true}$.
2.  **Symmetric:** For any non-null reference values $x$ and $y$, $x.\text{equals}(y)$ must return $\text{true}$ if and only if $y.\text{equals}(x)$ returns $\text{true}$.
3.  **Transitive:** For any non-null reference values $x$, $y$, and $z$, if $x.\text{equals}(y)$ returns $\text{true}$ and $y.\text{equals}(z)$ returns $\text{true}$, then $x.\text{equals}(z)$ must return $\text{true}$.
4.  **Consistent:** For any non-null reference values $x$ and $y$, multiple invocations of $x.\text{equals}(y)$ must consistently return $\text{true}$ or consistently return $\text{false}$, provided no information used in $\text{equals}$ comparisons on the objects is modified.
5.  **Non-nullity:** For any non-null reference value $x$, $x.\text{equals}(\text{null})$ must return $\text{false}$.

---

## 2. The $\text{hashCode()}$ Method

### Purpose
The $\text{hashCode()}$ method returns an integer hash code value for the object. This hash code is primarily used by hash-based collections in Java (like $\text{HashMap}$, $\text{HashSet}$, and $\text{Hashtable}$) for efficient storage and retrieval.

### Default Implementation
The default implementation in $\text{Object}$ typically returns a distinct integer for distinct objects, often derived from the object's memory address.

### Contract (Rules for Overriding)
When you override $\text{hashCode()}$, your new implementation **must** satisfy the following rules:

1.  **Consistency:** Whenever $\text{hashCode()}$ is invoked on the same object more than once during an execution of a Java application, it must consistently return the same integer, provided no information used in $\text{equals}$ comparisons on the object is modified.
2.  **The Strict Contract (The Core Rule):** If two objects are equal according to the $\text{equals(Object)}$ method, then calling the $\text{hashCode()}$ method on each of the two objects **must** produce the same integer result.
3.  **No Requirement for Unequal Objects:** It is **not required** that if two objects are unequal according to the $\text{equals(Object)}$ method, then calling $\text{hashCode()}$ on each of them must produce distinct integer results. (Two unequal objects *may* have the same hash code—this is called a **hash collision**).

---

## 3. The Strict Contract Between $\text{equals()}$ and $\text{hashCode()}$

The most critical part of the $\text{hashCode()}$ contract (rule 2 above) forms the strict contract between the two methods:

$$\text{If } a.\text{equals}(b) \text{ is true, then } a.\text{hashCode}() == b.\text{hashCode}() \text{ must be true.}$$

### Why the Contract is Strict

This contract is essential for the correct functioning of hash-based collections ($\text{HashMap}$, $\text{HashSet}$, etc.). These collections work in a two-step process to store and retrieve elements:

1.  **Step 1: Bucketing (using $\text{hashCode()}$):** When you try to find or retrieve an object, the collection first calculates the object's hash code to quickly identify the "bucket" or array index where the object *might* be stored.
2.  **Step 2: Comparison (using $\text{equals()}$):** The collection then iterates through the objects inside that bucket and uses the $\text{equals()}$ method to find the exact match.

### Consequences of Violating the Strict Contract

If you override $\text{equals()}$ but **fail** to override $\text{hashCode()}$ consistently, you violate this contract, leading to subtle but severe bugs:

| Action | Result | Consequence in Hash Collections |
| :--- | :--- | :--- |
| **Override only $\text{equals()}$** | Two logically equal objects ($a$ and $b$) will return $\text{true}$ for $\text{a.equals(b)}$, but they will have different default hash codes ($a.\text{hashCode}() \neq b.\text{hashCode}()$) since the default $\text{hashCode()}$ is often based on memory address. | A $\text{HashMap}$ or $\text{HashSet}$ will put the two equal objects into **different buckets**. You can $\text{put(a, value)}$, but then $\text{get(b)}$ will look in the wrong bucket and return $\text{null}$, or $\text{HashSet}$ will contain two duplicate entries. |
| **Override only $\text{hashCode()}$** | Two unequal objects may have the same hash code ($a.\text{hashCode}() = b.\text{hashCode}()$) but $\text{a.equals(b)}$ returns $\text{false}$ (based on default reference equality). This is technically *allowed* but often not what you intended when you started overriding. | If your $\text{hashCode()}$ is based on logical content, two logically equal but distinct objects will go to the **same bucket**. But since you didn't override $\text{equals()}$, $\text{equals()}$ will still check reference equality and return $\text{false}$, causing the collection to treat them as two different objects and fail to correctly replace a value or prevent a duplicate. |

**In summary, you must override $\text{hashCode()}$ whenever you override $\text{equals()}$ to ensure that objects considered logically equal by $\text{equals()}$ also land in the same bucket in hash-based collections, making them function correctly.**
