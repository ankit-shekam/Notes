[back to previous page](../LLD.md)

---

## Observer Design pattern

- Observer Design Pattern is a behavioral design pattern that establishes a one-to-many dependency between objects. This ensures that when one object changes its state, all its dependents are notified and updated automatically. It's particularly useful when you need to maintain consistency between related objects without them being tightly coupled.
- **Advantages**
   - Loose Coupling: Observers do not need to know about each other or the subject’s inner workings.
   - Scalability: Easy to add/remove observers without modifying the subject.

- **Disadvantages**
   - Performance Overhead: If there are many observers, notifying them can be time-consuming.
   - Complexity: Managing the list of observers can add complexity to the code.
- **Use Cases**
   - Implementing distributed event-handling systems.
   - Situations where a change in one object requires changes in others, but the object triggering the change doesn’t know how many objects need to be changed.

---

### Practice question 

**Question :**  
- Notify me button in e-commerce website