# 1 面向对象和面向过程的区别

面向对象和面向过程是两种不同的编程范式，它们在设计思路、实现方式和程序设计风格等方面有明显的区别。

## 1.1 设计思路

- 面向过程是一种以函数为基本单位的编程方法，它的核心是算法。在这种范式中，程序员会将一个复杂的问题分解为多个步骤，每个步骤对应一个函数，通过调用这些函数实现整个问题的解决。
- 面向对象则是以对象为核心，通过封装、继承和多态等特性实现代码复用和扩展。在这种范式中，程序员会将构成问题的事物分解成各个对象，每个对象都有自己的属性和行为，通过对象之间的交互来解决问题。
  
## 1.2 实现方式

- 面向过程采用函数调用的方式实现功能。在这种方式下，程序按照函数调用的顺序依次执行，每个函数完成特定的任务。
- 面向对象采用对象的方式实现功能。在这种方式下，程序员会创建多个对象，每个对象都有自己的属性和方法，通过对象之间的交互来实现程序的功能。

## 1.3 程序设计风格

- 面向过程程序设计以函数为基本单位，结构化，适用于小型程序开发。在这种风格下，程序按照一系列函数的调用顺序来执行，每个函数都负责完成特定的任务。
- 面向对象程序设计以类和对象为基本单位，层次化，适用于大型程序开发。在这种风格下，程序由多个对象组成，每个对象都有自己的属性和方法，通过对象之间的交互来实现程序的功能。

此外，面向对象和面向过程在性能、可用性和扩展性等方面也存在差异。一般来说，面向过程在性能上较高，适用于对性能要求较高的场景，如单片机、嵌入式开发等。而面向对象在可用性、扩展性和可维护性方面更具优势，可以设计出低耦合、高内聚的系统，更易于维护和扩展。

# 2 面向对象三大特征

面向对象的三大特征包括封装、继承和多态。

1. 封装：封装是指将对象的状态信息隐藏在对象内部，不允许外部程序直接访问对象内部信息，而是通过该类提供的方法进行操作和访问。封装把同一类事物的共性（包括属性和方法）归到同一类中，方便使用。封装的目的在于隐藏实现细节，使得代码模块化，同时保护某些数据不被程序中的无关部分错误调用。
2. 继承：继承是面向对象编程（OOP）语言的一个主要功能。它允许使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。子类可以继承父类的属性和行为，这意味着父对象拥有的属性和行为，其子对象也拥有。这种继承关系类似于大自然中的物种遗传。
3. 多态：多态性是指允许将子类类型的指针赋值给父类类型的指针，使得父对象可以根据当前赋值给它的子对象的特性以不同的方式运作。多态性允许不同类的对象对同一消息作出响应，实现接口的重用。多态性包括参数化多态性和包含多态性。

# 3 重载和重写区别

重载（Overloading）和重写（Overriding）是面向对象编程中两个重要的概念，它们在Java等编程语言中得到了广泛应用。以下是它们之间的主要区别：

## 3.1 重载

在同一个类中，允许存在一个以上的同名方法，只要它们的参数列表（参数类型、参数个数或参数顺序）不同即可。重载的目的是让类以统一的方式处理不同类型的数据。

## 3.2 重写

子类可以重新定义父类中的方法，要求方法名、参数列表必须相同，但返回类型可以不同（子类方法的返回类型可以是父类方法返回类型的子类型）。重写的目的是子类可以对父类的行为进行修改或扩展。

## 3.3 区别

1. 重载发生在一个类中，属于编译时的多态性。在调用方法时，通过传递不同的参数个数和类型来决定具体使用哪个方法。重写发生在子类与父类之间，属于运行时的多态性。在运行时，根据对象的实际类型来决定调用哪个方法。
2. 重载要求同名方法的参数列表不同，但返回类型可以相同也可以不同。无法以返回类型来作为重载函数的区分标准。而重写要求方法名、参数列表相同，但返回类型可以是父类方法返回类型的子类型。此外，重写方法的访问权限不能比父类中被重写的方法的访问权限更严格。
3. 重载方法可以有不同的异常列表。重写方法不能抛出新的或更广泛的检查型异常，只能抛出与父类方法相同或更少的异常。

# 4 接口和抽象类有什么共同点和区别

## 4.1 接口和抽象类的相同点

