# **Singleton Pattern in Java**

The **Singleton Design Pattern** ensures that a class has only one instance and provides a global point of access to that instance.

### **1. Eager Initialization Singleton**
```java
public class Singleton {
    private static final Singleton instance = new Singleton();
    private Singleton() {}
    public static Singleton getInstance() {
        return instance;
    }
}
```
**Pros:** Simple and thread-safe.  
**Cons:** Instance created even if not needed.

### **2. Lazy Initialization Singleton**
```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
**Pros:** Instance created only when needed.  
**Cons:** Not thread-safe.

### **3. Thread-Safe Singleton (Synchronized Method)**
```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
**Pros:** Thread-safe.  
**Cons:** Performance overhead due to synchronization.

### **4. Double-Checked Locking Singleton**
```java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
**Pros:** Thread-safe and high performance.  
**Cons:** More complex code.

### **5. Bill Pugh Singleton (Recommended)**
```java
public class Singleton {
    private Singleton() {}
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```
**Pros:** Thread-safe, lazy-loaded, and clean.  
**Cons:** None.

### **6. Enum Singleton (Most Preferred)**
```java
public enum Singleton {
    INSTANCE;
    public void show() {
        System.out.println("Singleton using Enum");
    }
}
```
**Pros:** Thread-safe, lazy-loaded, serialization-safe.  
**Cons:** Enum cannot extend another class.

### **Which One to Use?**

| Approach                 | Thread Safety | Lazy Initialization | Performance |
|--------------------------|--------------|----------------------|-------------|
| Eager Initialization     | ✅            | ❌                   | ✅           |
| Lazy Initialization      | ❌            | ✅                   | ✅           |
| Synchronized Method      | ✅            | ✅                   | ❌           |
| Double-Checked Locking   | ✅            | ✅                   | ✅           |
| Bill Pugh Singleton      | ✅            | ✅                   | ✅           |
| Enum Singleton           | ✅            | ✅                   | ✅           |

✅ **Recommended:** Bill Pugh Singleton or Enum Singleton for most cases.

