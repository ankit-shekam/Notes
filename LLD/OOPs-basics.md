[back to previous page](./LLD.md)

---

# Basics of OOPs

## Claases & Objects

- Classes : Class is a blueprint or template that defines the properties & behaviour of an object 

```
public class Car {
   // member fields
   private String type;
   private String model;
 
   // constructor
   public Car(String type, String model) {
      this.type = type;
      this.model = model;
   }
     
   // member methods
   public int increaseSpeed(int increment) {
      this.speed = this.speed + increment;
      return this.speed;
   }
}
```

- Object : An instance of a class, created using the class definition
- object have behaviour(functions/methods) and properties(state)

```
Car veyron = new Car("Bugatti", "Veyron", "crimson");
Car corvette = new Car("Chevrolet", "Corvette", "black");
```

---

## Encapsulation

Bundle the data and relevant code that works on that data in a single unit\
AKA data hiding

- Advantages : Loosey coupled code, better security and access control
- Achieved through : **Priavte** variables and **public** getters/setters to modify & obtain variable values

---

## Inheritance

Capability of a child to inherit properties and behaviour from their parent

- advantages : code reusability increases, can achieve POLYMORPHISM using inheritance 
- Achieved throug : **extends** keyword -> `Class A extends B {.....}`
- types of inheritance : 
   - single inheritance : child inherits from 1 parent 
   - Multi level inheritance : parent -> child -> grandchild ->....
   - heirarchical inheritance : 1 parent with multiple child classes
   - hybrid inheritance : mix of 2 or more other types
   - Multiple inheritance : 1 child with with more than 1 parent `causes diamond problem in java, therefore in java child can not inherit from more than 1 parent`

Diamond problem in multiple inheritance : 
```
Class A extends B, C {
   // now if A calls a method that is defined in more than one parent classes
   // JVM does not know which implementation to use 
}
``` 
solution for diamond problem -> **Interfaces**
- interfaces : A mechanism to achieve Abstraction, can contain abstract methods and variables 


---

## Polymorphism

Same method behaves different in different situations

- types : 
   - Runtime Polymorphism / Static Polymorphism / Method overriding
   - Compile-time Polymorphism / Dynamic Polymorphism / Method overloading

- Runtime Polymorphism : 
   - A child class povidign a specific implementation of a method already provided by one of its parent class
   - methos in the child class has same name, function sganture, return type as that in parent class
   - which method gets called is determined by the object used to invoke it 

   ```
   Parent obj1 = new Parent();
   obj1.show(); //parents show method called 

   Parent obj2 = new Child();
   obj2.show(); //childs show method called
   //show method overridden by child class
   ```

- Compile Time Polmorphism : 
   - More than 1 method share the same name with a different singature (number and type od params) in the class 
   - return may/may-not be same 
   ```
   int add(int a, int b){...}
   int add(int a, int b, int c, int d){...}
   void add(String a, String b){...}
   ```


---

## Abstraction

Hiding internal implementation detials and exposing only the requried parts of the functionality to the user 

- Advantanges : Increased security & Confidentiality
- Achieved through : **Abstract classes** and **Interfaces**

eg. PHONE - call('xyz') will call the person via the various hoops and protocols in between, no need to expose the process behind it, just expose the call method

---

## Object relationships : Is-A / Has-A

- Is-A relationship : 
   - achieved through : Inhertance
   ```
   Parent -> Child

   Animal -> Dog (Dog Is-A Animal)
   Vehicle -> Car (Car Is-A Vehicle)
   ```
- Has-A relationship : 
   - aka **COMPOSITION** | **AGGREGATION**
   - Composition (string relation) - Ending one object will end the other
   - Aggregation (weak relation) - both objects can survive individually, ending one wont end the other
   - Instance of Object of a class used in another class
   - can be 1:1/many:1/1:many/many:many
   ```
   Class Engine{...}

   Class KTM extends Bike{
      Engine ktm-engine = new Engine();
      ...
   }
   ```

---