1. **都不能被实例化**：无论是接口还是抽象类，都不能直接创建对象。对于接口，它定义的是一组行为规范，不能直接实例化；对于抽象类，它包含抽象方法，不能直接实例化。
2. **都可以包含抽象方法**：接口和抽象类都可以包含抽象方法，这些抽象方法用于描述类应该具备的功能，但不提供具体的实现。
3. **都可以被子类/实现类继承/实现**：抽象类可以被普通类继承，而接口可以被普通类实现。子类/实现类必须实现/重写所有的抽象方法。

## 4.2 接口和抽象类的差异

1. 抽象类是一种特殊的类，它不能被实例化，但可以包含普通成员变量和非抽象方法。接口则是一种完全抽象的类型，它只能包含抽象方法和常量。
2. 一个类只能继承一个直接父类，但可以实现多个接口。这是因为接口中只能包含抽象方法，没有具体的实现，因此实现多个接口并不会产生冲突。而抽象类则可以包含具体的实现，因此一个类只能继承一个抽象类，避免多重继承带来的复杂性。
3. 抽象类中可以包含非抽象方法，这些非抽象方法可以被子类继承并直接使用，这可以避免在子类中重复书写相同的代码，提高了代码的复用性。而接口中只能有抽象方法，不能包含非抽象方法。
4. 抽象类可以有构造方法，用于初始化抽象类中的普通成员变量。而接口不能有构造方法，因为接口不能被实例化。
5. 抽象类可以继承其他抽象类或具体的类，而接口只能继承其他接口，不能继承具体的类。

# 5 介绍private、protected、public

在Java编程语言中，`private`、`protected`和`public`是访问修饰符，用于设置类、变量、方法和构造函数的访问权限。它们决定了哪些其他类可以访问这些成员。以下是这三个访问修饰符之间的主要区别：

1. **public（公共的）**：
   - 当一个类的成员被声明为`public`时，它意味着该成员对所有的类都是可见的，并且可以在任何地方被访问。
   - `public`是访问权限最宽松的关键字，它允许任何类访问该成员，无论它们是否在同一个包内。
2. **private（私有的）**：
   - 当一个类的成员被声明为`private`时，它只能被声明它的类内部访问。
   - `private`是访问权限最严格的关键字，它防止任何其他类访问该成员。
   - `private`成员通常用于隐藏类的内部实现细节，确保类的封装性。
3. **protected（受保护的）**：
   - `protected`关键字允许成员被其所在类的子类以及同一个包内的其他类访问。
   - 如果两个类不在同一个包内，但是一个是另一个的子类，那么子类可以访问父类的`protected`成员。
   - `protected`提供了比`private`更宽松的访问权限，但比`public`更严格的访问权限。

# 6 构造方法有哪些特点？是否可被 override?

Java中的构造方法（也称为构造函数）具有以下特点：

1. **名称与类名相同**：构造方法的名称必须与类的名称完全相同，包括大小写。
2. **没有返回类型**：构造方法不能有返回类型，包括void。
3. **可以有参数**：构造方法可以带有参数，这些参数可以是任何类型，包括基本类型和引用类型。
4. **可以有多个**：一个类可以有多个构造方法，只要它们的参数列表不同（这被称为构造方法的重载）。
5. **自动调用**：当创建类的对象时，Java会自动调用与对象创建时提供的参数匹配的构造方法。
6. **可以访问类的私有成员**：构造方法可以访问类的私有成员，包括私有变量和私有方法。

在Java中，构造方法不能被覆盖（Override）。覆盖是面向对象编程中的一个概念，指的是子类可以提供一个与父类中方法名称、参数列表都相同的方法。当子类对象调用这个方法时，会执行子类中的实现，而不是父类中的实现。由于构造方法是用于初始化对象的，每个类都有自己特定的构造方法，因此不存在子类覆盖父类构造方法的情况。

注意，虽然构造方法不能被覆盖，但它们可以被继承。也就是说，子类可以继承父类的构造方法，但并不会直接继承。实际上，子类在创建对象时，会首先调用父类的构造方法（这可以通过super关键字实现），然后再执行子类自己的构造方法。

# 7 如果一个类没有声明构造方法，该程序能正确执行吗?

在Java中，如果一个类没有显式声明任何构造方法，程序仍然可以正确执行。这是因为Java编译器会自动为没有显式声明构造方法的类生成一个**默认的无参构造方法**

