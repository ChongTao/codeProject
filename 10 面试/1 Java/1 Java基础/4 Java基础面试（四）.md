# 1 介绍一下Java的泛型

泛型（Generics） 是Java 5引入的特性，允许在定义类、接口或方法时使用类型参数，从而将类型检查从运行时提前到编译时，提高代码的安全性和重用性。泛型的核心思想是参数化类型，即让代码操作的数据类型不固定，而是通过具体使用场景指定。

```java
// 泛型类
class Box<T> {
    private T content;
    public void setContent(T content) { 
        this.content = content; 
    }
    public T getContent() { return content; }
}

// 使用时指定具体类型
Box<String> stringBox = new Box<>();
stringBox.setContent("Hello");
String value = stringBox.getContent(); // 无需强制转换
```

- 类型安全：编译器检查类型合法性，避免运行时ClassCastException。
- 消除强制转换：直接使用正确类型，代码更简洁。
- 代码复用：一套逻辑可适配多种类型，减少重复代码。

# 2 介绍一下泛型的使用场景

1. **泛型类**：这是最常见的泛型使用方式。你可以定义一个类，并在类名后面添加类型参数。例如，`ArrayList<E>`就是一个泛型类，其中`E`是一个类型参数，代表该ArrayList可以存储的元素的类型。
2. **泛型接口**：与泛型类类似，你也可以定义泛型接口。泛型接口中定义的方法可以引用类型参数。
3. **泛型方法**：这是Java 5引入的新特性。你可以在方法签名中定义类型参数，这样，方法就可以处理不同类型的参数并返回相应类型的结果。泛型方法的一个关键特点是，类型参数只能代表方法中的返回类型或局部变量的类型，不能代表类中的成员变量的类型。

# 2 介绍一下反射

反射是 Java 语言的一种动态机制，允许程序在运行时获取类的元数据（如类名、方法、字段、注解等），并动态操作类或对象的行为（如创建实例、调用方法、修改字段）。其核心是通过 Class 对象实现对类的反向解析和操作，突破了编译时类型检查的限制。

## 2.1 反射的核心能力

- 动态获取类信息：通过类名获取 Class 对象。
- 创建对象：通过无参/有参构造器实例化对象。
- 调用方法：动态执行任意类的方法（包括私有方法）。
- 访问/修改字段：读写对象的字段值（包括私有字段）。
- 泛型擦除后获取泛型信息：通过反射可以获取方法或字段的泛型类型（如 List<String> 中的 String）。

## 2.2 反射的典型使用场景

1. 框架开发（如 Spring、MyBatis）
框架需要通过反射实现 动态加载类、自动装配依赖、注解解析 等核心功能：

## 2.3 反射的代价与注意事项

- 性能开销：反射操作比直接调用慢约 1~2 个数量级（可通过缓存 Method/Field 对象优化）。
- 安全限制：反射可以绕过访问修饰符（如调用私有方法），需谨慎使用。
- 代码可维护性：过度使用反射会导致代码难以理解和调试。
- 兼容性问题：反射依赖类结构，类名或方法名变更会导致运行时错误。

# 4 获取 Class 对象的四种方式

在Java中，获取`Class`对象（即类的元信息）的四种常见方式如下：

1. **使用`.class`语法**： 这是获取`Class`对象的最直接方式。对于任何类，你都可以使用`.class`语法来获取其`Class`对象。

```java
Class<?> c1 = String.class;
```

2. **使用`getClass()`方法**： 所有对象都有一个`getClass()`方法，该方法返回该对象的运行时类的`Class`对象。

```java
String s = "Hello";
Class<?> c2 = s.getClass();
```

3. **使用`Class.forName()`方法**： 这个方法在运行时动态地加载类，并返回对应的`Class`对象。通常用于加载配置文件或数据库驱动等。

```java
   try {
       Class<?> c3 = Class.forName("java.lang.String");
   } catch (ClassNotFoundException e) {
       e.printStackTrace();
   }
   ```

4. **使用类加载器loadClass()**： 自定义类加载逻辑（如热部署、插件化）。

```java
// 示例
ClassLoader classLoader = MyClass.class.getClassLoader();
Class<?> clazz = classLoader.loadClass("com.example.MyPlugin");
```

