[back to previous page](../LLD.md)

---
The Decorator design pattern allows behavior to be added to individual objects, dynamically, without affecting the behavior of other objects from the same class. It is typically used to adhere to the Single Responsibility Principle by allowing functionality to be divided between classes with unique areas of concern.

### Key Components:
- Component: The interface or abstract class defining the methods that will be implemented.
- ConcreteComponent: The class that implements the Component interface.
- Decorator: The abstract class that implements the Component interface and contains a reference to a Component object.
- ConcreteDecorator: The class that extends the Decorator and adds additional behavior.

### Advantages:

- **Avoids CLASS EXPLOSION**
- **Flexibility**: Allows behavior to be added to individual objects, either statically or dynamically, without affecting the behavior of other objects from the same class.
- **Single Responsibility Principle**: Divides functionality between classes with unique areas of concern, making the code easier to manage and understand.
- **Open/Closed Principle**: New functionality can be added by creating new decorators without modifying existing code.
- **Composability**: Multiple decorators can be combined to create complex behaviors by layering simple behaviors.
### Disadvantages:

- **Complexity**: Can lead to a large number of small classes that can be overwhelming and difficult to manage.
- **Debugging**: Can be more challenging to debug due to the added layers of abstraction.
- **Performance**: May introduce performance overhead due to the additional layers of wrapping.
- **Order Sensitivity**: The order in which decorators are applied can affect the behavior, which can lead to subtle bugs if not managed carefully.

### Real world Use-Case : 

- Design a Coffee Shop System:
Problem: Implement a system where customers can order different types of coffee with various add-ons like milk, sugar, and whipped cream.
Solution: Use the Decorator pattern to create a base Coffee class and decorators for each add-on (e.g., MilkDecorator, SugarDecorator).

- Design a Text Editor with Formatting Options:
Problem: Implement a text editor that supports various text formatting options like bold, italic, and underline.
Solution: Use the Decorator pattern to create a base Text class and decorators for each formatting option (e.g., BoldDecorator, ItalicDecorator).
### Implementation Steps:

```
1. Define Component interface/abstract class
public interface Coffee {
    double getCost();
}

2. create concrete Component
public class SimpleCoffee implements Coffee {
   @Override
   public double getCost() {
      return 5.0;
   }
}
===============================================================================================
3. create abstract decorator
public abstract class CoffeeDecorator implements Coffee {
   protected Coffee decoratedCoffee;

   public CoffeeDecorator(Coffee coffee) {
      this.decoratedCoffee = coffee;
   }

   @Override
   public double getCost() {
      return decoratedCoffee.getCost();
   }
}

4. create concrete decorator
public class MilkDecorator extends CoffeeDecorator {
   public MilkDecorator(Coffee coffee) {
      super(coffee);
   }

   @Override
   public double getCost() {
      return decoratedCoffee.getCost() + 1.5;
   }
}

public class SugarDecorator extends CoffeeDecorator {
   public SugarDecorator(Coffee coffee) {
      super(coffee);
   }

   @Override
   public double getCost() {
      return decoratedCoffee.getCost() + 0.5;
   }
}
===============================================================================================
Step 5: Use the Decorators
public class DecoratorPatternDemo {
    public static void main(String[] args) {
        
        Coffee simpleCoffee = new SimpleCoffee();
        System.out.println(simpleCoffee.getCost());

        Coffee milkCoffee = new MilkDecorator(new SimpleCoffee());
        System.out.println(milkCoffee.getCost());

        Coffee sugarMilkCoffee = new SugarDecorator(new MilkDecorator(new SimpleCoffee()));
        System.out.println(sugarMilkCoffee.getCost());
    }
}
```
