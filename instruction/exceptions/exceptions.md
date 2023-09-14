# Java Exceptions

🖥️ [Slides](https://docs.google.com/presentation/d/1CIyKxxGhJXUCQvwsJT64Oaao0p9LcBIZ/edit?usp=sharing&ouid=114081115660452804792&rtpof=true&sd=true)

📖 **Required Reading**: Core Java for the Impatient

- Chapter 5: Exceptions, Assertions, And Logging. (_Only read sections 5.1-5.1.9: Exception Handling_)

Java exceptions allow you to escape out of the normal execution flow of a program when something exceptional happens. You can then centrally handle the exception at a location higher in the code execution stack.

Java uses the standard `try`, `throw`, and `catch` syntax that are found in most programming languages. You define a block where exceptions can occur with the `try` statement. The `try` block is then followed by one or more `catch` blocks. For each `catch` block you can specify what exception the block handles. The runtime will pick the block that most specifically matches your exception. If you want to handle all exceptions, then you can specify the `Exception` base class in your catch block.

```java
try {
    // Code that might throw an exception
} catch (FileNotFoundException ex) {
    // Specific file error handling
} catch (Exception ex) {
    // General error handling
}

```

## Throw and Throws

You use the `throw` keyword followed by the allocation of a new exception in order to raise an exception.

```java
throw new IllegalArgumentException("Missing required parameter");
```

When you throw an exception the normal flow of your code is interrupted and the execution pointer will skip to the closest catch block in the execution stack.

You can throw any exception from a function, but Java requires that your function signature declares all of the exceptions that the function throws. Note that the declaration requirement propagates to any function that calls a function that can throw an exception.

```java
void top() {
    try {
        B();
    } catch (Exception ex) {
        System.out.println("this WILL execute");
    }
}

void A() throws Exception {
    B();
    System.out.println("this will NOT execute");
}

void B() throws Exception {
    C();
    System.out.println("this will NOT execute");
}

void C() throws Exception {
    throw new Exception("declarations all the way up");
    System.out.println("this will NOT execute");
}
```

The exclusion to the `throws` declaration rule, is when you throw what is known as an unchecked exception. Unchecked exceptions are defined as any exception that derived from the `RuntimeException` class. The reason for unchecked exceptions is that they can be thrown at anytime and so it is unreasonable to explicitly handle them on every function in your code.

## Finally

You can also use the `try` syntax to create a block of code that always gets executed whenever the try block exits. This is called a finally block. The finally block is executed whether or not an exception is throw.

```java
try {
    // Code that may throw an exception
} finally {
    // Code that always gets called
}
```

## Example

Consider the example of a program that requires a configuration file in order to work correctly. If the file does not exist, then you want report the error from your `main` function and not deep down in the initialization code where the file fails to load.

Note the use of multiple `catch` blocks, the use of `finally`, and also the necessity of declaring the exceptions that may be thrown.

```java
import java.io.File;
import java.io.FileNotFoundException;

public class ExceptionExample {
    public static void main(String[] args) {
        // Exceptions are handled centrally for anything that happens in this scope.
        try {
            var example = new ExceptionExample();
            example.loadConfig();
        } catch (FileNotFoundException ex) {
            System.out.printf("Required file not found: %s", ex);
        } catch (Exception ex) {
            System.out.printf("General error: %s", ex);
        } finally {
            System.out.println("Program completed");
        }
    }

    private void loadConfig() throws Exception {
        loadConfigFile("user");
        loadConfigFile("system");
    }

    // Note that the function indicates that it can throw an exception.
    private void loadConfigFile(String location) throws FileNotFoundException {
        var file = new File(location);
        if (!file.exists()) {
            // Let the code above know there was an exception.
            throw new FileNotFoundException();
        }

        // Otherwise load the configuration
    }
}
```

## Exceptions Should be Exceptional

Remember that exceptions should be exceptional. Do not throw exceptions for things that happen in the normal flow of your code. For example, if it is expected that sometimes a file may not be found, then that is not exceptional. Also do not throw exceptions simply to return values from a function. For example, a token parser should not throw exceptions in order to return tokens that it parses to anyone with a catch block.

Using exceptions for non-exceptional cases makes debugging much more difficult and creates unexpected side effects in your code that make it less maintainable.

## Things to Understand

- The difference between checked and unchecked exceptions in Java
- How and when to handle an exception in Java
- How and when to declare an exception in Java
- How to use try/catch blocks
- What finally blocks are and how to use them

## Videos

- 🎥 [Exceptions](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=83d5acf8-12b7-473d-919d-ad6b0124631b&start=0)
- 🎥 [Checked vs. Unchecked Exceptions](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=3e7b6f62-13e5-41e6-9a81-ad6b012e8b25&start=0)

## Demonstration code

📁 [ExceptionRethrowingExample](example-code/ExceptionRethrowingExample.java)

📁 [ExceptionThrowingExample](example-code/ExceptionThrowingExample.java)

📁 [FileReadingWithExceptions](example-code/FileReadingWithExceptions.java)

📁 [FileReadingWithoutExceptions](example-code/FileReadingWithoutExceptions.java)

📁 [FinallyExample](example-code/FinallyExample.java)

📁 [ImageEditorException](example-code/ImageEditorException.java)

📁 [TryCatchExample](example-code/TryCatchExample.java)

📁 [TryWithResourcesExample](example-code/TryWithResourcesExample.java)
