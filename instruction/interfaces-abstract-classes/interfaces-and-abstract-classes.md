# Interfaces and Abstract Classes

🖥️ [Slides](https://docs.google.com/presentation/d/17S-Y7Og08S9kRWHZfnH8k2wTBht39aCd/edit?usp=sharing&ouid=114081115660452804792&rtpof=true&sd=true)

📖 **Required Reading**: Core Java for the Impatient

- Chapter 3:
  - Section 1 - Interfaces
  - Section 2 - Static, Default, and Private Methods
  - Section 3 - Examples of Interfaces
- Chapter 4:
  - Section 1 - Extending a Class
  - Section 2 - Object: The Cosmic Superclass
  - Section 3 - Enumerations

## Polymorphism

Polymorphism is the blanket term used in computer science to represent the idea of morphing an object to fit into many (i.e. poly) different contexts. In Java, the use of inheritance and abstract classes are the primary ways to provide polymorphism. You use the `extends` keyword to inherit another class's functionality, and you use the `implements` keyword to inherit an interface definition. Polymorphism allows you to decouple, or abstract, a class's internals, from how it is used. That makes it so you can significantly alter the class without having to change how the class is used.

## Interfaces

Interfaces allow you to define what something does, without specifying how it does it. It also allows you to create and supply alternative implementations for the interface. For example, you can have an interface that defines what a `List` can do, and then create classes that provide different implementations of the `List`. Perhaps one implementation uses less memory, and a different one is faster. You can then write code that uses the `List` interface and not have to think about if it is using the fast version or the memory efficient version.

The following example shows two implementations of a `List`. One that uses an array, and one that uses a linked list. The two lists can be passed to a function, `addAndPrint` in this case, that doesn't know anything about the implementation of the list, it just knows that it can call the interface's `add` method.

```java
import java.util.List;
import java.util.ArrayList;
import java.util.LinkedList;

public class ListExample {
  public void listExample() {
    // Represent two different implementations of the List interface.
    List<String> list1 = new ArrayList<>();
    List<String> list2 = new LinkedList<>();

    addAndPrint(list1, "vanilla");
    addAndPrint(list2, "taco");
  }

  // This function takes any implementation of the List interface.
  private void addAndPrint(List<String> list, String value) {
    // The add method is defined on the list interface.
    list.add(value);
    System.out.println(value);
  }
}
```

Note that, because `addAndPrint()` doesn't know anything about `list`'s type except that it implements the `List` interface, it can only call methods on `list` that are declared in the interface. If `LinkedList` had a method that was not declared in `List`, and `addAndPrint()` tried to call that method, it would not compile, even when passed a `LinkedList`.

## Writing Your Own Interface

In addition to using a class that implements one of the JDK standard interfaces, you can write your own interface implementations, and even define completely new interfaces. Creating an interface is similar to creating a class, except you use the `interface` keyword and only define the signature of the methods that the interface represents.

For example, if you wanted to create a specialized iterator that returned each character in a string you could write the following:

```java
public interface CharIterator {
    /** Returns true if there is another character to iterate. */
    boolean hasNext();

    /** Returns the next character. */
    char next();
}
```

You can then implement the `CharIterator` interface by specifying the `implements` keyword after the class name declaration, and writing each of the methods defined by the interface. The `@Override` annotation is not technically necessary, but it makes it clear that the method is part of an interface.

```java
public class AlphabetIterator implements CharIterator {
    int current = 0;
    String charString = "abcdefg";

    @Override
    public boolean hasNext() {
        return current < charString.length();
    }

    @Override
    public char next() {
        return charString.charAt(current++);
    }
}
```

## Abstract Classes

Abstract classes provide another type of polymorphism. However, unlike interfaces, where the subclass implements all of the functionality of the interface, an abstract class provides some of the implementation and leaves other methods to be implemented by the subclass.

Here is an example of an abstract class that implements the JDK's `Iterator` interface, but also defines a new abstract method for iterating with a string prefix. This is done by specifying the `abstract` keyword on the `nextWithPrefix` method, without providing an implementation of the method.

You can think of abstract classes as a class/interface hybrid.

```java
/**
 * The abstract keyword signifies that this class contains methods
 * that must be implemented by a subclass
 */
public static abstract class AlphabetIterator implements Iterator {
    int current = 0;
    String charString = "abcdefg";

    public boolean hasNext() {
        return current < charString.length();
    }

    public Object next() {
        return charString.substring(current, ++current);
    }

    /**
     * This method is not implemented by the abstract class and
     * so it must be implemented by the subclass.
     */
    public abstract String nextWithPrefix(String prefix);
}
```

A subclass `extends` the abstract class by providing an implementation of the abstract `nextWithPrefix` method. Additionally, for instructive purposes, we also override the `next` method to include a default prefix that represents the numerical order of the iterated items.

Note the use of the `super` keyword in the `nextWithPrefix` function. `super` allows you to access methods in the abstract base class when you have methods with the same name as the subclass. In this example, it is used to access the abstract class's `next` function instead of the `next` function implemented by `PrefixAlphabetIterator`. Without the use of `super`, the call to `next` would have created an infinite loop.

```java
public static class PrefixAlphabetIterator extends AlphabetIterator {
    public String next() {
        return nextWithPrefix(String.format("%d.", current + 1));
    }

    public String nextWithPrefix(String prefix) {
        return String.format("%s. %s", prefix, super.next());
    }
}
```

Like interfaces, the name of the abstract class can be used to represent the subclass when passed to functions that expect the abstract class. In the following code, we create an object of the type `PrefixAlphabetIterator` and then pass it to a function that expects the abstract class `AlphabetIterator`.

```java
public static void main(String[] args) {
    var iter = new PrefixAlphabetIterator();
    print(new PrefixAlphabetIterator());
}


public static void print(AlphabetIterator iter) {
    while (iter.hasNext()) {
        System.out.println(iter.nextWithPrefix("+ "));
    }
}
```

## instanceof

Polymorphism is great because it makes it so code only needs to know about the provided interface. For example, if you want to have a list that can contain any type of object that extends the `Object` class, it can safely ignore the fact that those objects are also things like String, Integer, Person, or Map classes. However, sometimes you need to know if an object is of a specific type so that you can use it in different ways. This is where the `instanceof` operator comes in handy. `instanceof` will return true, if the provided variable is of the given type (including if it extends or implements it). A simple example of its use is demonstrated with a test to see if a string literal is an instance of type `String`.

```java
if ("I am a string" instanceof String) {
  // Always true
}
```

In the following example, we create a list that contains objects of different primitive types. We then iterate over this list and use the `instanceof` operator to execute different code based on the type of the list item.

```java
public static void main(String[] args) {
    List<Object> list = List.of('a', "b", 3);

    for (var item : list) {
        if (item instanceof String) {
            System.out.println("String");
        } else if (item instanceof Integer) {
            System.out.println("Integer");
        } else if (item instanceof Character) {
            System.out.println("Character");
        }
    }
}
```

## final

If you don't want a subclass to override a method in a base class then you can prefix the method with the keyword `final`. You can also use the final keyword to make a class's field value immutable. (For `final` fields containing objects, however, you can still change them by calling their mutator methods if they have any. It only prevents the variable from being reassigned.)

```java
public class FinalExample {
    /** This variable cannot be changed */
    final double PI = 3.14;

    /** This method cannot be overridden */
    final public double getPI() {
        return PI;
    }
}
```

## Things to Understand

- What polymorphism is
- What abstract classes are and when to use them
- What interfaces are and when to use them
- How interfaces and abstract classes are both similar and different
- How to implement an interface in a class
- How to create an interface
- How to do inheritance in Java

## Videos

- 🎥 [Polymorphism](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=23d2e58e-9628-43a4-9aaa-ad640141e7dc&start=0)
- 🎥 [Polymorphism Example Part 1](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=88adc709-e900-47d6-9e9a-ad64014400ad&start=0)
- 🎥 [Polymorphism Example Part 2](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=ebfcd403-53e4-4b68-8a3d-ad6401453df4&start=0)
- 🎥 [Polymorphism Example Part 3](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=f451dd38-e32d-445f-be0d-ad6401470c45&start=0)
- 🎥 [Creating an Interface](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=2da0fb3a-7aca-4344-a42b-ad640149f9e2&start=0)
- 🎥 [Implementing an Interface](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=f7ec17c1-c815-429b-8ffd-ad64014b0921&start=0)
- 🎥 [Inheritance](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=74faf0ca-9a24-4800-8ded-ad5d014f9493&start=0)
- 🎥 [The Final Keyword](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=c8298a1a-b65c-43bd-8928-ad64013a3b89&start=0)
