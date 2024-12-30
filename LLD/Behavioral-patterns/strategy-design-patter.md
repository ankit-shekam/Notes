[back to previous page](../LLD.md)

---

## Strategy Design Pattern

- A behavioral design pattern that allows you to define a family of algorithms, encapsulate each one, and make them interchangeable. This pattern lets the algorithm vary independently from clients that use it, promoting flexibility and reusability. 
   - Enables selecting algorithm at run-time
   - instead of implementing a single algorithm directly, application gets run time instructions as to which algorithm to use 

- When to use : 
   - The Strategy Design Pattern is particularly useful when you have multiple algorithms for specific tasks and want to switch between them easily without altering the client code
-  Major Implementation Steps : 
   1. **Strategy Interface**: An interface common to all supported algorithms.
   2. **Concrete Strategies**: Classes that implement the Strategy interface and define specific algorithms
   3. **Context**: Maintains a reference to a Strategy object and can change the strategy at runtime.

- Examples : 
   -  Payment Gateway with multiple payment options 

```
// 1. Strategy interface : 

interface Strategy {
   void execute();
}
-------------------------------------------------------------------------

// 2. Concrete Strategies (implementations) : 

class ConcreteStrategyA implements Strategy {
   @Override
   public void execute() {
      System.out.println("Executing Strategy A");
   }
}
class ConcreteStrategyB implements Strategy {
   @Override
   public void execute() {
      System.out.println("Executing Strategy B");
   }
}
-------------------------------------------------------------------------

// 3. Context class 

class Context {
   private Strategy strategy;

   // Set strategy at runtime
   public void setStrategy(Strategy strategy) {
      this.strategy = strategy;
   }

   // Execute strategy
   public void executeStrategy() {
      strategy.execute();
   }
}

-------------------------------------------------------------------------

// Usage

public class StrategyPatternExample {
   public static void main(String[] args) {

      Context context = new Context();

      // Use ConcreteStrategyA
      context.setStrategy(new ConcreteStrategyA());
      context.executeStrategy(); // Output: Executing Strategy A

      // Use ConcreteStrategyB
      context.setStrategy(new ConcreteStrategyB());
      context.executeStrategy(); // Output: Executing Strategy B
   }
}
```

- Question : 
   - How is Factory pattern different from strategy pattern
- Answer : 
   - factory pattern is used to create objects of a specific type. 
   - A strategy pattern is use to perform an operation (or set of operations) in a particular manner. 
   - In the classic example, a factory might create different types of `Animals:` Dog, Cat, Tiger, while a strategy pattern would perform particular actions, for example, `Move();` using Run, Walk strategies.