[back to previous page](../LLD.md)

---

## Observer Design pattern

A behavioral design pattern that allows an object(aka **SUBJECT**) to maintain a list of its dependents(aka **OBSERVERS**) and automatically notify them of any state changes. This pattern is particularly useful in scenarios where an object needs to communicate changes to multiple other objects without being tightly coupled to them.

### Key Concepts
- Subject: The object that holds the data and sends notifications when its state changes.
- Observer: The object that wants to be informed of changes in the subject.
- ConcreteSubject: A specific implementation of the subject.
- ConcreteObserver: A specific implementation of the observer that receives updates.

### How It Works
- Attach: Observers can register themselves with the subject.
- Detach: Observers can unregister themselves from the subject.
- Notify: When the subject's state changes, it sends a notification to all registered observers.

### Advantages
- Loose Coupling: The subject and observers are loosely coupled, making it easier to add or remove observers.
- Scalability: Supports multiple observers being notified simultaneously.

### Disadvantages
- Complexity: Can introduce complexity if not managed properly, especially with a large number of observers.
- Performance: Can lead to performance issues if many observers need to be notified frequently.

### Use Case 
- **Real-Time Notifications**: When an object needs to notify multiple objects about a state change. For example, in a chat application, when a new message arrives, it should notify all the active chat windows.
- **Reactive Programming**: When implementing reactive systems where components react to changes in data streams.
- **Stock Market Tickers**: Displaying real-time stock prices to multiple observers.
- **News Aggregators**: Updating subscribers with the latest news articles.
- ***Social Media Feeds***: Updating followers with new posts or activity.


```
class Observable {
   private List<Observer> observers = new ArrayList<>();
   private String message;

   public void addObserver(Observer observer) {
      observers.add(observer);
   }

   public void removeObserver(Observer observer) {
      observers.remove(observer);
   }

   public void notifyObservers() {
      for (Observer observer : observers) {
         observer.update(message);
      }
   }

   public void setMessage(String message) {
      this.message = message;
      notifyObservers();
   }
}

===========================================================================

interface Observer {
   void update(String message);
}

class ConcreteObserver implements Observer {
    private String name;

    public ConcreteObserver(String name) {
        this.name = name;
    }

    @Override
    public void update(String message) {
        System.out.println(name + " received message: " + message);
    }
}

===========================================================================

public class ObserverPatternDemo {
    public static void main(String[] args) {
        Subject subject = new Subject();
        
        Observer observer1 = new ConcreteObserver("Observer 1");
        Observer observer2 = new ConcreteObserver("Observer 2");
        
        subject.addObserver(observer1);
        subject.addObserver(observer2);

        subject.setMessage("Hello, Observers!");
    }
}

```