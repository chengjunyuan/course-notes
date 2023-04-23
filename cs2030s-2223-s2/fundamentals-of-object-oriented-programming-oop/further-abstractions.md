---
description: Abstract classes, interfaces, and wrapper classes.
---

# Further Abstractions

## Abstract Classes

In Java, an **abstract class** is a class which has been made into something so general that it cannot and should not be instantiated. This is usually because one or more of its instance methods cannot be implemented without further details. To declare an abstract class in Java, we add the `abstract` keyword to the class declaration. To declare an **abstract method** in Java, we add the `abstract` keyword to the method.

{% code lineNumbers="true" %}
```java
abstract class Shape { // abstract class
    abstract public double getArea(); // abstract method
}
```
{% endcode %}

Abstract methods cannot be implemented, and therefore do not have a method body. Abstract classes only need to have one abstract method - they can have other non-abstract methods as well. In the example above, we can have many different types of `Shape` objects, such as circles, triangles, and rectangles. As the formula for the area of each type of shape is different, we should leave the `getArea` method as an abstract method, to be overriden by each subclass of `Shape`.

A **concrete class** is a class that is not abstract, i.e., a class without abstract methods. Concrete classes must override all abstract methods of the parent class, and therefore be instantiable and fully usable.

## Interfaces

Observe that an abstract class is just a nice way of saying we want our group of objects to all have the same set of methods, which each concrete subclass has a different implementation for. We can package this idea in an abstraction called an **interface**, which is declared using the `interface` keyword.

{% code lineNumbers="true" %}
```java
interface GetAreable {
  public abstract double getArea();
}
```
{% endcode %}

All the methods in an interface are `public abstract` by default, and we can make our classes implement an interface through the `implements` keyword.

{% code lineNumbers="true" %}
```java
class Flat extends RealEstate implements GetAreable {
    private int numOfRooms;
    private String block;
    private String street;
    private int floor;
    private int unit;

    @Override
    public double getArea() {
        :
    }
}
```
{% endcode %}

One way of thinking of our interface is that our interface is a set of methods that a class _must_ possess in order to be functional. As every method in the interface is an abstract method, we therefore force the programmer to provide an overriden method implementation for the methods in the interface.

Both concrete and abstract classes can implement an interface. However, a class can only extend from one superclass, but can implement multiple interfaces. Likewise, an interface can extend from one or more other interfaces, but an interface cannot extend from another class. One interesting effect of this is that if a class `C` implements an interface `I`, we have the subtyping relationship `C<:I`. This allows us to make the following declarations and type casting.

{% code lineNumbers="true" %}
```java
interface I {
  :
}

class A {
  :
}

class B implements I {
  :
}

class C extends B {
  :
}

I i1 = new B();
I i2 = (I) new A();
I i3 = (I) new C();
```
{% endcode %}

Although the compile-time type of our variables `i1`, `i2`, and `i3` is `I`, our run-time type will be updated to the different classes. Java allows us to explicitly type cast interfaces, since the compiler cannot rule out the possibility that a subclass such as `C` also implements an interface as well. However, the programmer has to be careful, as a totally unrelated class like `A` may also be typecasted as `I`, leading to a run-time error in the future.

Finally, Java actually allows interfaces to provide default implementation of methods that all implementation subclasses will inherit (unless they override it). These methods are tagged with the `default` keyword and leads to inelegant situations where an interface has both abstract and non-abstract methods. These are referred to as **impure interfaces**, and will not be discussed in CS2030S.

## Wrapper Classes

Java also provides **wrapper classes** for each of its primitive types. Wrapper classes encapsulate a type, rather than fields and methods. This allows us to manipulate primitive types as if they're objects, allowing us to apply methods to them too. All primitive wrapper classes are **immutable** - once an object is created, it cannot be changed.

As changing between the primitive type and its wrapper class is pretty common, Java provides a feature called **auto-boxing/unboxing** to perform type conversions between primitive types and its wrapper class.

{% code lineNumbers="true" %}
```java
Integer i = 4;
int j = i;
```
{% endcode %}

Although working with wrapper classes is nice, we also want to avoid this where necessary, because changing our primitive types to objects increases the memory and garbage collection costs of the program, making it less efficient than primitive types.
