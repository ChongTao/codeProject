# 1 介绍一下java异常

Java中的异常处理是编程中的一个重要概念，它有助于处理程序执行过程中可能遇到的错误和异常情况。Java中的异常可以分为两大类：已检查异常（Checked Exceptions）和未检查异常（Unchecked Exceptions）。

# 1.1 已检查异常（Checked Exceptions）

已检查异常是编译器要求必须处理的异常。这些异常通常是由于编程错误或环境问题导致的，例如文件找不到、网络中断等。常见的已检查异常包括`IOException`、`ClassNotFoundException`等。处理已检查异常通常需要使用`try-catch`语句块。例如：

```java
try {
    // 可能会抛出已检查异常的代码
    FileInputStream fileInput = new FileInputStream("nonexistent.txt");
} catch (IOException e) {
    // 处理异常的代码
    e.printStackTrace();
}
```

# 1.2 未检查异常（Unchecked Exceptions）

未检查异常是编译器不要求必须处理的异常。这些异常通常是由于程序逻辑错误导致的，例如空指针访问、数组越界等。常见的未检查异常包括`NullPointerException`、`ArrayIndexOutOfBoundsException`等。虽然编译器不要求处理未检查异常，但最佳实践是仍然应该捕获并处理这些异常，以确保程序的健壮性和稳定性。处理未检查异常同样可以使用`try-catch`语句块。例如：

```java
try {
    // 可能会抛出未检查异常的代码
    String str = null;
    int length = str.length();
} catch (NullPointerException e) {
    // 处理异常的代码
    e.printStackTrace();
}
```

# 2 Throwable 类常用方法有哪些？

Throwable类在Java中是用来表示错误和异常的超类。它有几个常用的方法，主要包括：

1. **String getMessage()**：这个方法返回关于发生的异常的详细信息。它通常返回的是异常的原因或描述。
2. **String toString()**：这个方法返回异常的类型以及异常的详细信息。它的返回值通常包含了异常的类名和getMessage()方法返回的信息。
3. **void printStackTrace()**：这个方法将异常的详细信息打印到标准错误流（通常是控制台）。它打印的信息包括了异常的类名、异常信息以及异常在程序中的位置（即出现异常的行号）。这个方法在调试程序时非常有用，因为它可以帮助我们找到异常的来源。

这些方法在异常处理和调试中都是非常有用的。通常，我们在捕获异常后，会使用这些方法中的一个或多个来获取和处理异常的详细信息。例如，我们可以使用getMessage()方法来获取异常的原因，或者使用printStackTrace()方法来打印异常的详细信息以便调试。

# 3 介绍一下try-catch-finally

在Java中，`try-catch-finally`是一种异常处理机制，用于捕获和处理程序运行时可能出现的异常。以下是`try-catch-finally`的基本使用方式：

```java
try {
    // 尝试执行的代码块
    // 可能会抛出异常的代码
} catch (ExceptionType1 e) {
    // 当try块中抛出ExceptionType1类型的异常时执行的代码块
    // 处理异常的代码
} catch (ExceptionType2 e) {
    // 当try块中抛出ExceptionType2类型的异常时执行的代码块
    // 处理异常的代码
} finally {
    // 无论是否发生异常，都会执行的代码块
    // 通常用于释放资源
}
```

- `try`块：这是包含可能引发异常的代码块。当try块中的代码抛出异常时，程序会立即跳转到与该异常类型匹配的`catch`块。
- `catch`块：这是用于捕获并处理异常的代码块。你可以定义多个`catch`块来捕获不同类型的异常。每个`catch`块后面跟着的是异常类型和其对应的变量，用于存储捕获到的异常对象。在`catch`块中，你可以编写处理异常的代码，例如打印错误信息、记录日志、回滚事务等。
- `finally`块：这是无论是否发生异常都会执行的代码块。通常，我们在`finally`块中释放资源，例如关闭数据库连接、文件流等。这是因为，即使发生异常，我们也希望确保这些资源
- 得到正确释放，以避免资源泄漏。

请注意，`try-catch-finally`结构是可选的，你可以只使用`try-catch`或`try-finally`。但是，`try`块是必需的，因为没有`try`块，就没有要捕获的异常。

# 4 finally 中的代码一定会执行吗？

`finally`块中的代码在Java中通常是一定会执行的，无论`try`块中的代码是否成功执行，或者是否发生了异常，或者`catch`块中的代码是否执行了return语句。但是，有几个特殊情况需要注意：

