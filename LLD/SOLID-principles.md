[back to previous page](./LLD.md)

---

# SOLID Principles

S - Single Responsbility Principle\
O - Open-Close Principle\
L - Liskov Substitution Principle\
I - Interface segmentation principle\
D - Dependency inversion principle

---

## Single Responsibility Principle

- A class/module/logic-unit should only have one responsibility and one reason to change
```
Class Invoice{
   //creates invoice
   //calculate bill total
   //saves invoice to DB
   //prints invoice
}
```
- one class with multiple responsibilities i.e. down the line multiple reasons to change

```
class Invoice{
   //creates invoice 
   //calculates bill total
}
Class InvoiceDao{ 
   //DAO - data access object (naming convention)
   //saves invoice to DB
}
class InvoicePrinter{
   //prints invoice
}
```
- multiple classes with single responsibility - easy to understand and maintain

---

## Open Close Principle

- open for extension but closed for modification
```
Class InvoiceDao{
   //takes invoice object and saves to DB
   //existing code thats been tested 

   //new additions
   //save to local file system
   //save to NoSql DB after conversion
}
```
- above is an example of modification in existing code that already been tested, modification would risk breaking existing code behaviour 

- solution - **Extension**
```
1. Create interface and define expected behaviour
Interface InvoiceDB{
   //save Invoice
}

2. Implement as many times as required, with little changes in exact behaviour
Class SqlInvoiceDao implements Invoice InvoiceDao{...}
Class LocalFileInvoiceDao implements Invoice InvoiceDao{...}
Class NoSqlInvoiceDao implements Invoice InvoiceDao{...}

new DB/save behaviour -> new Function/file with additional functionality defined, no need to test existing working code now
```

---

## Liskov Substitution Principle 

- Subclass should extend the capability of parent class without narrowing it down

- If B is subclass of A -> we should be able to replace the object of A with B -> without breaking the behaviour of the code 

```
Class Bike{
   //start Engine
   //accelerate
}

//Limits parent 
Class Bicycle extends Bike{
   //accelerate 
   //No Engine start method - limits the parent class behaviour
   //if you try  to invoke StartEngine() method after deleting the parent class -> error
}

//adds on to parent behaviour
Class MotorBike extends Bike{
   //start Engine
   //accelerate
   //brake
   //stop engine
}
//this subclass can replace the parent class completely 
```
- **Solution** : only keep very generic properties and methods in the parent class

---

## Interface Segmentation Principle

- Interface should be such that clients should not implement unnecessary functions they dont need

```
Interface RestaurantWorker{
   //wash dishes
   //cook
   //getOrders
   //serveOrders
}

Class Waiter implements RestaurantWorker{
   //needs only to implement relevant getOrder and ServeOrder methods
   //but will have to also implement the orther non requried ones as well
}
```
```
Interface WaiterInterface{
   //getOrders
   //serveOrders
}

Class Waiter implements WaiterInterface{
   //only implement the requried ones
}
```

---

## Dependency Inversion Princple 

- Class should depend on interfaces/abstract-classes rather than Concrete classes
```
Interface Mouse{...}
Class WiredMouse implements Mouse{...}

Interface keyboard{...}
Class WiredKeyboard implements Keyboard{...}

// Not to do //
Class Computer{
   private final WiredKeyboard keyboard1;
   private final WiredMouse mouse1;
   //dependeing on doncrete classes ->any further changes in those calsses will affect this one 

   public Computer(){
      this.keyboard1 = new WiredKeyboard();
      this.mouse1 = new WiredMouse();
   }
   //tightly coupled different units of the application
}

// to do //
Class OpenSourceComputer{
   private final Keyboard keyboard1;
   private final Mouse mouse1;
   //using interfaces - loose coupling and open to further enhancements

   public OpenSourceComputer(Keyboard kb, Mouse m){
      this.keyboard1 = kb;
      this.mouse1 = m;
   }  
   //in this case you use *constructor injection*
   //that is, in the constructor you can pass a subtype of keyboard & mouse and that will be asigned to the object
   
}
```

---