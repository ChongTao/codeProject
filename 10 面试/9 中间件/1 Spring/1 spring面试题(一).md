## 1 spring bean的生命周期

Spring Bean 的生命周期涉及从实例化到销毁的多个阶段，每个阶段都提供了扩展点供开发者干预，如下图：

![img](https://pic3.zhimg.com/v2-7604e6df5eba7b28ba978f09fa42737a_r.jpg)

1. **Bean定义加载与BeanFactoryPostProcessor**

- **Bean定义加载**：容器读取配置元数据（XML、注解或Java配置），解析并注册Bean的定义。

- **BeanFactoryPostProcessor**：在Bean实例化前，允许修改Bean的定义（如处理占位符`${}`）。

  ```java
  public class CustomBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
      @Override
      public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) {
          // 修改BeanDefinition
      }
  }
  ```

2. **实例化Bean**

- 通过构造函数或工厂方法创建Bean的实例。

3. **属性注入（依赖注入）**

- 根据配置注入依赖（通过Setter、构造函数或自动装配）。

4. **Aware接口回调**

- 调用一系列Aware接口方法，使Bean获取容器信息：

  - **BeanNameAware**：设置Bean的名称。

  - **BeanFactoryAware**：设置BeanFactory引用。

  - **ApplicationContextAware**：设置ApplicationContext引用（还包括ResourceLoaderAware、EnvironmentAware等）。

    ```java
    public class MyBean implements BeanNameAware {
        @Override
        public void setBeanName(String name) {
            // 获取Bean的名称
        }
    }
    ```

5. **BeanPostProcessor前置处理**

- **postProcessBeforeInitialization()**：在初始化前进行自定义处理（如修改Bean属性）。

  ```java
  public class CustomBeanPostProcessor implements BeanPostProcessor {
      @Override
      public Object postProcessBeforeInitialization(Object bean, String beanName) {
          // 前置处理逻辑
          return bean;
      }
  }
  ```

6. **初始化阶段**

- **@PostConstruct注解方法**：优先执行。

- **InitializingBean的afterPropertiesSet()**：属性设置完成后执行。

- **自定义init-method**：通过XML或`@Bean(initMethod = "init")`指定。

  ```java
  public class MyBean implements InitializingBean {
      @PostConstruct
      public void postConstruct() {
          // 初始化逻辑1
      }
  
      @Override
      public void afterPropertiesSet() {
          // 初始化逻辑2
      }
  
      public void customInit() {
          // 初始化逻辑3
      }
  }
  ```

7. **BeanPostProcessor后置处理**

- **postProcessAfterInitialization()**：在初始化后进行最终处理（如生成代理对象）。

  ```java
  public class CustomBeanPostProcessor implements BeanPostProcessor {
      @Override
      public Object postProcessAfterInitialization(Object bean, String beanName) {
          // 后置处理逻辑（如AOP代理）
          return bean;
      }
  }
  ```

8. **Bean就绪**

- 此时Bean已完全初始化，可被应用程序使用。

9. **销毁阶段**

- **@PreDestroy注解方法**：优先执行。

- **DisposableBean的destroy()**：释放资源。

- **自定义destroy-method**：通过XML或`@Bean(destroyMethod = "cleanup")`指定。

  ```java
  public class MyBean implements DisposableBean {
      @PreDestroy
      public void preDestroy() {
          // 销毁逻辑1
      }
  
      @Override
      public void destroy() {
          // 销毁逻辑2
      }
  
      public void customCleanup() {
          // 销毁逻辑3
      }
  }
  ```

10. **DestructionAwareBeanPostProcessor（可选）**

- 在Bean销毁前执行自定义逻辑（如资源释放）

  ```java
  public class CustomDestructionProcessor implements DestructionAwareBeanPostProcessor {
      @Override
      public void postProcessBeforeDestruction(Object bean, String beanName) {
          // 销毁前处理
      }
  }
  ```




## 2 spring容器生命周期



## 3 @Resource和@Autowire区别

`@Resource` 和 `@Autowired` 是 Java 中用于依赖注入（Dependency Injection, DI）的两种常用注解，但它们有以下关键区别：

1.**来源不同**

- **`@Autowired`**
  属于 **Spring 框架**（`org.springframework.beans.factory.annotation`），是 Spring 特有的注解。
- **`@Resource`**
  属于 **Java 标准**（`javax.annotation`），基于 JSR-250 规范，由 Java EE 提供，不依赖 Spring。

2.注入方式不同

- **`@Autowired`**： 默认按 **类型（ByType）** 注入。如果存在多个相同类型的 Bean，需结合 `@Qualifier` 指定名称。

  ```java
  @Autowired
  @Qualifier("userServiceImplA")
  private UserService userService;
  ```

- **`@Resource`**：默认按 **名称（ByName）** 注入。若未指定 `name` 属性，则按字段名或属性名匹配 Bean；若未找到，则回退到按类型注入。

  ```java
  @Resource(name = "userServiceImplA")
  private UserService userService;
  ```

3. **适用范围**

- **`@Autowired`**:  支持构造函数、方法、参数、字段等更广泛的注入场景。
- **`@Resource`**: 主要用于字段和方法注入，不支持构造函数注入。

## 4 spring IOC 原理

Spring的IOC（Inversion of Control，控制反转）原理是一种设计思想，旨在降低代码之间的耦合度。在传统的Java设计中，对象会在其内部通过`new`关键字直接创建所依赖的对象。而在Spring的IOC容器中，这种创建和依赖关系的管理被反转给了容器（即DI）。

IOC的工作原理如下：

1. **定义对象及其依赖关系**：首先，开发者需要定义需要Spring容器管理的对象（Bean），以及这些对象之间的依赖关系。这通常通过配置文件（如XML文件）或注解来完成。
2. **容器创建和管理对象**：Spring容器（如`DefaultListableBeanFactory`）负责根据定义创建对象，并管理这些对象的生命周期。当容器启动时，它会读取配置文件或注解，识别需要创建的对象，并根据依赖关系进行初始化。
3. **注入依赖**：在对象创建过程中，Spring容器会自动将所依赖的对象注入到目标对象中。这可以通过多种方式实现，如构造函数注入、setter方法注入等。通过这种方式，目标对象无需自己创建依赖对象，而是从容器中获取，从而实现了控制反转。

通过使用IOC，开发者可以将对象的创建和依赖关系的管理交给Spring容器来处理，从而降低了代码之间的耦合度。这使得代码更加灵活、可维护，并提高了系统的可扩展性。



## 14 Spring如何解决循环依赖

在Spring框架中，循环依赖是指两个或多个Bean相互依赖，形成一个闭环，导致Spring容器在初始化这些Bean时无法解析依赖关系。为了解决这个问题，Spring提供了以下几种策略和方法：

1. **单例模式与原型模式的区别**：

   - 单例模式（默认）：Spring IoC容器中每个Bean的定义只有一个实例。在这种情况下，Spring使用三级缓存来解决循环依赖问题。
   - 原型模式：每次请求都会创建一个新的Bean实例。由于每次都会创建新实例，所以循环依赖问题就不存在了。

2. **三级缓存**： Spring IoC容器使用三级缓存来解决单例模式下的循环依赖问题。

   - 一级缓存（singletonObjects）：存储已完全初始化的Bean。
   - 二级缓存（earlySingletonObjects）：存储早期暴露的Bean引用（即还未完成所有依赖注入，但已经可以被其他Bean引用的Bean）。
   - 三级缓存（singletonFactories）：存储创建Bean的工厂对象。

   当容器创建Bean A时，如果Bean A依赖Bean B，而Bean B又依赖Bean A，Spring会先尝试从一级缓存中获取Bean A的实例。如果找不到，Spring会检查Bean A是否已经处于创建过程中（即Bean A的实例已经在二级缓存中）。如果是，Spring会从二级缓存中获取Bean A的早期引用并注入到Bean B中，同时Bean A会继续创建。完成所有依赖注入后，Bean A会被放入一级缓存中。

3. **构造器注入与setter注入**：

   - 构造器注入（constructor injection）：通过在构造函数中传入依赖来初始化Bean。由于构造函数的调用发生在Bean的实例创建时，所以循环依赖问题难以通过构造器注入来解决。
   - Setter注入（setter injection）：通过setter方法设置Bean的依赖。由于setter方法的调用发生在Bean的实例创建之后，所以setter注入更容易解决循环依赖问题。

4. **使用`@Lazy`注解**： 在依赖注入时，可以使用`@Lazy`注解来延迟初始化Bean。这样，当Spring容器遇到循环依赖时，它会等待需要的时候再去初始化这个Bean。

5. **避免循环依赖**： 最佳实践是尽量避免代码中出现循环依赖。这通常意味着需要重新设计代码结构，使得Bean之间的依赖关系更加清晰和简洁。

总的来说，Spring使用三级缓存机制和`@Lazy`注解来解决循环依赖问题。然而，最佳的做法仍然是避免在代码中创建循环依赖，因为这通常意味着代码结构需要改进。



## 15 Spring初始化bean的方法

在Spring框架中，Bean的初始化是Spring容器生命周期中的一个重要环节。Bean的初始化方法通常是通过Spring的生命周期回调接口来实现的。以下是几种常见的初始化Bean的方法：

1. **InitializingBean接口**： 实现`org.springframework.beans.factory.InitializingBean`接口，并重写`afterPropertiesSet()`方法。当Bean的所有属性都被设置之后，Spring会调用这个方法进行初始化。

   ```java
   public class MyBean implements InitializingBean {
   
       @Override
       public void afterPropertiesSet() throws Exception {
           // 初始化逻辑
       }
   }
   ```

2. **自定义init方法**： 在Bean的类定义中，可以定义一个名为`init`的方法，并且使用`@PostConstruct`注解标记。Spring会在Bean的属性设置之后调用此方法。

   ```java
   public class MyBean {
   
       @PostConstruct
       public void init() {
           // 初始化逻辑
       }
   }
   ```

3. **XML配置**： 在Spring的XML配置文件中，可以为Bean定义一个`init-method`属性，该属性的值应该是Bean类中定义的初始化方法的名称。

   ```
   <bean id="myBean" class="com.example.MyBean" init-method="init"/>
   ```

   对应的Java类定义：

   ```
   public class MyBean {
   
       public void init() {
           // 初始化逻辑
       }
   }
   ```

4. **使用@Bean注解**： 在Java配置类中，使用`@Bean`注解定义一个Bean，并通过实现`BeanPostProcessor`接口或使用`@Bean(initMethod = "init")`来指定初始化方法。

   ```
   @Configuration
   public class AppConfig {
   
       @Bean(initMethod = "init")
       public MyBean myBean() {
           return new MyBean();
       }
   }
   ```

   对应的Java类定义：

   ```
   public class MyBean {
   
       public void init() {
           // 初始化逻辑
       }
   }
   ```

5. **使用JSR-250的@PostConstruct注解**： `@PostConstruct`注解是JSR-250规范的一部分，它用于在依赖注入完成后，但在任何生命周期回调之前执行初始化方法。这通常是在Spring和Java EE环境中都可以使用的方法。

   ```java
   public class MyBean {
   
       @PostConstruct
       public void postConstruct() {
           // 初始化逻辑
       }
   }
   ```

以上就是在Spring框架中初始化Bean的几种常见方法。在大多数情况下，使用`@PostConstruct`注解或者实现`InitializingBean`接口是最常见和推荐的做法，因为它们提供了清晰且类型安全的初始化机制。

## 16 bean三级缓存存放内容和各自的作用

在Spring框架中，为了解决单例Bean之间的循环依赖问题，IoC容器使用了三级缓存机制。这三级缓存分别是：

1. **一级缓存（singletonObjects）**：
   - **存放内容**：这个缓存中存放的是已经完全初始化好的Bean实例。也就是说，当一个Bean的所有依赖都已经被注入，且Bean的初始化方法（如果有的话）已经被调用之后，这个Bean的实例就会被放入一级缓存中。
   - **作用**：当其他Bean需要引用这个已经完全初始化的Bean时，可以直接从一级缓存中获取。
2. **二级缓存（earlySingletonObjects）**：
   - **存放内容**：这个缓存中存放的是早期暴露的Bean引用，即那些还没有完成所有依赖注入，但已经可以被其他Bean引用的Bean实例。这些Bean实例通常是通过调用Bean的工厂方法（如`getObject()`方法）而创建的，但它们的依赖可能还没有完全注入。
   - **作用**：当一个Bean A正在创建过程中，它的依赖Bean B也被请求时，如果Bean B也依赖于Bean A，Spring会从二级缓存中获取Bean A的早期暴露引用，并将其注入到Bean B中。这样，Bean B就可以引用到Bean A，即使Bean A还没有完全初始化。
3. **三级缓存（singletonFactories）**：
   - **存放内容**：这个缓存中存放的是创建Bean实例的工厂对象（通常是`ObjectFactory`）。这些工厂对象被用来创建Bean实例，并在创建过程中放入二级缓存。
   - **作用**：当一个Bean正在创建过程中，它的工厂对象会被放入三级缓存中。如果其他Bean需要引用这个正在创建的Bean，Spring会从三级缓存中获取对应的工厂对象，并通过该工厂对象获取Bean的早期暴露引用，然后将其放入二级缓存。

这三级缓存共同协作，使得Spring IoC容器能够在单例模式下解决循环依赖问题。当一个Bean正在创建过程中，它的早期暴露引用可以被其他Bean引用，从而打破了循环依赖的僵局。一旦所有Bean都完成了依赖注入，它们就会从二级缓存移动到一级缓存中，供其他Bean使用。





## 17 Spring http请求处理流程

Spring HTTP请求处理流程主要包括以下几个步骤：

1. 客户端发送HTTP请求到Web服务器。
2. Web服务器接收到请求后，将其交给Web容器处理。
3. Web容器将请求交给DispatcherServlet（前端控制器）进行处理。
4. DispatcherServlet将请求分发给具体的Controller进行处理。在分发请求之前，需要借助于Spring提供的HandleMapping来定位到具体的Controller。HandleMapping负责完成客户请求和Controller之间的映射。
5. Controller接收到请求后，调用相应的业务逻辑进行处理。
6. 一旦Controller处理完用户请求，它会返回一个ModelAndView对象给DispatcherServlet。ModelAndView对象包含了视图和模型数据。
7. DispatcherServlet使用ViewResolver来解析ModelAndView对象，找到具体的视图进行渲染。
8. 视图渲染完成后，将结果返回给客户端。



## 18 Spring 依赖注入方式

Spring框架提供了多种依赖注入（Dependency Injection，DI）的方式，允许开发者灵活地将依赖关系注入到Bean中。以下是Spring中常见的依赖注入方式：

1. **构造函数注入（Constructor Injection）**：
   - 通过在Bean的构造函数中声明依赖参数，Spring容器在创建Bean实例时将相应的依赖注入到构造函数中。这种方式保证了Bean在实例化后就可以直接使用依赖对象。
2. **Setter方法注入（Setter Injection）**：
   - 通过在Bean类中添加setter方法，并使用`@Autowired`或`@Resource`注解来标记这些方法，Spring容器会在Bean实例化后调用这些setter方法，并将相应的依赖注入到Bean中。Setter注入可以实现选择性注入，并且每个setter单独注入某个对象，便于控制。
3. **字段注入（Field Injection）**：
   - 通过在Bean类中使用`@Autowired`或`@Resource`注解直接标记需要注入的字段，Spring容器会自动将这些字段注入到Bean中。这种方式简单直接，但存在一些问题，如对象的外部可见性和循环依赖等。
4. **注解注入（Annotation Injection）**：
   - Spring支持使用注解来简化依赖注入的配置。开发者可以在Bean类、方法或字段上使用`@Autowired`、`@Resource`等注解来声明依赖关系，Spring容器会根据这些注解自动进行依赖注入。
5. **XML配置注入**：
   - 在早期的Spring版本中，依赖注入主要通过XML配置文件来实现。开发者可以在XML配置文件中使用`<bean>`元素来定义Bean，并使用`<property>`子元素来注入依赖关系。虽然这种方式现在已经逐渐被注解方式所取代，但在一些老的项目中仍然会使用。

以上是Spring中常见的依赖注入方式。在实际开发中，开发者可以根据项目的需求和团队的约定选择适合的注入方式。通常情况下，推荐使用注解方式进行依赖注入，因为它更加简洁、易于理解和维护。



## 19 JDKProxy 和 Cglib分别怎么实现的

JDK Proxy和Cglib是两种不同的动态代理实现方式，它们在Spring AOP中都有应用。

### 19.1 JDK Proxy

JDK Proxy的实现主要依赖于Java的反射机制。JDK Proxy会创建一个实现了被**代理对象接口的匿名类**，这个匿名类在调用方法前会调用一个`InvocationHandler`接口的实现类。这个`InvocationHandler`实现类在调用目标方法时会使用反射机制，从而可以动态地织入切面逻辑。

### 19.2 Cglib

Cglib是一个强大的高性能的代码生成库，它可以在运行时扩展Java类与实现Java接口。Cglib的动态代理实现是通过继承被代理类并覆盖其中的方法来完成的。因此，Cglib可以代理没有实现接口的类。

需要注意的是，由于Cglib是通过继承来实现动态代理的，因此如果被代理类被标记为`final`，那么它是无法使用Cglib进行动态代理的。

总的来说，JDK Proxy和Cglib都是实现动态代理的工具，但它们在实现方式上有所不同。JDK Proxy基于反射机制，只能代理接口；而Cglib则通过继承被代理类并覆盖方法来实现动态代理，可以代理没有实现接口的类。在Spring AOP中，会根据被代理对象是否实现接口来选择使用哪种代理方式。



## 20 spring声明事务原理

Spring 声明式事务管理的核心原理是基于 **AOP（面向切面编程）** 和 **动态代理** 实现的。通过注解（如 `@Transactional`）或 XML 配置声明事务规则，Spring 在运行时自动为相关方法生成代理对象，拦截方法调用并管理事务的生命周期（如开启、提交、回滚）。

**步骤**

1. **声明事务的入口**：
   - 使用 `@Transactional` 注解标记需要事务管理的方法或类。
   - 或在 XML 中通过 `<tx:advice>` 配置事务规则。
2. **AOP 动态代理**：
   - Spring 通过动态代理（JDK 动态代理或 CGLIB）生成目标对象的代理对象。
   - 代理对象拦截被 `@Transactional` 注解标记的方法调用。
3. **事务拦截器（TransactionInterceptor）**：
   - 代理对象调用 `TransactionInterceptor`，它是事务逻辑的核心实现。
   - 拦截器根据事务属性（如隔离级别、传播行为）决定如何处理事务。
4. **事务管理器（PlatformTransactionManager）**：
   - `PlatformTransactionManager` 是事务管理的核心接口（如 `DataSourceTransactionManager`、`JpaTransactionManager`）。
   - 负责具体的事务操作：开启事务（`getTransaction`）、提交（`commit`）、回滚（`rollback`）。
5.  **事务的提交与回滚**：
   - 方法执行前：开启事务，绑定到当前线程（通过 `ThreadLocal`）。
   - 方法正常执行：提交事务。
   - 方法抛出异常：根据回滚规则（默认回滚 `RuntimeException` 和 `Error`）决定是否回滚。



## 21 Spring 框架中用到了哪些设计模式

Spring框架中广泛应用了多种设计模式，这些设计模式不仅增强了框架的灵活性和可扩展性，也简化了应用程序的开发和维护。以下是一些在Spring框架中常见的设计模式：

1. **单例模式（Singleton Pattern）**：Spring容器中的Bean默认都是单例的，这意味着在整个应用程序中，一个Bean只会有一个实例。这通过配置文件或注解实现，确保了Bean的唯一性。

2. **工厂模式（Factory Pattern）**：Spring通过`BeanFactory`接口实现了工厂方法模式。`BeanFactory`负责创建并管理Bean的实例，Spring容器在调用`getBean()`方法时，会自动调用`FactoryBean`的`getObject()`方法返回Bean实例。这种解耦的方式使得对象的创建和使用更加灵活。

3. **观察者模式（Observer Pattern）**：Spring的事件机制就是观察者模式的应用。当某个事件发生时，注册为该事件监听器的对象会收到通知并执行相应的操作。这种模式实现了对象之间的解耦和通信。

4. **代理模式（Proxy Pattern）**：Spring的AOP（面向切面编程）功能是基于代理模式实现的。通过为目标对象创建代理对象，可以在不修改目标对象代码的情况下，增强目标对象的功能。

5. **模板方法模式（Template Method Pattern）**：Spring的JdbcTemplate是一个典型的模板方法模式的应用。它提供了一套模板方法用于处理数据库操作，而具体的操作细节则由子类实现。
6. **策略模式（Strategy Pattern）**：Spring的事务管理机制使用了策略模式。通过定义不同的事务管理策略，应用可以灵活地选择不同的事务处理方式。
7. **装饰者模式（Decorator Pattern）**：Spring的Bean后置处理器（BeanPostProcessor）体现了装饰者模式的思想。BeanPostProcessor可以在Bean的创建过程中添加额外的逻辑，从而增强Bean的功能。



## 22 Spring 管理事务的方式

Spring管理事务的方式主要有以下几种：

1. **编程式事务管理**：这种方式需要用户通过编程的方式手动管理事务，例如使用`TransactionTemplate`来执行事务。虽然这种方式提供了更大的灵活性，但是需要用户手动编写大量的事务管理代码，相对繁琐。
2. **声明式事务管理**：这是Spring推荐使用的事务管理方式，通过配置的方式来实现事务管理，用户只需定义事务边界和事务属性，不需要显式编写事务管理的代码。声明式事务管理可以进一步细分为基于XML的声明式事务管理和基于注解的声明式事务管理。
   - **基于XML的声明式事务管理**：通过在Spring的配置文件中（如XML文件）定义事务管理器、事务通知以及将事务通知应用到具体的方法上，实现事务的管理。这种方式虽然配置相对繁琐，但是对于复杂的事务管理需求，可以提供更精细的控制。
   - **基于注解的声明式事务管理**：通过在方法或类上使用注解（如`@Transactional`）来声明事务的边界和属性，这种方式更为简洁和直观，是Spring中推荐的事务管理方式。
3. **使用`TransactionProxyFactoryBean`**：这种方式通过配置`TransactionProxyFactoryBean`来创建代理对象，代理对象会自动管理事务。不过，这种方式在Spring的后续版本中逐渐被注解方式所取代。

在选择事务管理方式时，需要根据项目的实际需求、团队的编程习惯以及项目的复杂度等因素进行综合考虑。一般来说，对于简单的项目或者团队习惯使用注解的方式，可以选择基于注解的声明式事务管理；对于复杂的事务管理需求或者需要更精细控制的场景，可以考虑使用基于XML的声明式事务管理或编程式事务管理。

## 23 Spring事务传播机制



## 24 Spring 事务中的隔离级别有哪几种

在Spring事务中，隔离级别是用来定义事务之间的隔离程度，它决定了事务在并发执行时彼此之间的可见性。Spring支持五种事务隔离级别，这些级别与数据库管理系统（DBMS）中的标准隔离级别相对应。以下是Spring事务中的五种隔离级别：

1. **READ_UNCOMMITTED（读取未提交数据）**：
   - 这是最低的隔离级别。
   - 允许一个事务读取另一个事务尚未提交的数据。
   - 这种隔离级别可能导致脏读（Dirty Read）、不可重复读和幻读问题。
2. **READ_COMMITTED（读取已提交数据）**：
   - 保证一个事务只能读取到已经提交的数据。
   - 避免脏读问题。
   - 但是在并发情况下，可能会导致不可重复读和幻读问题。
3. **REPEATABLE_READ（可重复读取）**：
   - 保证一个事务在多次读取同一数据时，能够得到一致的结果。
   - 在该隔离级别下，其他事务不能修改当前事务已经读取的数据，避免了不可重复读问题。
   - 但是仍然可能出现幻读问题。
4. **SERIALIZABLE（可串行化）**：
   - 这是最高的隔离级别。
   - 确保并发事务之间不会发生任何并发问题。
   - 它通过强制事务串行执行来避免脏读、不可重复读和幻读问题。
   - 由于需要串行化执行事务，性能相对较低。
5. **ISOLATION_DEFAULT**：
   - 使用底层数据库设置的隔离级别。
   - 如果没有指定隔离级别，Spring将使用此默认隔离级别。