1. **System.exit() 调用**：如果你的`finally`块中或者由`finally`块调用的代码调用了`System.exit(int status)`方法，那么JVM将终止，此时`finally`块之后的代码（如果有的话）将不会执行。
2. **线程中断**：如果`finally`块中的代码执行时间过长，线程可能会被中断（通过`Thread.currentThread().interrupt()`方法）。虽然`finally`块中的代码通常会执行，但如果中断发生在`finally`块执行期间，并且该代码没有适当地处理中断，那么`finally`块中的代码可能不会完全执行完。
3. **关闭JVM**：如果`finally`块中的代码或由其调用的代码尝试关闭JVM（这通常是不建议的），那么JVM可能会关闭，导致`finally`块之后的代码不执行。
4. **异常发生在`finally`块中**：如果`finally`块中的代码抛出异常，并且这个异常没有被捕获（即没有内嵌的`try-catch`块来处理它），那么该异常会终止`finally`块的执行，并且如果`try`或`catch`块中有`return`语句，该返回值不会被使用。
5. **关闭的流或资源**：如果`finally`块试图关闭一个已经关闭的资源（如流），则可能会抛出异常，但这不会影响`finally`块中其他代码的执行。

# 5 throw和throws的区别

在Java中，`throw`和`throws`都是与异常处理相关的关键字，但它们在使用和含义上有显著的区别。

`throws`关键字主要用于声明方法可能会抛出的异常类型。它出现在方法的函数头（即方法签名中，紧跟在方法名后面，并在参数列表之前），用于告知该方法的调用者该方法可能会抛出哪些类型的异常。这样，调用该方法的代码就可以根据这些信息来决定是否需要进行异常处理。例如：

```java
public void myMethod() throws IOException {
    // ... 方法体，可能会抛出IOException
}
```

在这种情况下，`myMethod`的调用者需要处理或继续抛出`IOException`。

而`throw`关键字则用于实际抛出异常对象。它出现在方法体内，当程序遇到某种它不能处理或不想处理的情况时，就可以使用`throw`来抛出一个异常对象。这会导致程序立即转入异常处理阶段，并且后面的代码将不再执行。例如：

```java
public void anotherMethod() {
    try {
        // ... 尝试执行某些操作
    } catch (SomeException e) {
        throw e; // 重新抛出捕获到的异常
    }
}
```

在这个例子中，`anotherMethod`捕获了一个`SomeException`异常，然后使用`throw`关键字将其重新抛出。这通常发生在方法需要将异常传递给其上层调用者处理时。

总结一下，`throws`用于声明方法可能抛出的异常类型，而`throw`用于实际抛出异常对象。另外，`throws`出现在方法签名中，而`throw`出现在方法体内。`throws`是一种消极处理异常的方式，它表明方法不处理异常，而是将异常交给上层调用者处理；而`throw`则是积极处理异常的一种方式，它直接抛出异常对象。

# 6 Exception 和 Error 有什么区别？

在Java等编程语言中，Exception和Error都是表示程序运行过程中遇到的问题或错误的类型，但它们之间存在一些关键的区别。

- **可恢复性**：`Exception`通常表示程序运行过程中可预见的、可以被处理的问题，这些问题通常是由于程序员的编程错误或者程序逻辑错误导致的。当程序遇到`Exception`时，可以通过异常处理机制来捕获并处理这些异常，使程序能够继续执行。而Error则通常表示程序运行过程中的严重问题，这些问题通常是由于系统资源不足、JVM内部错误等导致的，是无法恢复的错误。当程序遇到Error时，通常无法通过代码来处理，需要通过修复代码或者调整环境来解决。

- **分类**：`Exception`和`Error`都是继承自`Throwable`类，是异常处理机制的基本组成类型。但`Exception`是程序正常运行过程中预料到可能会出现的错误，分为运行时异常（RuntimeException）和编译时异常（非RuntimeException）。运行时异常通常表示程序运行时的错误，如空指针异常、数组越界异常等，这些异常在编译时可能不会被检查出来。编译时异常则表示在编译时就能检查出来的错误，如文件找不到异常、类找不到异常等，这些异常必须在编写代码时进行显式的处理。而Error则是程序运行中不可预料的异常情况，如OutOfMemoryError、NoClassDefFoundError等，这些错误通常是由于系统资源不足、JVM内部错误等导致的。

总的来说，Exception和Error都是程序中可能遇到的问题，但它们在可恢复性、分类等方面存在一些区别。在实际编程中，我们需要正确地处理这些异常和错误，以保证程序的正确性和稳定性。
