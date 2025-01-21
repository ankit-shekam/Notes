[back to previous page](../LLD.md)

---

### Abstract Factory Pattern
- Factory of Factories
- Creational design pattern that provides an interface for creating families of related or dependent objects without specifying their concrete classes. It is useful when a system needs to be independent of how its objects are created, composed, and represented.

### Key Implementation components

- AbstractFactory: Declares an interface for creating abstract products.
- ConcreteFactory: Implements the operations to create concrete product objects.
- AbstractProduct: Declares an interface for a type of product object.
- ConcreteProduct: Implements the AbstractProduct interface.
- Client: Uses only the interfaces declared by AbstractFactory and AbstractProduct classes.

```
1. Define Abstract Factory
public interface FurnitureFactory {
   Chair createChair();
   Sofa createSofa();
}

===================================================================================

2. Create Concrete Factories
public class VictorianFurnitureFactory implements FurnitureFactory {
   @Override
   public Chair createChair() {
      return new VictorianChair();
   }

   @Override
   public Sofa createSofa() {
      return new VictorianSofa();
   }
}

public class ModernFurnitureFactory implements FurnitureFactory {
   @Override
   public Chair createChair() {
      return new ModernChair();
   }

   @Override
   public Sofa createSofa() {
      return new ModernSofa();
   }
}

====================================================================================

3. Define abstract product 
public interface Chair {
   void sitOn();
}

public interface Sofa {
   void lieOn();
}

====================================================================================

4. Create Concrete Product
public class VictorianChair implements Chair {
   @Override
   public void sitOn() {
      System.out.println("Sitting on a Victorian chair.");
   }
}

public class ModernChair implements Chair {
   @Override
   public void sitOn() {
      System.out.println("Sitting on a Modern chair.");
   }
}

public class VictorianSofa implements Sofa {
   @Override
   public void lieOn() {
      System.out.println("Lying on a Victorian sofa.");
   }
}

public class ModernSofa implements Sofa {
   @Override
   public void lieOn() {
      System.out.println("Lying on a Modern sofa.");
   }
}

====================================================================================

5. Client using the factory of factories
public static void main(String[] args) {
   FurnitureFactory victorianFactory = new VictorianFurnitureFactory();
   FurnitureFactory modernFactory = new ModernFurnitureFactory();

   System.out.println("Victorian Furniture:");
   Chair victorianChair = victorianFactory.createChair();
   victorianChair.sitOn();
   Sofa victorinSofa = victorianFactory.createSofa();
   victorinSofa.lieOn();
}

```