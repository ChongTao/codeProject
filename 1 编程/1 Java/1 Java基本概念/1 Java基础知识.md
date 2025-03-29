# 1 ==和equal的区别

在Java中，`==` 和 `equals()` 是用来比较对象是否相等的，但它们在使用和行为上有明显的区别：

1. ==操作符
用于比较两个对象的内存地址是否相等（引用类型）或者值是否相等。

```java
int a = 1;
int b = 1;
System.out.println(a == b) // 基本类型是直接比较值

String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2); // 两个对象的地址不同
```

2. equal()方法
默认情况下与==操作符相同，但是如果类重写该方法，大部分是比较内容是否一致（如String类）。

```java
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1.equal(s2)); // 两个对象的地址不同
```

| **场景**         | `==`                       | `equals()`                   |
| :--------------- | :------------------------- | :--------------------------- |
| **基本数据类型** | 比较值                     | 不可用（基本类型不是对象）   |
| **引用类型**     | 比较内存地址是否相同       | 默认比较地址，重写后比较内容 |
| **字符串比较**   | 常量池优化可能相等，否则不 | 总是比较内容（推荐用法）     |


