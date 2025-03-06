# Design-Patterns-
- Design Patterns are the well proved soluutions of commonly occuring problems in software design.

# Core Java Design patterns:
1) Creational Design Pattern:-
- Factory pattern
- Builder pattern
- singleton pattern etc.

2) Structural Design pattern:-
- Proxy pattern
- Adapter pattern etc.

3) Behavioral Design pattern:-
- Observer pattern
- state pattern
- Iterator pattern

# CREATIONAL DESIGN PATTERN :-
1) Singleton Pattern:-
- The Singleton Design Pattern ensures that a class has only one instance and provides a global point of access to that instance.
- Below are different ways to implement the Singleton Design Pattern in Java.
________________________________________
1. Eager Initialization Singleton
This is the simplest way, where the instance is created at the time of class loading.
public class Singleton {

    private static final Singleton instance = new Singleton();

    // Private constructor to prevent instantiation
    private Singleton() {}

    public static Singleton getInstance() {
        return instance;
    }
}
•	Pros: Simple and thread-safe.
•	Cons: Instance created even if not needed.
________________________________________
2. Lazy Initialization Singleton
The instance is created only when it is requested.
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
•	Pros: Instance created only when needed.
•	Cons: Not thread-safe.
________________________________________
3. Thread-Safe Singleton (Synchronized Method)
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
•	Pros: Thread-safe.
•	Cons: Performance overhead due to synchronization.
________________________________________
4. Double-Checked Locking (Best Practice for Singleton)
This reduces the overhead of synchronization after the instance is initialized.
public class Singleton {

    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {    // First check (no locking)
            synchronized (Singleton.class) {
                if (instance == null) {   // Second check (with locking)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
•	Pros: Thread-safe and high performance.
•	Cons: More complex code.
________________________________________
5. Bill Pugh Singleton (Recommended)
This approach uses a static inner class to hold the instance.
public class Singleton {

    private Singleton() {}

    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
•	Pros: Thread-safe, lazy-loaded, and clean.
•	Cons: None.
________________________________________
6. Enum Singleton (Most Preferred)
Since Enums are thread-safe by design and prevent multiple instantiation, this is the simplest and safest method.
public enum Singleton {
    INSTANCE;

    public void show() {
        System.out.println("Singleton using Enum");
    }
}
•	Pros: Thread-safe, lazy-loaded, serialization-safe.
•	Cons: Enum cannot extend another class.
________________________________________

# Which One to Use?
---------------------------------------------------------------------------------
Approach	              | Thread Safety	| Lazy Initialization	| Performance   |
---------------------------------------------------------------------------------
Eager Initialization	  |    ✅	        |        ❌	          |      ✅       |
Lazy Initialization	    |    ❌	        |        ✅	          |      ✅       |
Synchronized Method	    |    ✅	        |        ✅	          |      ❌       |
Double-Checked Locking	|    ✅	        |        ✅	          |      ✅       |  
Bill Pugh Singleton	    |    ✅	        |        ✅	          |      ✅       |
Enum Singleton	        |    ✅	        |        ✅	          |      ✅       |
----------------------------------------------------------------------------------
✅ Recommended: Bill Pugh Singleton or Enum Singleton for most cases.


  
