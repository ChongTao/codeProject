## 1 @Configuration

用于定义配置类，可替换xml配置文件。被注解的类内部包含有一个或多个被`@Bean`注解的方法，这些方法将会被`AnnotationConfigApplicationContext`或`AnnotationConfigWebApplicationContext`类进行扫描，并用于构建bean定义，初始化Spring容器。需要注意的是，`@Configuration`不可以是final类型，也不可以是匿名类，嵌套的configuration必须是静态类。



## 2 @Component

让Spring容器在加载时自动添加到容器里面的注解，标注在类上，在Spring容器中相当于一个Bean标签。当组件不属于任何层的时候（如层次不明确的工具类），我们可以使用这个注解进行标注。



## 3 @Service

用于标注业务逻辑层的组件（如Service类），这样Spring就可以识别并管理这个类的对象，并将其纳入Spring容器中。



## 4 @Repository

用于标注数据访问组件，即DAO组件，这样Spring就可以识别并管理这个类的对象，并将其纳入Spring容器中。



## 5 @Controller

用于标注控制层组件（如struts中的action），这样Spring就可以识别并管理这个类的对象，并将其纳入Spring容器中。

## 6 @Autowired

用于为类的属性、构造器、方法进行注值。它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。通过byType方式完成自动装配，如果没有找到匹配的bean，那么在运行时，将会抛出异常。如果找到多个匹配的bean，那么Spring的IoC容器将无法决定使用哪个bean，也会导致异常。因此，此注解最好和`@Qualifier`注解一起使用。

## 7  @Value

用于注入基本类型和String类型的数据。例如，`@Value("${app.name}")` 可以注入名为app.name的配置项的值。

## 8 @Bean

标注在一个方法上，这个注解可以给这个方法一个name属性，用于给这个Bean对象取一个唯一的名称，便于调用。



## 9 @PropertySource

用于加载.properties 文件中的配置。关键字classpath，表示类路径下。