| 场景                                     | 能否正确执行？ | 原因                                                 |
| :--------------------------------------- | :------------- | :--------------------------------------------------- |
| 类未声明任何构造方法                     | ✅ 是           | 编译器自动生成默认无参构造方法。                     |
| 类显式声明了有参构造方法但无无参构造方法 | ❌ 否           | 必须显式声明无参构造方法才能调用`new ClassName()`。  |
| 父类无无参构造方法且子类未处理           | ❌ 否           | 子类必须通过`super(...)`显式调用父类的某个构造方法。 |

# 8 介绍一下`continue`、`break`和`return`

在Java编程语言中，`continue`、`break`和`return`是用于控制程序流程的关键字，但它们的功能和用途有所不同。以下是它们之间的主要区别：

1. continue：用于在循环中跳过当前迭代，并立即开始下一次迭代。当`continue`语句被执行时，循环的剩余部分（即`continue`之后的代码）将不会被执行，而是直接跳到下一次循环的开始。 `continue`只能用在循环语句（如`for`、`while`、`do-while`）中。
2. break：用于完全终止循环或`switch`语句。当`break`语句被执行时，它会立即退出当前循环或`switch`语句，程序流程将继续在循环或`switch`语句结构之后的第一条语句。`break`也可以用在嵌套循环中，以跳出多层循环。
3. return：用于从当前方法中退出，并返回到调用该方法的语句处。当`return`语句被执行时，它会结束当前方法的执行，并将控制权返回给调用该方法的代码。如果`return`语句后面带有值，它将返回该值给调用者。如果`return`语句后面没有值，它将返回`void`（对于返回类型为`void`的方法）。`return`不能用于循环或`switch`语句中，它只能用于方法中。

# 9 成员变量与局部变量的区别

成员变量和局部变量是两种不同类型的变量，它们在Java（以及其他许多编程语言）中有不同的定义、范围和生命周期。下面是它们之间的主要区别：

## 9.1. 定义

- **成员变量**：也被称为类的属性或字段。它们是在类的内部但在任何方法之外声明的。成员变量可以是任何数据类型，包括基本数据类型和引用数据类型。
- **局部变量**：它们是在方法或代码块内部声明的。局部变量的作用域仅限于声明它的代码块、方法或构造函数。

## 9.2 范围

- **成员变量**：成员变量属于类，所以它们在整个类中都是可见的。所有类的实例（对象）都共享相同的成员变量。
- **局部变量**：局部变量只在声明它的方法或代码块内可见。一旦方法或代码块执行完毕，局部变量就会被销毁。

## 9.3 生命周期

- **成员变量**：成员变量的生命周期与类的实例（对象）相同。只要对象存在，成员变量就存在。当对象被销毁时，成员变量也会被销毁。
- **局部变量**：局部变量的生命周期从它们被声明的地方开始，到它们所在的代码块、方法或构造函数结束为止。当代码块、方法或构造函数执行完毕时，局部变量就会被销毁。

## 9.4 默认值

- **成员变量**：如果没有显式地初始化成员变量，它们会被自动初始化为默认值（如基本数据类型的0或false，引用类型的null）。
- **局部变量**：局部变量在使用前必须显式初始化，否则编译器会报错。

## 9.5 存储位置

- **成员变量**：通常存储在堆内存中，与对象一起。
- **局部变量**：存储在栈内存中，因为它们是在方法或代码块执行期间创建的。

# 10 静态变量有什么作用

在Java中，静态变量（也被称为类变量）有几个主要的作用：

1. **数据共享**：静态变量是类级别的变量，而不是实例级别的。这意味着无论创建多少个类的实例（对象），它们都共享同一个静态变量的副本。因此，静态变量可以用于在多个对象之间共享数据或记录全局状态。
2. **节省内存**：由于静态变量属于类而不是实例，所以只会在内存中存在一份副本。这意味着无论创建多少个对象，静态变量都只会占用一份内存空间，从而节省了内存。
3. **方便访问**：静态变量可以通过类名直接访问，而无需创建类的实例。这使得在没有实例化对象的情况下也能够使用该变量。
4. **存储常量值**：静态变量经常用于存储常量值，如数学常量或系统配置参数。这些值在程序的生命周期中不会改变，因此使用静态变量是合适的。

# 11 静态方法为什么不能调用非静态成员

静态方法不能调用非静态成员（包括非静态变量和非静态方法）的原因主要有以下几个方面：

