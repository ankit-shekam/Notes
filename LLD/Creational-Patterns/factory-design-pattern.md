[back to previous page](../LLD.md)

---

## Factory Design Pattern

Create objects without exposing the creation logic to the client and refer to newly created object using a common interface,

the subclasses at runtime decide which class (that implemented the iterface) to instantiate

- when to use factory method :
   - class can not anticipate the class of objects it must create
   - class wants its subclass to specify the objects it creates
   - Classes delegate responsibility to one of several helper subclasses, and you want to localize the knowledge of which helper subclass is the delegate

- Major mplementation steps: 
   - Root interface/abstract-class
   - Concrete-implementation classes
   - FactoryMethod
   - sometimes Enums used to create switch statements, not requried(optional)

![Class diagram for factory method of payment system](../Images/factoryMethod-classDiagram.png)


```
// root interface
public interface Payment {
  void pay(BigInteger amount);
}

// concrete implementation classes
public class CreditCardPayment implements Payment{
  @Override
  public void pay(BigInteger amount) {
    System.out.println(MessageFormat.format("Successfully paid ${0} to merchant using a credit card", amount));
  }
} //similarly classes made for other implementations (Paypal, GooglePay, Applepay)

// enum to specify the impementation requried from factory
public enum PaymentMethod {
  CREDIT_CARD,
  GOOGLE_PAY,
  PAYPAL,
  APPLE_PAY
}

// factory class
public class PaymentFactory {
  public static Payment create(PaymentMethod paymentMethod) throws ClassNotFoundException {
    switch (paymentMethod) {
      case CREDIT_CARD:
        return new CreditCardPayment();
      case GOOGLE_PAY:
        return new GooglePayPayment();
      case PAYPAL:
        return new PayPalPayment();
      default:
        throw new ClassNotFoundException(MessageFormat.format("${0} method not supported yet", paymentMethod));
    }
  }
}


//client code using pattern for payment logic
Payment payment = PaymentFactory.create(PaymentMethod.CREDIT_CARD);
payment.pay(new BigInteger("98900"));
Payment payment1 = PaymentFactory.create(PaymentMethod.GOOGLE_PAY);
payment1.pay(new BigInteger("98900"));
Payment payment2 = PaymentFactory.create(PaymentMethod.PAYPAL);
payment2.pay(new BigInteger("98900"));

```