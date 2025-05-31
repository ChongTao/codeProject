双亲委派模型是Java类加载器的一种实现方式，其核心思想是在类加载器之间建立一种父子关系，父类加载器加载的类能够被子类加载器使用，而子类加载器加载的类不能被父类加载器所使用。这种模型的主要目的是为了保证Java核心类库的安全性和避免类的重复加载。

具体来说，双亲委派模型的工作流程是：当一个类加载器收到了加载某个类的请求时，它并不会直接去加载该类，而是把这个请求委派给父类加载器。每一个层次的类加载器都是如此，因此所有的类加载请求最终都会传送到顶端的启动类加载器。只有当父类加载器在其搜索范围内无法找到所需的类，并将该结果反馈给子类加载器时，子类加载器才会尝试自己去加载。

这种模型有以下几个优点：

1. **安全性**：通过双亲委派模型，Java核心类库中的类可以被保护起来，防止被随意修改。因为双亲委派模型保证了Java核心类库中的类只能由启动类加载器来加载，而启动类加载器只能从文件系统中的特定位置（如<JAVA_HOME>\lib目录）加载类，这就避免了核心类库被篡改的风险。
2. **避免类的重复加载**：在双亲委派模型中，由于每个类加载器都会先尝试将类加载请求委派给父类加载器，这就保证了同一个类只会被加载一次，避免了类的重复加载。
3. **模块化开发**：在实际应用中，我们经常需要在一个程序中使用多个第三方库，这些库可能会存在同名类。如果使用了双亲委派模型，就可以保证不同的类加载器只会加载自己的类，从而避免了类名冲突的问题。

需要注意的是，虽然双亲委派模型有很多优点，但它也有一些局限性。例如，在某些情况下，我们可能需要打破这种模型，例如在使用自定义的类加载器加载非核心类库的类时。此时，我们就需要实现自己的类加载器，并覆盖双亲委派模型的默认行为。

# 2 类加载器有哪些

在 Java 中，类加载器（ClassLoader）负责将类的字节码加载到内存并生成对应的 Class 对象。以下是 Java 类加载器的分类及其核心作用：

## 2.1 类加载器的层次结构

Java 类加载器采用 双亲委派模型（Parent Delegation Model），其层次关系如下：
Bootstrap ClassLoader（根加载器）> Extension ClassLoader（扩展类加载器）> Application ClassLoader（应用类加载器）> 自定义 ClassLoader（用户自定义加载器）

1. Bootstrap ClassLoader（启动类加载器）：

   - 由 C/C++ 实现，是 JVM 的一部分。
   - 负责加载 `JAVA_HOME/lib` 下的核心类库（如 `rt.jar`、`charsets.jar` 等）。
   - **不继承 `java.lang.ClassLoader`**，是其他类加载器的祖先。

   加载 Java 核心类库（如 java.lang.*、java.util.* 等）。加载路径：<JAVA_HOME>/jre/lib 目录下的核心类（如 rt.jar、charsets.jar）。

2. Extension ClassLoader（扩展类加载器）：

   - 由 `sun.misc.Launcher$ExtClassLoader` 实现。
   - 负责加载 `JAVA_HOME/lib/ext` 目录下的扩展类库。

   加载 Java 的扩展类库。由 sun.misc.Launcher$ExtClassLoader 实现。加载路径：<JAVA_HOME>/jre/lib/ext 目录下的 JAR 包。

3. Application ClassLoader（应用程序类加载器）：

   - 由 `sun.misc.Launcher$AppClassLoader` 实现。
   - 负责加载用户类路径（`classpath`）下的类（即用户编写的代码和第三方依赖）。

   加载用户类路径（ClassPath）下的类。由 sun.misc.Launcher$AppClassLoader 实现。加载路径：由 -classpath 或 -cp 参数指定的路径。

4. 自定义 ClassLoader（用户类加载器）。实现动态加载、热部署、隔离类等特殊需求。实现：继承 ClassLoader 并重写 findClass() 方法。
