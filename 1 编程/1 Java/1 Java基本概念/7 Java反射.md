反射是 Java 语言的一种动态机制，允许程序在运行时获取类的元数据（如类名、方法、字段、注解等），并动态操作类或对象的行为（如创建实例、调用方法、修改字段）。其核心是通过 Class 对象实现对类的反向解析和操作，突破了编译时类型检查的限制。

## 1 反射的功能

- 动态获取类信息：通过类名获取 Class 对象。
- 创建对象：通过无参/有参构造器实例化对象。
- 调用方法：动态执行任意类的方法（包括私有方法）。
- 访问/修改字段：读写对象的字段值（包括私有字段）。
- 泛型擦除后获取泛型信息：通过反射可以获取方法或字段的泛型类型（如 List<String> 中的 String）。

## 2 使用

1. **获取Class对象**：这是反射的第一步，也是核心步骤。Class对象代表了类的元信息，通过它可以获取类的所有信息。获取Class对象的方法有多种，包括调用对象的`getClass()`方法、调用类的`class`属性、使用`Class.forName()`静态方法等。

   - **使用`.class`语法**： 这是获取`Class`对象的最直接方式。对于任何类，你都可以使用`.class`语法来获取其`Class`对象。

     ```java
     Class<?> c1 = String.class;
     ```

   - **使用`getClass()`方法**： 所有对象都有一个`getClass()`方法，该方法返回该对象的运行时类的`Class`对象。

     ```java
     String s = "Hello";
     Class<?> c2 = s.getClass();
     ```

   - **使用`Class.forName()`方法**： 这个方法在运行时动态地加载类，并返回对应的`Class`对象。通常用于加载配置文件或数据库驱动等。

     ```java
     try {
        Class<?> c3 = Class.forName("java.lang.String");
     } catch (ClassNotFoundException e) {
        e.printStackTrace();
     }
     ```

   - **使用类加载器loadClass()**： 自定义类加载逻辑（如热部署、插件化）。

     ```java
     // 示例
     ClassLoader classLoader = MyClass.class.getClassLoader();
     Class<?> clazz = classLoader.loadClass("com.example.MyPlugin");
     ```

2. **获取方法**：通过Class对象，可以获取类中的所有方法。这可以通过调用`getDeclaredMethods()`方法实现，该方法返回一个包含类中所有方法（包括私有方法）的数组。如果只想获取公共方法，可以使用`getMethods()`方法。

3. **方法调用**：在获取了方法之后，就可以通过反射来调用这些方法了。这可以通过`Method`类的`invoke()`方法实现。在调用`invoke()`方法时，需要传入一个对象作为调用方法的实例（如果是静态方法，则传入`null`），以及方法的参数（如果有的话）。

下面是一个简单的示例代码，演示了如何使用反射来调用一个类的方法：

```java
// 获取Class对象
Class<?> cls = Class.forName("com.example.MyClass");

// 获取方法
Method method = cls.getDeclaredMethod("myMethod", int.class, String.class);

// 创建对象
Object obj = cls.newInstance();

// 调用方法
Object result = method.invoke(obj, 20, "Hello");

// 处理结果
System.out.println(result);
```

在这个示例中，我们首先通过`Class.forName()`方法获取了`MyClass`类的Class对象。然后，通过`getDeclaredMethod()`方法获取了`myMethod`方法，该方法接受一个`int`类型和一个`String`类型的参数。接着，通过`newInstance()`方法创建了`MyClass`类的一个实例。最后，通过`invoke()`方法调用了`myMethod`方法，并传入了相应的参数。调用结果会被返回并打印出来。

需要注意的是，在使用反射时，需要处理可能抛出的各种异常，如`ClassNotFoundException`、`NoSuchMethodException`、`InstantiationException`、`IllegalAccessException`、`InvocationTargetException`等。同时，由于反射会破坏封装性并可能带来安全问题，因此在使用时需要谨慎考虑其代价和收益。

## 2 反射的典型使用场景

1. **框架设计**：反射是许多Java框架的核心组成部分，如Spring、Hibernate和Struts等。这些框架使用反射来动态地创建对象、注入依赖、调用方法等，从而实现灵活的配置和扩展。
2. **插件机制**：在插件式架构中，反射允许主程序在运行时动态地加载和调用插件的功能。插件可以是实现了特定接口的类，主程序通过反射来发现、加载和实例化这些插件，从而扩展程序的功能。
3. **测试**：反射在单元测试框架（如JUnit）中得到了广泛应用。测试框架通过反射来调用被测试类的私有方法和属性，从而实现对代码的全面测试。
4. **ORM（对象关系映射）**：在ORM框架中，反射用于将数据库表的数据映射到Java对象中。框架通过反射来访问对象的属性和方法，从而实现数据的持久化和检索。
5. **动态代理**：反射是实现动态代理的关键技术之一。动态代理允许程序在运行时创建一个代理对象来替代目标对象，从而实现对目标对象的调用进行拦截和处理。
6. **序列化与反序列化**：反射在Java对象序列化和反序列化过程中发挥着重要作用。通过反射，序列化框架可以获取对象的所有属性和方法信息，并将其转换为字节流进行存储或传输。在反序列化时，框架通过反射将这些字节流恢复为原始对象。
7. **代码生成**：反射可以用于在运行时动态生成代码。例如，编译器可以使用反射来生成Java字节码，从而实现动态编译和加载新功能。

## 3 反射的代价与注意事项

- 性能开销：反射操作比直接调用慢约 1~2 个数量级（可通过缓存 Method/Field 对象优化）。
- 安全限制：反射可以绕过访问修饰符（如调用私有方法），需谨慎使用。
- 代码可维护性：过度使用反射会导致代码难以理解和调试。
- 兼容性问题：反射依赖类结构，类名或方法名变更会导致运行时错误。
