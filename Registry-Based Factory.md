### **Extending Factory Without Modifying It (Open/Closed Principle Violation) – In-Depth Explanation with a Real-Time Example**  

---

## **1. Understanding the Problem: Open/Closed Principle Violation**  
The **Open/Closed Principle (OCP)** states that:  

> **"Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification."**  

However, in a traditional **Factory Pattern**, every time we need to add a new type of object (e.g., a new shape), we **modify** the factory class. This violates OCP because we are constantly changing the factory class instead of extending it.  

### **Example of the Violation**  

#### **Step 1: Define a `Shape` Interface**
```java
public interface Shape {
    void draw();
}
```

#### **Step 2: Implement Some Concrete Shapes**
```java
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Circle");
    }
}

public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Rectangle");
    }
}
```

#### **Step 3: Create a Traditional Factory**
```java
public class ShapeFactory {
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        }
        return null;
    }
}
```

#### **Step 4: Client Code Uses Factory**
```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory factory = new ShapeFactory();

        Shape shape1 = factory.getShape("CIRCLE");
        shape1.draw();

        Shape shape2 = factory.getShape("RECTANGLE");
        shape2.draw();
    }
}
```

---

## **2. What Happens When We Need a New Shape (Triangle)?**  
Now, suppose we want to add a new shape, **Triangle**.  

- We must **modify the factory class (`ShapeFactory`)** and add a new `if` condition.  
- Every time we add a new shape, the factory class **grows bigger and more complex**.  
- This violates the **Open/Closed Principle** because we are **modifying an existing class instead of extending it**.  

### **Modifying the Factory (OCP Violation)**
```java
public class ShapeFactory {
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (shapeType.equalsIgnoreCase("TRIANGLE")) {  // New Modification
            return new Triangle();
        }
        return null;
    }
}
```
🚨 **Problem:** Every time we introduce a new shape, we modify the `ShapeFactory` class, which makes maintenance difficult and error-prone.

---

## **3. Solution: Using a Registry-Based Factory (Open/Closed Principle Compliant)**  
Instead of modifying `ShapeFactory`, we can **register new shapes dynamically** using a **Registry-Based Factory**. This way, we can add new shapes without modifying existing code.

### **Step 1: Define `Shape` Interface (Same as Before)**
```java
public interface Shape {
    void draw();
}
```

### **Step 2: Implement Concrete Shape Classes**
```java
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Circle");
    }
}

public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Rectangle");
    }
}

public class Triangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Triangle");
    }
}
```

### **Step 3: Create an Enum for Shape Types**
```java
public enum ShapeType {
    CIRCLE, RECTANGLE, TRIANGLE
}
```

### **Step 4: Implement a Registry-Based Factory**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.function.Supplier;

public class ShapeFactory {
    private static final Map<ShapeType, Supplier<Shape>> shapeRegistry = new HashMap<>();

    // Static block to register initial shapes
    static {
        registerShape(ShapeType.CIRCLE, Circle::new);
        registerShape(ShapeType.RECTANGLE, Rectangle::new);
    }

    // Method to register new shapes dynamically
    public static void registerShape(ShapeType type, Supplier<Shape> supplier) {
        shapeRegistry.put(type, supplier);
    }

    // Factory method to get a shape
    public static Shape getShape(ShapeType type) {
        Supplier<Shape> shapeSupplier = shapeRegistry.get(type);
        if (shapeSupplier != null) {
            return shapeSupplier.get();
        }
        throw new IllegalArgumentException("Unknown shape type: " + type);
    }
}
```

### **Step 5: Client Code**
```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        // Using the factory to create predefined shapes
        Shape shape1 = ShapeFactory.getShape(ShapeType.CIRCLE);
        shape1.draw();

        Shape shape2 = ShapeFactory.getShape(ShapeType.RECTANGLE);
        shape2.draw();

        // Registering a new shape dynamically without modifying the factory
        ShapeFactory.registerShape(ShapeType.TRIANGLE, Triangle::new);

        // Now the factory can create Triangle objects without modifying its code
        Shape shape3 = ShapeFactory.getShape(ShapeType.TRIANGLE);
        shape3.draw();
    }
}
```

---

## **4. Advantages of Registry-Based Factory**
✅ **Open/Closed Principle Compliant:**  
- We **never modify** `ShapeFactory` to add new shapes.  
- Instead, we **register new shapes dynamically**.  

✅ **More Maintainable and Scalable:**  
- Developers **don't need to edit existing code** to add new types.  
- The `ShapeFactory` remains **unchanged and stable**.  

✅ **Reduces Bugs & Testing Effort:**  
- Any modification to the factory would require regression testing.  
- Now, new shape implementations **don't affect existing factory logic**.  

---

## **5. Real-Time Use Case: Payment Gateway Factory**
### **Problem Statement**
A payment gateway integration needs different payment methods (PayPal, Credit Card, Google Pay). New payment methods are introduced frequently, and modifying an existing factory class every time is impractical.

### **Solution: Registry-Based PaymentFactory**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.function.Supplier;

interface PaymentGateway {
    void processPayment();
}

class PayPal implements PaymentGateway {
    @Override
    public void processPayment() {
        System.out.println("Processing payment via PayPal");
    }
}

class CreditCard implements PaymentGateway {
    @Override
    public void processPayment() {
        System.out.println("Processing payment via Credit Card");
    }
}

class PaymentFactory {
    private static final Map<String, Supplier<PaymentGateway>> paymentRegistry = new HashMap<>();

    static {
        registerPaymentMethod("PAYPAL", PayPal::new);
        registerPaymentMethod("CREDITCARD", CreditCard::new);
    }

    public static void registerPaymentMethod(String type, Supplier<PaymentGateway> supplier) {
        paymentRegistry.put(type, supplier);
    }

    public static PaymentGateway getPaymentMethod(String type) {
        Supplier<PaymentGateway> supplier = paymentRegistry.get(type);
        if (supplier != null) {
            return supplier.get();
        }
        throw new IllegalArgumentException("Unknown payment method: " + type);
    }
}

public class PaymentSystem {
    public static void main(String[] args) {
        PaymentGateway payment1 = PaymentFactory.getPaymentMethod("PAYPAL");
        payment1.processPayment();

        PaymentFactory.registerPaymentMethod("GOOGLEPAY", GooglePay::new);
        PaymentGateway payment2 = PaymentFactory.getPaymentMethod("GOOGLEPAY");
        payment2.processPayment();
    }
}

class GooglePay implements PaymentGateway {
    @Override
    public void processPayment() {
        System.out.println("Processing payment via Google Pay");
    }
}
```

---

## **Conclusion**
🔹 Traditional factory design leads to OCP violations when extending functionality.  
🔹 A **Registry-Based Factory** eliminates the need to modify existing code when adding new types.  
🔹 This approach is **widely used in frameworks like Spring** for dependency injection and service management.  