1. **生命周期不同**：静态方法属于类级别，与类本身相关，而不需要类的实例即可调用。非静态成员（变量和方法）则是与类的实例相关的，它们的生命周期与类的实例绑定在一起。当静态方法被调用时，可能没有类的实例存在，因此无法访问非静态成员。
2. **内存布局不同**：静态成员（变量和方法）在内存中只有一份副本，而非静态成员每个实例都有一份。静态方法无法直接访问实例级别的内存，因为它不依赖于任何特定实例。
3. **语义不符合**：静态方法通常用于执行与类相关但不依赖于任何特定实例的操作。如果静态方法调用非静态成员，那么这种调用就隐式地依赖于某个实例，这与静态方法的初衷相悖。
4. **设计原则**：面向对象设计原则鼓励将数据和操作封装在对象中，通过对象的方法来访问和操作数据。静态方法不依赖于对象实例，因此不应该直接访问实例级别的数据。
5. **线程安全**：如果静态方法能够直接访问非静态成员，那么在多线程环境下可能会导致数据不一致或竞态条件，因为不同的线程可能同时修改同一个非静态成员。

# 12 静态方法和实例方法有何不同？

静态方法和实例方法在Java等面向对象编程语言中具有不同的特性。它们的主要区别体现在以下几个方面：

1. **定义与声明**：
   - 静态方法：在方法声明时使用`static`关键字进行修饰。静态方法属于类，而不是类的实例。
   - 实例方法：不需要使用`static`关键字。实例方法是属于类的实例的，也就是属于对象的。
2. **调用方式**：
   - 静态方法：可以通过类名直接调用，也可以使用类的实例来调用。例如，`ClassName.methodName()`或`objectInstance.methodName()`。
   - 实例方法：只能通过类的实例来调用。例如，`objectInstance.methodName()`。
3. **访问成员变量**：
   - 静态方法：只能访问静态成员变量（即静态变量）和其他静态方法。它不能访问实例成员变量或实例方法。
   - 实例方法：可以访问静态成员变量和静态方法，也可以访问实例成员变量和其他实例方法。
4. **内存与速度**：
   - 静态方法：在程序开始时生成内存，并常驻内存。由于静态方法属于类级别，因此在内存中只有一份副本，可以提高访问速度。
   - 实例方法：在程序运行时生成内存，并且每次创建类的实例时都会为其分配内存空间。由于每个实例都有自己的方法副本，因此实例方法会占用更多的内存空间。
5. **用途与适用场景**：
   - 静态方法：通常用于执行与类相关但不依赖于任何特定实例的操作。它们通常用于实现工具类、工厂方法、单例模式等。
   - 实例方法：用于处理与类的实例相关的操作。它们是面向对象编程中封装数据和行为的主要方式。

# 13 介绍一下字节码

字节码（Bytecode）是Java源代码经过Java编译器（如`javac`）编译后生成的一种中间代码，这些代码不是直接由计算机的CPU执行的机器码，而是由Java虚拟机（JVM）执行的。字节码是一种与平台无关的代码格式，它包含了Java类文件的结构和Java虚拟机指令。

Java字节码文件通常以`.class`为扩展名，并且通常包含在JAR（Java Archive）文件中。字节码文件包含了Java程序中的类、接口、字段、方法和字节码指令等信息。

采用字节码的好处主要有以下几点：

1. **跨平台性**：由于字节码是针对Java虚拟机设计的，而不是针对特定的硬件或操作系统，因此Java程序可以在任何安装了JVM的设备上运行，从而实现了“一次编写，到处运行”的目标。
2. **安全性**：字节码在加载到JVM之前，可以通过各种安全机制进行校验，以确保其安全性和完整性。这有助于防止恶意代码的执行。
3. **性能优化**：JVM可以对字节码进行各种优化，如即时编译（JIT, Just-In-Time compilation），将热点代码编译成高效的本地机器码，从而提高执行效率。
4. **版本兼容性**：通过字节码，Java平台可以支持多个版本并存，因为不同版本的Java类库可以加载不同版本的字节码。这使得老版本的Java程序可以在新版本的Java平台上运行，只要新版本的平台支持老版本的字节码格式。
5. **网络传输和分发**：字节码文件比源代码文件小，且平台无关，因此适合在网络上进行传输和分发，方便Java程序的部署和更新。
6. **动态加载**：JVM支持动态加载字节码，这允许在运行时加载和编译新的代码，这对于实现插件机制、代码热替换等高级功能非常有用。
