---
description: Managing, writing, and working with errors.
---

# Exception Handling

## Exception Handling

In Java, exception handling is performed using the `try`, `catch`, and `finally` keywords, along with a hierarchy of `Exception` classes. We first begin our discussion with an example of the `try`-`catch`-`finally` framework being implemented.

{% code lineNumbers="true" %}
```java
try {
  reader = new FileReader(filename);
  scanner = new Scanner(reader);
  value = scanner.nextInt();
}
catch (FileNotFoundException e) {
    System.err.println("Unable to open " + filename + " " + e);
}
catch (InputMismatchException e) {
    System.err.println("Unable to scan for an integer");
}
catch (NoSuchElementException e) {
    System.err.println("No input found");
}
finally {
  if (scanner != null)
    scanner.close();
}
```
{% endcode %}

In the first `try` block, we have a piece of code that tries to open a file and read information in the file. However,  note that several things may go wrong along the way. For example, the user may have put in the wrong file name, or the file may not contain the information we want, and so on.&#x20;

Each different possible error is caught by a different `catch` block, with `catch` blocks for `FileNotFoundException`, `InputMismatchException`, and `NoSuchElementException` in the example above. In Java, exceptions are instances of a subtype under the `Excpetion` class, that is, exceptions are objects too.

Our code ends with a `finally` block, that is run after all exceptions have been handled. This block of code is optional, and will _always_ run (regardless of whether a `catch` block contains a return statement, or whether no errors are ever thrown).

We observe that we can generalize the syntax for the `try`-`catch`-`finally` framework as follows:

{% code lineNumbers="true" %}
```java
try {
    // do something
} catch (an exception parameter) {
    // handle exception
} finally {
    // clean up code
    // regardless of there is an exception or not
}
```
{% endcode %}

A common mistake by programmers is to write a `catch` block that catches the general exception `Exception`. This is known as **Pokemon Exception Handling**, and is terrible practice because every exception is now caught, and the programmer will not know whether the exception is actually being handled.

{% code lineNumbers="true" %}
```java
try {
  // your code
}
catch (Exception e) {
  // do nothing
}
```
{% endcode %}

## Creating Exceptions

With the `try`-`catch`-`finally` framework from above, we are now able to handle different types of exceptions. We now turn to the task of throwing our own exceptions. For example, we now want our `Circle` constructor to throw an exception if the client tries and initializes a `Circle` with a negative radius.

{% code lineNumbers="true" %}
```java
public Circle(Point c, double r) throws IllegalArgumentException {
  if (r < 0) {
    throw new IllegalArgumentException("radius cannot be negative.");
  }
  this.c = c;
  this.r = r;
}
```
{% endcode %}

We now use the `throws` keyword to declare that our method will throw an exception, and to actually throw an exception, we create a new `Exception` object, and throw it to the caller using the `throw` keyword.

## Checked and Unchecked Exceptions

Java distinguishes between two types of exceptions: checked and unchecked exceptions.

* An **unchecked exception** is an exception caused by programmer error, and should not occur if perfect code is written. These errors are not explicitly caught or thrown (since they should never exist!) In Java, all unchecked exceptions are subclasses of the class `RuntimeException`.
* A **checked exception** is an exception that a programmer has no control over (and are likely a result of user error), and all checked exceptions must be explicitly handled for the program to compile.

Beyond checked and unchecked exceptions, Java also has an `Error` class for situations where the program should terminate as there is no way to recover from the error. For example, a `StackOverflowError` and `OutOfMemoryError` occur when the stack or the heap is full. In CS2030S, we don't need to create or care about such errors.

## Control Flow Mechanisms

One good thing about Java exception handling is that we need not handle the exception where it is thrown. If the exception is thrown within a method that is nested within several other calls, it is possible for it to pass the handling of the exception to the method that it is encapsulated within, and so on.

If an unchecked exception is not caught in a specific block, it will propagate down the stack until it is either caught in another block, or an error message is displayed to the user. Well-written programs should handle checked exceptions gracefully, and hide the details from the user.

In general, we should not use exception handling as a control flow mechanism. Exceptions are meant to identify and solve potential issues with the program, not act as a set of fancy `if`-`else` statements.

## Creating New Exceptions

Java allows the programmer to create new types of extensions, through inheriting an existing exception class. Here is an example of a new exception class, called `IllegalCircleException`.

{% code lineNumbers="true" %}
```java
class IllegalCircleException extends IllegalArgumentException {
  Point center;
  IllegalCircleException(String message) {
    super(message);
  }
  IllegalCircleException(Point c, String message) {
    super(message);
    this.center = c;
  }
  @Override
    public String toString() {
      return "The circle centered at " + this.center + " cannot be created:" + getMessage();
    }
}
```
{% endcode %}

When we override a method that throws a checked exception, the overriding method must throw only the same, or a more specific checked exception, than the overriden method. This is done so that LSP is enforced, and the child method is substitutable for the parent method.
