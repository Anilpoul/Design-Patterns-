# **Factory Design Pattern in Java**

The **Factory Design Pattern** is a creational pattern that provides an interface for creating objects but allows subclasses to alter the type of objects that will be created. It promotes loose coupling by encapsulating the object creation logic.

---

## **Implementation of Factory Design Pattern in Java**

Here's a simple example using a `Shape` interface and its implementations (`Circle`, `Rectangle`, and `Square`). A `ShapeFactory` class will be responsible for creating objects.

### **Step 1: Create the `Shape` Interface**
```java
public interface Shape {
    void draw();
}
```

### **Step 2: Implement the Interface in Different Classes**
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

public class Square implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Square");
    }
}
```

### **Step 3: Create the Factory Class**
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
        } else if (shapeType.equalsIgnoreCase("SQUARE")) {
            return new Square();
        }
        return null;
    }
}
```

### **Step 4: Use the Factory in Client Code**
```java
public class FactoryPatternDemo {
    public static void main(String[] args) {
        ShapeFactory shapeFactory = new ShapeFactory();

        Shape shape1 = shapeFactory.getShape("CIRCLE");
        shape1.draw();

        Shape shape2 = shapeFactory.getShape("RECTANGLE");
        shape2.draw();

        Shape shape3 = shapeFactory.getShape("SQUARE");
        shape3.draw();
    }
}
```

---

## **Breaking Ways and Solutions**

### **1. Tight Coupling (Breaking Dependency Inversion)**
- The `ShapeFactory` class directly instantiates concrete classes (`Circle`, `Rectangle`, `Square`).
- This makes the factory inflexible to adding new shapes.

#### **Solution: Use Reflection or Dynamic Binding**
Modify `ShapeFactory` to avoid direct class dependencies.
```java
public class ShapeFactory {
    public Shape getShape(Class<? extends Shape> shapeClass) {
        try {
            return shapeClass.getDeclaredConstructor().newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

**Usage:**
```java
ShapeFactory factory = new ShapeFactory();
Shape circle = factory.getShape(Circle.class);
circle.draw();
```

---

### **2. Hardcoded Strings (String Dependency)**
- Using `"CIRCLE"`, `"RECTANGLE"`, etc., can lead to typos and debugging issues.

#### **Solution: Use Enums**
```java
public enum ShapeType {
    CIRCLE, RECTANGLE, SQUARE
}
```

Modify `ShapeFactory`:
```java
public class ShapeFactory {
    public Shape getShape(ShapeType type) {
        switch (type) {
            case CIRCLE: return new Circle();
            case RECTANGLE: return new Rectangle();
            case SQUARE: return new Square();
            default: throw new IllegalArgumentException("Unknown shape type");
        }
    }
}
```

**Usage:**
```java
Shape shape = factory.getShape(ShapeType.CIRCLE);
shape.draw();
```

---

### **3. Extending Factory Without Modifying It (Open/Closed Principle Violation)**
- When adding new shapes, we modify `ShapeFactory`, violating OCP.

#### **Solution: Use a Registry-Based Factory**
```java
import java.util.HashMap;
import java.util.Map;
import java.util.function.Supplier;

public class ShapeFactory {
    private static final Map<ShapeType, Supplier<Shape>> shapeMap = new HashMap<>();

    static {
        shapeMap.put(ShapeType.CIRCLE, Circle::new);
        shapeMap.put(ShapeType.RECTANGLE, Rectangle::new);
        shapeMap.put(ShapeType.SQUARE, Square::new);
    }

    public static void registerShape(ShapeType type, Supplier<Shape> supplier) {
        shapeMap.put(type, supplier);
    }

    public Shape getShape(ShapeType type) {
        Supplier<Shape> shapeSupplier = shapeMap.get(type);
        if (shapeSupplier != null) {
            return shapeSupplier.get();
        }
        throw new IllegalArgumentException("Unknown shape type");
    }
}
```

Now, new shapes can be added without modifying `ShapeFactory`:
```java
ShapeFactory.registerShape(ShapeType.TRIANGLE, Triangle::new);
```

---

## **Conclusion**

The **Factory Design Pattern** is useful for decoupling object creation logic from the client. However, it can be improved by:

1. Avoiding direct instantiation using reflection.
2. Using enums instead of hardcoded strings.
3. Implementing a registry-based factory for better extensibility.

## **Advanced Example: Abstract Factory Pattern**

The **Abstract Factory Pattern** provides an interface for creating families of related or dependent objects without specifying their concrete classes.

### **Step 1: Create Abstract Factory**
```java
public interface AbstractFactory<T> {
    T create(String type);
}
```

### **Step 2: Implement Concrete Factories**
```java
public class ShapeFactory implements AbstractFactory<Shape> {
    @Override
    public Shape create(String type) {
        if (type.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (type.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        } else if (type.equalsIgnoreCase("SQUARE")) {
            return new Square();
        }
        return null;
    }
}
```

```java
public class ColorFactory implements AbstractFactory<Color> {
    @Override
    public Color create(String type) {
        if (type.equalsIgnoreCase("RED")) {
            return new Red();
        } else if (type.equalsIgnoreCase("BLUE")) {
            return new Blue();
        }
        return null;
    }
}
```

### **Step 3: Create Factory Producer**
```java
public class FactoryProducer {
    public static AbstractFactory<?> getFactory(String choice) {
        if (choice.equalsIgnoreCase("SHAPE")) {
            return new ShapeFactory();
        } else if (choice.equalsIgnoreCase("COLOR")) {
            return new ColorFactory();
        }
        return null;
    }
}
```

### **Step 4: Use Abstract Factory**
```java
public class AbstractFactoryPatternDemo {
    public static void main(String[] args) {
        AbstractFactory<Shape> shapeFactory = (AbstractFactory<Shape>) FactoryProducer.getFactory("SHAPE");
        Shape shape1 = shapeFactory.create("CIRCLE");
        shape1.draw();
    }
}
```

---

## **Conclusion**

The **Factory Design Pattern** is useful for decoupling object creation logic from the client. However, it can be improved by:

1. Avoiding direct instantiation using reflection.
2. Using enums instead of hardcoded strings.
3. Implementing a registry-based factory for better extensibility.
4. Using **Abstract Factory Pattern** for creating families of related objects.


