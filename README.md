本文档是对spring官方文档的解读，原文档参见

[spring官网]: https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-child-bean-definitions	"Spring 官网"

，本人只是翻译和整理，由于水平有限，部分解读可能不正确，欢迎提出更好的意见和建议！网页版移步

[临时的网站]: http://180.76.184.216/README.html	"临时搭建的网站"



# 1 Spring综述

## 1.1 jdk环境依赖

> 从Spring Framework 5.1开始，Spring需要JDK 8+ (Java SE 8+)，并提供对JDK 11 LTS的开箱即用支持。建议将Java SE 8 update 60作为Java 8的最低补丁版本，但通常建议使用最新的补丁版本。

## 1.2 spring介绍

> The Spring Framework is divided into modules. Applications can choose which modules they need. At the heart are the modules of the core container, including a configuration model and a dependency injection mechanism. Beyond that, the Spring Framework provides foundational support for different application architectures, including messaging, transactional data and persistence, and web.

> spring分为许多模块，应用可以选择他们需要的模块。它的核心模块是core container（也就是核心容器），包括配置模块和DI依赖注入。除此之外，Spring框架还为不同的应用程序体系结构提供基础支持，包括消息传递、事务数据和持久性以及web。

## 1.3 Spring历史

- Spring框架的出现是为了解决早期J2EE规范的复杂性，他没有包括所有J2EE平台的规范，而是精心集成了一些特别的规范：
  - Servlet API ([JSR 340](https://jcp.org/en/jsr/detail?id=340))
  - WebSocket API ([JSR 356](https://www.jcp.org/en/jsr/detail?id=356))
  - Concurrency Utilities ([JSR 236](https://www.jcp.org/en/jsr/detail?id=236))
  - JSON Binding API ([JSR 367](https://jcp.org/en/jsr/detail?id=367))
  - Bean Validation ([JSR 303](https://jcp.org/en/jsr/detail?id=303))
  - JPA ([JSR 338](https://jcp.org/en/jsr/detail?id=338))
  - JMS ([JSR 914](https://jcp.org/en/jsr/detail?id=914))
  - as well as JTA/JCA setups for transaction coordination, if necessary.
  - the Dependency Injection ([JSR 330](https://www.jcp.org/en/jsr/detail?id=330)) and Common Annotations ([JSR 250](https://jcp.org/en/jsr/detail?id=250)) specifications（spring同样支持依赖注入和通用注解规范）
- Spring5.0要求的最低Java版本为Java7

## 1.4 设计理念

在学习框架的时候，你不仅仅需要知道他是怎么使用和怎么做的，更需要了解他遵循的原则：

- Provide choice at every level. 提供每个层次的选择，Spring允许您尽可能晚地推迟设计决策。例如你可以通过配置切换持久性提供程序（可能指的是类似数据源之类的）和基础设施以及第三方api集成。
- Accommodate diverse perspectives.Spring embraces flexibility and is not opinionated about how things should be done. It supports a wide range of application needs with different perspectives.意思是spring是灵活多变的，支持广泛的应用程序需求。
- Maintain strong backward compatibility. 具有强大的向后兼容性。Spring的发展已经被小心地管理，在不同的版本之间很少有中断的变化。Spring支持精心选择的JDK版本和第三方库，以方便维护依赖于Spring的应用程序和库。
- Care about API design.关心api的设计。Spring团队投入了大量的思想和时间来开发直观的api，这些api可以支持多个版本和许多年。
- Set high standards for code quality. 为代码质量设置高标准。它是少数几个可以声明干净的代码结构且包之间没有循环依赖关系的项目之一。

# 2. Core 核心

> IoC Container, Events, Resources, i18n, Validation, Data Binding, Type Conversion, SpEL, AOP.
>
> IoC（控制反转）容器，事件，资源，i18n，验证，数据绑定，类型转换，表达式，面向切面编程

## 2.1 IoC(Inversion of Control) Container

> It is a process whereby objects define their dependencies (that is, the other objects they work with) only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes or a mechanism such as the **Service Locator pattern**.

> 项目的依赖（对象）仅通过构造器参数，工厂方法参数或者通过构造器或工厂方法返回的实例对象的set方法来定义他们的依赖（属性）。容器在bean对象创建时，将依赖注入到bean对象中。这个过程通过使用类的直接构造或服务定位模式机制，从根本上反转了bean本身控制实例化或依赖的位置

- org.springframework.beans和 org.springframework.context是SpringIoC容器的基础
- BeanFactory接口提供了能够管理任何类型对象的高级配置机制。
- ApplicationContext时BeanFactory的一个子接口，他包含了：
  - 更容易与AOP特性集成   Easier integration with Spring’s AOP features
  - 消息资源处理   Message resource handling 
  - 事件发布   Event publication
  - 特定于应用程序的Context（例如WebApplicationContext应用在web应用中）     Application-layer specific contexts such as the `WebApplicationContext` for use in web applications.

> In short, the `BeanFactory` provides the configuration framework and basic functionality, and the `ApplicationContext` adds more enterprise-specific functionality. The `ApplicationContext` is a complete superset of the `BeanFactory` and is used exclusively in this chapter in descriptions of Spring’s IoC container.

所以说，BeanFactory提供了配置框架和基础功能，ApplicationContext增加了更多特定功能。ApplicationContext时BeanFactory的一个完整超集，它在本章描述Spring的IoC容器时专用。

> In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container. Otherwise, a bean is simply one of many objects in your application. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.

在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是由Spring IoC容器实例化、组装和管理的对象。另外，bean只是应用程序中的众多对象之一。bean及其之间的依赖关系反映在容器使用的配置元数据中。

### 2.1.1 补充

- Service Locator Pattern：服务定位模式，一种设计模式。当我们希望使用JNDI查找来定位各种服务时，选择此种设计模式。这种设计模式使用了缓存技术。
  - 第一次需要服务时，服务定位器在JNDI中查找并缓存服务对象。
  - 再次查找相同的服务时，将在缓存中完成，提高了程序的性能
- JNDI

## 2.2 Container OverView 容器综述

> The `org.springframework.context.ApplicationContext` interface represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the beans. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. It lets you express the objects that compose your application and the rich interdependencies between those objects.

ApplicationContext这个接口就代表IoC容器，它负责实例化、配置和组装bean。容器通过读取配置文件的元数据来获取要实例化、配置和组装哪些对象的指令。配置文件支持xml,Java注解和纯Java代码。它允许您表达组成应用程序的对象以及这些对象之间丰富的相互依赖关系。

**这个描述就是说对象的实例化，分配和管理由容器来负责，你只需要将这些信息用xml配置文件或注解或Java代码配置即可**

> Several implementations of the `ApplicationContext` interface are supplied with Spring. In stand-alone applications, it is common to create an instance of [`ClassPathXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.5.BUILD-SNAPSHOT/javadoc-api/org/springframework/context/support/ClassPathXmlApplicationContext.html) or [`FileSystemXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.5.BUILD-SNAPSHOT/javadoc-api/org/springframework/context/support/FileSystemXmlApplicationContext.html). While XML has been the traditional format for defining configuration metadata, you can instruct the container to use Java annotations or code as the metadata format by providing a small amount of XML configuration to declaratively enable support for these additional metadata formats

**ApplicationContext的几个实现由Spring提供，通常你需要创建 ClassPathXmlApplicationContext或FileSystemXmlApplicationContext的实例。你也可以通过配置使用Java注解结合少量的XML配置来指示容器使用Java注释或代码作为元数据格式。**

> In most application scenarios, explicit user code is not required to instantiate one or more instances of a Spring IoC container. For example, in a web application scenario, a simple eight (or so) lines of boilerplate web descriptor XML in the `web.xml` file of the application typically suffices (see [Convenient ApplicationContext Instantiation for Web Applications](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#context-create)). If you use the [Spring Tools for Eclipse](https://spring.io/tools) (an Eclipse-powered development environment), you can easily create this boilerplate configuration with a few mouse clicks or keystrokes.

**就是说，大多数情况下你不需要显示的代码来实例化一个容器，只需要少量的配置就足够了**

>  The following diagram shows a high-level view of how Spring works. Your application classes are combined with configuration metadata so that, after the `ApplicationContext` is created and initialized, you have a fully configured and executable system or application.

![container magic](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/images/container-magic.png)

### 2.2.1 Configuration Metadata

- 基于注解的配置：spring2.5+
- 基于Java的配置：spring3.0+

> Spring configuration consists of at least one and typically more than one bean definition that the container must manage. XML-based configuration metadata configures these beans as `` elements inside a top-level `` element. Java configuration typically uses `@Bean`-annotated methods within a `@Configuration` class.

你可以在xml配置文件中使用<bean>标签或在带有@Configuration注解的java类中使用@Bean来定义Bean

>Typically, one does not configure fine-grained domain objects in the container, because it is usually the responsibility of DAOs and business logic to create and load domain objects.
>
>**通常，不会在容器中配置细粒度域对象，因为通常由DAOs和业务逻辑负责创建和加载域对象，这时可以使用Spring与AspectJ的集成来配置在IoC容器控制之外创建的对象。**

**基于xml配置元数据的基本结构：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

- The `id` attribute is a string that identifies the individual bean definition. 用来标识单个bean
- The `class` attribute defines the type of the bean and uses the fully qualified classname. 这个bean对应类的全限定名

### 2.2.2 实例化容器

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

### 2.2.3 组合xml配置元数据 Composing XML-based Configuration Metadata

>  It can be useful to have bean definitions span multiple XML files. Often, each individual XML configuration file represents a logical layer or module in your architecture.
>
> 让bean定义跨越多个XML文件可能很有用。通常，每个单独的XML配置文件表示体系结构中的逻辑层或模块。

```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

>  It is possible, but not recommended, to reference files in parent directories using a relative "../" path. Doing so creates a dependency on a file that is outside the current application. In particular, this reference is not recommended for `classpath:` URLs (for example, `classpath:../services.xml`), where the runtime resolution process chooses the “nearest” classpath root and then looks into its parent directory. Classpath configuration changes may lead to the choice of a different, incorrect directory.You can always use fully qualified resource locations instead of relative paths: for example, `file:C:/config/services.xml` or `classpath:/config/services.xml`. However, be aware that you are coupling your application’s configuration to specific absolute locations. It is generally preferable to keep an indirection for such absolute locations — for example, through "${…}" placeholders that are resolved against JVM system properties at runtime.

此处提示最好不要使用相对路径，你可以使用绝对路径(不建议)，和classpath，但是不要将配置文件耦合在系统的绝对路径，因为换了操作系统（在服务器部署）时很可能出问题。

### 2.2.4 Using the Container 容器的使用

> The `ApplicationContext` is the interface for an advanced factory capable of maintaining a registry of different beans and their dependencies. By using the method `T getBean(String name, Class requiredType)`, you can retrieve instances of your beans.
>
> The `ApplicationContext` lets you read bean definitions and access them, as the following example shows:
>
> 通过ApplicationContext接口的getBean方法获取Bean对象的实例。

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

## 2.3 Bean Overview Bean对象概述

> A Spring IoC container manages one or more beans. These beans are created with the configuration metadata that you supply to the container (for example, in the form of XML `` definitions).
>
> 一个springIoC容器管理着一个或多个bean对象，这些bean对象由你提供给容器的配置元数据创建生成。
>
> Within the container itself, these bean definitions are represented as **`BeanDefinition`** objects, which contain (among other information) the following metadata:
>
> 在容器内部，这些bean定义表示为**BeanDefinition**对象，包含着以下元数据：

- A package-qualified class name: typically, the actual implementation class of the bean being defined.（全限定类名）
- Bean behavioral configuration elements, which state how the bean should behave in the container (scope, lifecycle callbacks, and so forth).（Bean的行为配置元素，作用域，生命周期回调等）
- References to other beans that are needed for the bean to do its work. These references are also called collaborators or dependencies.（对该bean执行其工作所需的其他bean的引用。）
- Other configuration settings to set in the newly created object — for example, the size limit of the pool or the number of connections to use in a bean that manages a connection pool.（在创建bean对象时的其他配置，如连接池的最大数量）

|         Property         |                        Explained in…                         |      |
| :----------------------: | :----------------------------------------------------------: | ---- |
|          Class           | [Instantiating Beans](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class) |      |
|           Name           | [Naming Beans](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-beanname) |      |
|          Scope           | [Bean Scopes](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes) |      |
|  Constructor arguments   | [Dependency Injection](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-collaborators) |      |
|        Properties        | [Dependency Injection](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-collaborators) |      |
|     Autowiring mode      | [Autowiring Collaborators](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-autowire) |      |
| Lazy initialization mode | [Lazy-initialized Beans](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lazy-init) |      |
|  Initialization method   | [Initialization Callbacks](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean) |      |
|    Destruction method    | [Destruction Callbacks](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lifecycle-disposablebean) |      |

> In addition to bean definitions that contain information on how to create a specific bean, the `ApplicationContext` implementations also permit the registration of existing objects that are created outside the container (by users). This is done by accessing the ApplicationContext’s BeanFactory through the `getBeanFactory()` method, which returns the BeanFactory `DefaultListableBeanFactory` implementation. `DefaultListableBeanFactory` supports this registration through the `registerSingleton(..)` and `registerBeanDefinition(..)` methods. However, typical applications work solely with beans defined through regular bean definition metadata.
>
> 这段话说我们可以通过ApplicationContext的getBeanFactory方法在容器之外创建对象，但是这种方法不被建议。**//此处我找不到getBeanFactory这个方法。**

**Tip**

> Bean metadata and manually supplied singleton instances need to be registered as early as possible, in order for the container to properly reason about them during autowiring and other introspection steps. While overriding existing metadata and existing singleton instances is supported to some degree, the registration of new beans at runtime (concurrently with live access to the factory) is not officially supported and may lead to concurrent access exceptions, inconsistent state in the bean container, or both.
>
> bean元数据和手动提供的单例实例需要尽早的注册，为了容器在自动装配和其他内省步骤中正确地推理它们。虽然在一定程度上支持覆盖现有元数据和现有单例实例，但是在运行时注册新bean(与对工厂的实时访问同时进行)不受官方支持，可能会导致并发访问异常，使bean容器中的不一致状态，或者两者都是。

### 2.3.1 Naming Beans	命名Bean

> Every bean has one or more identifiers. These identifiers must be unique within the container that hosts the bean. A bean usually has only one identifier. However, if it requires more than one, the extra ones can be considered aliases.
>
> 每个bean都有一个或多个标识符。这些标识符在承载bean的容器中必须是惟一的。一个bean通常只有一个标识符。但是，如果需要多个别名，则可以将额外的别名视为别名。

> In XML-based configuration metadata, you use the `id` attribute, the `name` attribute, or both to specify the bean identifiers. The `id` attribute lets you specify exactly one id. Conventionally, these names are alphanumeric ('myBean', 'someService', etc.), but they can contain special characters as well. If you want to introduce other aliases for the bean, you can also specify them in the `name` attribute, separated by a comma (`,`), semicolon (`;`), or white space.
>
> 用id配置标识，这个需要使唯一的，同时你还可以使用name来配置bean的别名，并且可以配置多个，你可以用逗号分号和空格来分开多个别名。

```java
<bean id="user" class="com.leishida.spring.User" name="u us,use;user2"/>
```

> You are not required to supply a `name` or an `id` for a bean. If you do not supply a `name` or `id` explicitly, the container generates a unique name for that bean. However, if you want to refer to that bean by name, through the use of the `ref` element or a Service Locator style lookup, you must provide a name. Motivations for not supplying a name are related to using [inner beans](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-inner-beans) and [autowiring collaborators](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-autowire).
>
> 你也可以不为bean配置name和id属性，这样容器会为它自动分配唯一的id，但是你将无法引用它

**Bean命名约定**

> The convention is to use the standard Java convention for instance field names when naming beans. That is, bean names start with a lowercase letter and are camel-cased from there. Examples of such names include `accountManager`, `accountService`, `userDao`, `loginController`, and so forth.
>
> Naming beans consistently makes your configuration easier to read and understand. Also, if you use Spring AOP, it helps a lot when applying advice to a set of beans related by name.
>
> 遵循Java的命名规范，首字母小写，然后采用驼峰命名。

通过在类路径中扫描组件，Spring按照前面描述的规则为未命名的组件生成bean名称:本质上，使用简单的类名并将其初始字符转换为小写。但是，在(不寻常的)特殊情况下，如果有多个字符，并且第一个和第二个字符都是大写的，则保留原来的大小写。这些规则与java.beans.自省器.decapitalize (Spring在这里使用)定义的规则相同。

**在bean定义外定义别名**

```xml
<alias name="fromName" alias="toName"/>
```

### 2.3.2 Instantiating Beans 实例化Beans

> If you use XML-based configuration metadata, you specify the type (or class) of object that is to be instantiated in the `class` attribute of the `` element. This `class` attribute (which, internally, is a `Class` property on a `BeanDefinition` instance) is usually mandatory. (For exceptions, see [Instantiation by Using an Instance Factory Method](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class-instance-factory-method) and [Bean Definition Inheritance](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-child-bean-definitions).) You can use the `Class` property in one of two ways:

你通常必须指定class属性（因为要创建对象肯定要知道它的全类名）

你可以通过以下两种方式之一使用Class属性:

- Typically, to specify the bean class to be constructed in the case where the container itself directly creates the bean by calling its constructor reflectively, somewhat equivalent to Java code with the `new` operator.直接通过反射创建
- To specify the actual class containing the `static` factory method that is invoked to create the object, in the less common case where the container invokes a `static` factory method on a class to create the bean. The object type returned from the invocation of the `static` factory method may be the same class or another class entirely.通过静态工厂创建

> Inner class names
>
> If you want to configure a bean definition for a `static` nested class, you have to use the binary name of the nested class.
>
> For example, if you have a class called `SomeThing` in the `com.example` package, and this `SomeThing` class has a `static` nested class called `OtherThing`, the value of the `class` attribute on a bean definition would be `com.example.SomeThing$OtherThing`.
>
> Notice the use of the `$` character in the name to separate the nested class name from the outer class name.

**如果你想要配置一个静态内部类的bean，你需要这么写: packageName.OuterClassName$InnerClassName**

#### Instantiation with a Constructor 使用构造器实例化

>  When you create a bean by the constructor approach, all normal classes are usable by and compatible with Spring. That is, the class being developed does not need to implement any specific interfaces or to be coded in a specific fashion. Simply specifying the bean class should suffice. However, depending on what type of IoC you use for that specific bean, you may need a default (empty) constructor.

**当您通过构造函数方法创建一个bean时，所有的普通类都可以被Spring使用并与Spring兼容。也就是说，正在开发的类不需要实现任何特定的接口，也不需要以特定的方式进行编码。只需指定bean类就足够了。但是，根据您为特定bean使用的IoC类型，您可能需要一个默认(空)构造函数。**

> The Spring IoC container can manage virtually any class you want it to manage. It is not limited to managing true JavaBeans. Most Spring users prefer actual JavaBeans with only a default (no-argument) constructor and appropriate setters and getters modeled after the properties in the container. You can also have more exotic non-bean-style classes in your container. If, for example, you need to use a legacy connection pool that absolutely does not adhere to the JavaBean specification, Spring can manage it as well.

**Spring IoC容器几乎可以管理您希望它管理的任何类。它不仅限于管理真正的javabean。大多数Spring用户更喜欢实际的javabean，它只有一个默认的(无参数的)构造函数，以及根据容器中的属性建模的适当的setter和getter方法。您还可以在容器中包含更多非bean风格的类。例如，如果您需要使用完全不符合JavaBean规范的遗留连接池，Spring也可以管理它。**

With XML-based configuration metadata you can specify your bean class as follows:

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>
<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```

有关构造器注入的细节，请移步注入依赖项[Injecting Dependencies]。

#### Instantiation with a Static Factory Method 使用静态工厂方法实例化

> When defining a bean that you create with a static factory method, use the `class` attribute to specify the class that contains the `static` factory method and an attribute named `factory-method` to specify the name of the factory method itself. You should be able to call this method (with optional arguments, as described later) and return a live object, which subsequently is treated as if it had been created through a constructor. One use for such a bean definition is to call `static` factories in legacy code.
>
> 在定义使用静态工厂方法创建的bean时，使用class属性指定包含静态工厂方法的类，使用factory-method属性指定工厂方法本身的名称。您应该能够调用这个方法(带有可选参数，如后面所述)并返回一个活动对象，该对象随后被视为是通过构造函数创建的。这种bean定义的一个用途是在遗留代码中调用静态工厂。
>
> The following bean definition specifies that the bean be created by calling a factory method. The definition does not specify the type (class) of the returned object, only the class containing the factory method. In this example, the `createInstance()` method must be a static method. The following example shows how to specify a factory method:
>
> 下面的bean定义指定通过调用工厂方法来创建bean。定义不指定返回对象的类型(类)，只指定包含工厂方法的类。在本例中，createInstance()方法必须是一个静态方法。下面的例子展示了如何指定一个工厂方法:

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}

public class UserFactory {

    public static User getUser(){
        return new User("工厂", 19);
    }
}
```

```xml
<bean id="user" class="com.leishida.spring.UserFactory" factory-method="getUser"/>
```

```java
public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object user = context.getBean("user");
        System.out.println(user);   //User(name=工厂, age=19)
    }
```

#### Instantiation by Using an Instance Factory Method 使用实例工厂方法实例化

> Similar to instantiation through a [static factory method](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class-static-factory-method), instantiation with an instance factory method invokes a non-static method of an existing bean from the container to create a new bean. To use this mechanism, leave the `class` attribute empty and, in the `factory-bean` attribute, specify the name of a bean in the current (or parent or ancestor) container that contains the instance method that is to be invoked to create the object. Set the name of the factory method itself with the `factory-method` attribute. The following example shows how to configure such a bean:

与通过静态工厂方法实例化类似，使用实例工厂方法实例化将从容器中调用现有bean的非静态方法来创建新bean。要使用这种机制，将class属性保留为空，并在factory-bean属性中指定当前(或父或父)容器中bean的名称，该容器包含要调用来创建对象的实例方法。使用factory-method属性设置工厂方法本身的名称。下面的例子展示了如何配置这样一个bean:

```java
public class InstanceUserFactory {
    private static User user = new User();

    public User createClientServiceInstance() {
        return user;
    }
}
```

```xml
<bean id="instanceUserFactory" class="com.leishida.spring.InstanceUserFactory"/>
<bean id="user1" factory-bean="instanceUserFactory" factory-method="createClientServiceInstance"/>
```

```java
@org.junit.Test
    public void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object user = context.getBean("user1");
        System.out.println(user);   //User(name=null, age=0)
    }
```

**一个工厂类也可以有多个工厂方法**

**TIP**

> In Spring documentation, “factory bean” refers to a bean that is configured in the Spring container and that creates objects through an [instance](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class-instance-factory-method) or [static](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-class-static-factory-method) factory method. By contrast, `FactoryBean` (notice the capitalization) refers to a Spring-specific [`FactoryBean` ](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-extension-factorybean).
>
> 意思使在spring文档里，factoryBean是你在配置里自己配置的工厂bean对象，而FactoryBean则值得是Spring的FactoryBean

## 2.4 Dependencies 依赖项

> A typical enterprise application does not consist of a single object (or bean in the Spring parlance). Even the simplest application has a few objects that work together to present what the end-user sees as a coherent application. This next section explains how you go from defining a number of bean definitions that stand alone to a fully realized application where objects collaborate to achieve a goal.
>
> 典型的企业应用程序不包含单个对象(或Spring中的bean)。即使是最简单的应用程序也有几个对象一起工作，以呈现最终用户所看到的一致的应用程序。下一节将解释如何从定义许多独立的bean定义过渡到一个完全实现的应用程序，在这个应用程序中，对象通过协作来实现一个目标。

### 2.4.1 Dependency Injection 依赖注入

> Dependency injection (DI) is a process whereby objects define their dependencies (that is, the other objects with which they work) only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the instantiation or location of its dependencies on its own by using direct construction of classes or the Service Locator pattern.
>
> 依赖项注入(DI)是一个过程，在这个过程中，对象仅通过构造函数参数、工厂方法的参数或从工厂方法构造或返回后在对象实例上设置的属性来定义它们的依赖项(即它们与之一起工作的其他对象)。然后容器在创建bean时注入这些依赖项。这个过程基本上是bean本身的逆过程(因此称为控制反转过程)，**由对象自己控制实例化或者定位他的依赖，改变为通过直接使用类的构造方法或者使用服务定位模式。**

> Code is cleaner with the DI principle, and decoupling is more effective when objects are provided with their dependencies. The object does not look up its dependencies and does not know the location or class of the dependencies. As a result, your classes become easier to test, particularly when the dependencies are on interfaces or abstract base classes, which allow for stub or mock implementations to be used in unit tests.
>
> 使用DI原则，代码更简洁，并且当对象提供其依赖项时，解耦更有效。对象不查找其依赖项，也不知道依赖项的位置或类。结果，您的类变得更容易测试，特别是当依赖关系在接口或抽象基类上时，这允许在单元测试中使用存根或模拟实现。

> DI exists in two major variants: [Constructor-based dependency injection](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-constructor-injection) and [Setter-based dependency injection](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-setter-injection).
>
> 依赖注入存在与两个主要部分：基于构造器依赖注入和基于Set方法的依赖注入

#### Constructor-based Dependency Injection 基于构造器的依赖注入

> Constructor-based DI is accomplished by the container invoking a constructor with a number of arguments, each representing a dependency. Calling a `static` factory method with specific arguments to construct the bean is nearly equivalent, and this discussion treats arguments to a constructor and to a `static` factory method similarly. The following example shows a class that can only be dependency-injected with constructor injection:
>
> 基于构造函数的DI是通过容器调用带有许多参数的构造函数来完成的，每个参数代表一个依赖项。调用带有特定参数的静态工厂方法来构造bean几乎是等价的，本讨论将参数分别用于构造函数和静态工厂方法。下面的例子展示了一个只能依赖注入构造函数注入的类:

**Bean类：**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private int age;
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Score {
    private int English;
    private int Math;
}
```

**XML配置**

```xml
<bean id="user" class="com.leishida.spring.User">
        <constructor-arg name="name" value="构造器注入"/>
        <constructor-arg name="age" value="18"/>
        <!-- 当对象内部的属性是引用类型时，通过ref引用上面注册的score Bean -->
        <constructor-arg name="score" ref="score"/>
</bean>
```

**测试**

```java
@org.junit.Test
    public void test3(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object user = context.getBean("user");
        System.out.println(user);   //User(name=构造器注入, age=18, score=Score(English=0, Math=0))
    }
```

**通过构造器注入时可以根据参数索引，类型，名称来注入**

**通过索引注入**

```xml
<bean id="user1" class="com.leishida.spring.User">
        <constructor-arg index="0" value="索引"/>
        <constructor-arg index="1" value="18"/>
        <constructor-arg index="2" ref="score"/>
</bean>
```

**通过类型注入**

```xml
    <bean id="user2" class="com.leishida.spring.User">
        <constructor-arg type="java.lang.String" value="类型"/>
        <constructor-arg type="int" value="18"/>
        <constructor-arg type="com.leishida.spring.Score" ref="score"/>
    </bean>
```

**通过名称匹配上面例子已经写过，此处不再赘述**

#### Setter-based Dependency Injection 基于Set方法的注入

> Setter-based DI is accomplished by the container calling setter methods on your beans after invoking a no-argument constructor or a no-argument `static` factory method to instantiate your bean.
>
> The following example shows a class that can only be dependency-injected by using pure setter injection. This class is conventional Java. It is a POJO that has no dependencies on container specific interfaces, base classes, or annotations.
>
> 基于setter的DI是在调用无参数构造函数或无参数静态工厂方法来实例化bean之后，通过容器调用bean上的setter方法来完成的。
>
> 下面的示例显示了一个只能通过使用纯setter注入来依赖注入的类。这个类是传统的Java。它是一个不依赖于容器特定接口、基类或注释的POJO。

**使用此种方式，一定要保证类对应的属性上有相应的get方法，否则会注入失败**

> The `ApplicationContext` supports constructor-based and setter-based DI for the beans it manages. It also supports setter-based DI after some dependencies have already been injected through the constructor approach. You configure the dependencies in the form of a `BeanDefinition`, which you use in conjunction with `PropertyEditor` instances to convert properties from one format to another. However, most Spring users do not work with these classes directly (that is, programmatically) but rather with XML `bean` definitions, annotated components (that is, classes annotated with `@Component`, `@Controller`, and so forth), or `@Bean` methods in Java-based `@Configuration` classes. These sources are then converted internally into instances of `BeanDefinition` and used to load an entire Spring IoC container instance.
>
> ApplicationContext为它管理的bean支持基于构造函数和基于setter的DI。在通过构造函数方法注入了一些依赖项之后，它还支持基于setter的DI。您可以将依赖项配置为BeanDefinition的形式，并将其与PropertyEditor实例一起使用，以便将属性从一种格式转换为另一种格式。但是，大多数Spring用户并不直接使用这些类(也就是说，以编程的方式)，而是使用XML bean定义、带注释的组件(也就是说，使用@Component、@Controller等注释的类)

> ​												**Constructor-based or setter-based DI?**
>
> Since you can mix constructor-based and setter-based DI, it is a good rule of thumb to use constructors for mandatory dependencies and setter methods or configuration methods for optional dependencies. Note that use of the [@Required](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-required-annotation) annotation on a setter method can be used to make the property be a required dependency; however, constructor injection with programmatic validation of arguments is preferable.
>
> The Spring team generally advocates constructor injection, as it lets you implement application components as immutable objects and ensures that required dependencies are not `null`. Furthermore, constructor-injected components are always returned to the client (calling) code in a fully initialized state. As a side note, a large number of constructor arguments is a bad code smell, implying that the class likely has too many responsibilities and should be refactored to better address proper separation of concerns.
>
> Setter injection should primarily only be used for optional dependencies that can be assigned reasonable default values within the class. Otherwise, not-null checks must be performed everywhere the code uses the dependency. One benefit of setter injection is that setter methods make objects of that class amenable to reconfiguration or re-injection later. Management through [JMX MBeans](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/integration.html#jmx) is therefore a compelling use case for setter injection.
>
> Use the DI style that makes the most sense for a particular class. Sometimes, when dealing with third-party classes for which you do not have the source, the choice is made for you. For example, if a third-party class does not expose any setter methods, then constructor injection may be the only available form of DI.

**那么究竟是选择基于构造器的注入还是选择基于set方式的注入呢？这里官方给出了我们建议：**

- 如果你的set注入参数是必须的，那么你可以在set方法上加一个@Required注解来保证这个参数一定被注入
- 最好使用带有参数编程式验证的构造函数注入，这样可以保证你需要的参数一定被注入
- 大量的构造函数参数是一种不好的代码味道，这意味着类可能有太多的责任，应该对其进行重构，以更好地解决问题的适当分离。
- Setter注入应该主要用于可选的依赖项，这些依赖项可以在类中分配合理的默认值。
- 使用对特定类最有意义的DI样式。有时，在处理您没有源代码的第三方类时，需要为您做出选择。例如，如果第三方类没有公开任何setter方法，那么构造函数注入可能是惟一可用的DI形式。

**使用set注入**

```xml
    <bean id="user3" class="com.leishida.spring.User">
        <property name="name" value="set注入"/>
    </bean>
```

```java
@org.junit.Test
    public void test4(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object user = context.getBean("user3");
        System.out.println(user);       //User(name=set注入, age=0, score=null)
    }
```

#### Dependency Resolution Process 依赖性解析过程

The container performs bean dependency resolution as follows:

- The `ApplicationContext` is created and initialized with configuration metadata that describes all the beans. Configuration metadata can be specified by XML, Java code, or annotations.
- For each bean, its dependencies are expressed in the form of properties, constructor arguments, or arguments to the static-factory method (if you use that instead of a normal constructor). These dependencies are provided to the bean, when the bean is actually created.
- Each property or constructor argument is an actual definition of the value to set, or a reference to another bean in the container.
- Each property or constructor argument that is a value is converted from its specified format to the actual type of that property or constructor argument. By default, Spring can convert a value supplied in string format to all built-in types, such as `int`, `long`, `String`, `boolean`, and so forth.

**容器执行bean依赖项解析如下:**

- ApplicationContext是用描述所有bean的配置元数据创建和初始化的。配置元数据可以由XML、Java代码或注释指定。
- 对于每个bean，其依赖项都以属性、构造函数参数或静态工厂方法的参数的形式表示(如果您使用该方法而不是普通的构造函数)。这些依赖项是在bean实际创建时提供给bean的。
- 每个属性或构造函数参数都是要设置的值的实际定义，或者是对容器中另一个bean的引用。
- 值的每个属性或构造函数参数都从其指定的格式转换为该属性或构造函数参数的实际类型。默认情况下，Spring可以将以字符串格式提供的值转换为所有内置类型，如int、long、string、boolean等。

> The Spring container validates the configuration of each bean as the container is created. However, the bean properties themselves are not set until the bean is actually created. Beans that are singleton-scoped and set to be pre-instantiated (the default) are created when the container is created. Scopes are defined in [Bean Scopes](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes). Otherwise, the bean is created only when it is requested. Creation of a bean potentially causes a graph of beans to be created, as the bean’s dependencies and its dependencies' dependencies (and so on) are created and assigned. Note that resolution mismatches among those dependencies may show up late — that is, on first creation of the affected bean.
>
> 在创建容器时，Spring容器验证每个bean的配置。但是，在实际创建bean之前，不会设置bean属性本身。在创建容器时，将创建**单例作用域**的bean并将其设置为**预实例化**(默认值)。作用域是在Bean作用域中定义的。另外，仅在请求bean时才创建它。当bean的依赖项及其依赖项的依赖项(等等)被创建和分配时，bean的创建可能会创建一个bean图。注意解决依赖项延迟出现的不匹配问题，第一次创建将影响Bean。不是很懂最后一句

Circular dependencies

If you use predominantly constructor injection, it is possible to create an unresolvable circular dependency scenario.

For example: Class A requires an instance of class B through constructor injection, and class B requires an instance of class A through constructor injection. If you configure beans for classes A and B to be injected into each other, the Spring IoC container detects this circular reference at runtime, and throws a `BeanCurrentlyInCreationException`.

One possible solution is to edit the source code of some classes to be configured by setters rather than constructors. Alternatively, avoid constructor injection and use setter injection only. In other words, although it is not recommended, you can configure circular dependencies with setter injection.

Unlike the typical case (with no circular dependencies), a circular dependency between bean A and bean B forces one of the beans to be injected into the other prior to being fully initialized itself (a classic chicken-and-egg scenario).

**循环依赖**

>  如果主要使用构造函数注入，可能会创建无法解析的循环依赖场景。
>
> 例如:类A需要一个类B通过构造函数注入的实例，而类B需要一个类A通过构造函数注入的实例。如果您将bean配置为类A和B相互注入，Spring IoC容器将在运行时检测这个循环引用，并抛出一个BeanCurrentlyInCreationException异常。
>
> 一种可能的解决方案是编辑某些类的源代码，由setter注入而不是构造函数来注入。另外，避免构造函数注入，只使用setter注入。换句话说，尽管不建议这样做，但您可以使用setter注入配置循环依赖项。
>
> 与典型的情况(没有循环依赖关系)不同，bean A和bean B之间的循环依赖关系迫使一个bean在完全初始化之前被注入到另一个bean中(典型的先有鸡还是先有蛋的场景)。

**下面来模拟一下循环依赖**

首先两个JavaBean：

```java
@AllArgsConstructor
public class Checken {
    private Egg egg;
}
@AllArgsConstructor
public class Egg {
    private Checken checken;
}
```

然后再xml中配置：

```xml
<!-- 循环依赖 -->
    <bean id="checken" class="com.leishida.spring.Checken">
        <constructor-arg name="egg" ref="egg"/>
    </bean>
    <bean id="egg" class="com.leishida.spring.Egg">
        <constructor-arg name="checken" ref="checken"/>
    </bean>
```

然后测试一下：

```java
    @org.junit.Test
    public void test5(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object checken = context.getBean("checken");
        Object egg = context.getBean("egg");
        System.out.println(checken +" "+ egg);       
        /**
         * org.springframework.beans.factory.BeanCreationException: 
         * Error creating bean with name 'checken' defined in class path resource 
         * [applicationContext.xml]: Cannot resolve reference to bean 'egg' while setting constructor argument; 
         * nested exception is org.springframework.beans.factory.BeanCreationException: 
         * Error creating bean with name 'egg' defined in class path resource [applicationContext.xml]: 
         * Cannot resolve reference to bean 'checken' while setting constructor argument; 
         * nested exception is org.springframework.beans.factory.BeanCurrentlyInCreationException: 
         * Error creating bean with name 'checken': Requested bean is currently in creation: 
         * Is there an unresolvable circular reference?
         */
    }
```

现在我们将注入方式改为set注入看看能不能解决问题：

```xml
    <bean id="egg" class="com.leishida.spring.Egg">
        <property name="checken" ref="checken"/>
    </bean>
    <bean id="checken" class="com.leishida.spring.Checken">
        <property name="egg" ref="egg"/>
    </bean>
<!-- 注意对应的无参构造器和set方法，如果没有会报错-->
```

```java
@org.junit.Test
    public void test6() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object checken = context.getBean("checken");
        Object egg = context.getBean("egg");
        System.out.println(checken + " " + egg);    //Checken(egg=com.leishida.spring.Egg@c8e4bb0) com.leishida.spring.Egg@c8e4bb0
    }
```

> You can generally trust Spring to do the right thing. It detects configuration problems, such as references to non-existent beans and circular dependencies, at container load-time. Spring sets properties and resolves dependencies as late as possible, when the bean is actually created. This means that a Spring container that has loaded correctly can later generate an exception when you request an object if there is a problem creating that object or one of its dependencies — for example, the bean throws an exception as a result of a missing or invalid property. This potentially delayed visibility of some configuration issues is why `ApplicationContext` implementations by default pre-instantiate singleton beans. At the cost of some upfront time and memory to create these beans before they are actually needed, you discover configuration issues when the `ApplicationContext` is created, not later. You can still override this default behavior so that singleton beans initialize lazily, rather than being pre-instantiated.

你通常可以相信spring总会做正确的事情。他会在容器的加载阶段检测配置问题，例如引用了不存在的Bean对象和循环依赖。在Bean对象真正的创建时，它会尽可能晚的设置属性和解决依赖。这意味着，如果在创建对象或其依赖项时出现问题，则在请求对象时，已正确加载的Spring容器稍后可以生成异常——例如，由于缺少或无效的属性，bean会抛出异常。一些配置问题的潜在延迟可见性是ApplicationContext实现默认预实例化单例bean的原因。（如果是多例对象，那么所有请求非法的bean都会同时抛出异常），在实际需要这些bean之前花费一些前期时间和内存来创建它们，您将在创建ApplicationContext时发现配置问题，而不是稍后。**（也就是说，你在实例化ApplicationContext的时候，它就会创建所有配置的bean，并及时发现他们的问题）**您仍然可以覆盖这个默认行为，这样单例bean就可以延迟初始化，而不是预先实例化。**（ApplicationContext在创建bean对象时的模式时立即加载，但你可以通过修改配置，让它变为懒加载）**

> If no circular dependencies exist, when one or more collaborating beans are being injected into a dependent bean, each collaborating bean is totally configured prior to being injected into the dependent bean. This means that, if bean A has a dependency on bean B, the Spring IoC container completely configures bean B prior to invoking the setter method on bean A. In other words, the bean is instantiated (if it is not a pre-instantiated singleton), its dependencies are set, and the relevant lifecycle methods (such as a [configured init method](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean) or the [InitializingBean callback method](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean)) are invoked.

如果不存在循环依赖项，那么当一个或多个协作bean被注入一个依赖bean时，每个协作bean在被注入依赖bean之前都要完全配置好。这意味着,如果beanA依赖bean B, Spring IoC容器会优先完全配置beanB，然后再调用beanA的setter方法。换句话说,bean实例化(如果它不是一个单例预先实例化),他的依赖项就被设置,相关的生命周期方法(如InitializingBean init方法或配置回调方法)调用。

### 2.4.2. Dependencies and Configuration in Detail  依赖配置的细节

这一部分具体讲解了属性注入的具体方法：

#### Straight Values 直接的值

**P标签的使用：**

再使用p命名空间时，需要首先导入约束：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"	<!-- p命名空间约束 -->
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="score" class="com.leishida.spring.Score"></bean>
	<bean id="user4" class="com.leishida.spring.User" p:name="P标签导入" p:age="18" p:score-ref="score"/>

</beans>
```

**The `idref` element ** **idref元素**

The `idref` element is simply an error-proof way to pass the `id` (a string value - not a reference) of another bean in the container to  a <constructor-arg/>  or <property/>  element. The following example shows how to use it:

**idref元素只是将容器中另一个bean的id(字符串值，而不是引用)传递给<constructor-arg/>或<property/>元素。**

```xml
    <bean id="user4" class="com.leishida.spring.User" p:name="P标签导入" p:age="18" p:score-ref="score"/>

    <bean id="user5" class="com.leishida.spring.User">
        <property name="name">
            <idref bean="user4"/>
        </property>
    </bean>
```

结果：

```java
    @org.junit.Test
    public void test7() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object user5 = context.getBean("user5");
        System.out.println(user5);  //User(name=user4, age=0, score=null)
    }
```

可以看到，上面的xml配置和下面的是等同的效果：

```xml
    <bean id="user5" class="com.leishida.spring.User">
        <property name="name" value="user4"/>
    </bean>
```

那它为啥要整个idref。。神经病么，显然不是：

> The first form is preferable to the second, because using the `idref` tag lets the container validate at deployment time that the referenced, named bean actually exists. In the second variation, no validation is performed on the value that is passed to the `targetName` property of the `client` bean. Typos are only discovered (with most likely fatal results) when the `client` bean is actually instantiated. If the `client` bean is a [prototype](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes) bean, this typo and the resulting exception may only be discovered long after the container is deployed.

官方说，第一种方式比第二种方式更为可取，为什么呢：因为idref标签让容器再部署时校验这个引用，命名的这个bean事实上时存在的。而第二种方式中对name属性的值不执行任何验证，只有在实际实例化客户端bean时才会发现拼写错误(很可能导致致命的结果)。如果客户机bean是原型多例的，则此类型错误和由此产生的异常可能只有在部署容器之后很久才会被发现。

A common place (at least in versions earlier than Spring 2.0) where the `` element brings value is in the configuration of [AOP interceptors](https://docs.spring.io/spring/docs/5.2.5.BUILD-SNAPSHOT/spring-framework-reference/core.html#aop-pfb-1) in a `ProxyFactoryBean` bean definition. Using `` elements when you specify the interceptor names prevents you from misspelling an interceptor ID.

**官方还列出了idref的使用场景，那就是在ProxyFactoryBean bean定义中的AOP拦截器配置中。在指定拦截器名称时使用<idref/>元素可以防止对拦截器ID的拼写错误。**

#### References to Other Beans (Collaborators)  对其他bean的引用（协作者）

> 引用其他bean最常用的方式就是通过<ref/>标签，这个标签允许在同一个容器或父容器中创建对任何bean的引用，他们可以不在同一个xml配置文件之中。你可以使用与引用的bean的相同id或name来指定。

Specifying the target bean through the `parent` attribute creates a reference to a bean that is in a parent container of the current container. The value of the `parent` attribute may be the same as either the `id` attribute of the target bean or one of the values in the `name` attribute of the target bean. The target bean must be in a parent container of the current one. You should use this bean reference variant mainly when you have a hierarchy of containers and you want to wrap an existing bean in a parent container with a proxy that has the same name as the parent bean. The following pair of listings shows how to use the `parent` attribute:

> 这里还有一堆关于父容器的说明：通过parent属性指定目标bean将创建对当前容器的父容器中的bean的引用。父属性的值可以与目标bean的id属性相同，也可以与目标bean的name属性中的一个值相同。目标bean必须位于当前bean的父容器中。您应该主要在容器层次结构中使用此bean引用变体，并且希望将现有的bean与具有与父bean相同名称的代理包装在父容器中。

```xml
<!-- in the parent context -->
<bean id="accountService" class="com.something.SimpleAccountService">
    <!-- insert dependencies as required as here -->
</bean>

==========================================================================
<!-- in the child (descendant) context -->
<bean id="accountService" <!-- bean name is the same as the parent bean -->
    class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target">
        <ref parent="accountService"/> <!-- notice how we refer to the parent bean -->
    </property>
    <!-- insert other configuration and dependencies as required here -->
</bean>
```

**这个部分因为没有具体使用到，所以不清楚他的作用**

#### Inner Beans

```xml
    <!-- inner bean-->
    <bean id="user6" class="com.leishida.spring.User">
        <property name="score">
            <bean class="com.leishida.spring.Score">
                <property name="english" value="100"/>
                <property name="math" value="90"/>
            </bean>
        </property>
    </bean>
```

```java
    @org.junit.Test
    public void test8() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object user6 = context.getBean("user6");
        System.out.println(user6);  //User(name=null, age=0, score=Score(English=100, Math=90))
    }
```

An inner bean definition does not require a defined ID or name. If specified, the container does not use such a value as an identifier. The container also ignores the `scope` flag on creation, because inner beans are always anonymous and are always created with the outer bean. It is not possible to access inner beans independently or to inject them into collaborating beans other than into the enclosing bean.

> 内部bean定义不需要已定义的ID或名称。如果指定，容器将不使用此类值作为标识符。容器还会忽略创建时的范围标志，因为内部bean始终是匿名的，并且始终使用外部bean创建。不可能独立地访问内部bean，也不可能将它们注入到协作的bean中，而不是注入到封闭的bean中。

As a corner case, it is possible to receive destruction callbacks from a custom scope — for example, for a request-scoped inner bean contained within a singleton bean. The creation of the inner bean instance is tied to its containing bean, but destruction callbacks let it participate in the request scope’s lifecycle. This is not a common scenario. Inner beans typically simply share their containing bean’s scope.

> 作为一种特殊情况，可以从自定义范围接收销毁回调—例如，对于单例bean中包含的请求范围的内部bean。内部bean实例的创建被绑定到它所包含的bean，但是销毁回调让它参与请求作用域的生命周期。这不是一个常见的场景。内部bean通常只是简单地共享其包含bean的范围。

#### Collections 集合

<list/>、<set/>、<map/>、<props/>元素分别设置了Java集合类型list、set、map和properties的属性和参数。

Java类：

```java
@Data
@NoArgsConstructor
public class ComplexObject {
    private Properties email;
    private List list;
    private Map map;
    private Set set;
}
```

```xml
	<bean id="complexObject" class="com.leishida.spring.ComplexObject">
        <!-- props 类型-->
        <property name="email">
            <props>
                <prop key="email">123456@qq.com</prop>
            </props>
        </property>
        <property name="list">
            <list>
                <value>list类型</value>
                <ref bean="user6"></ref>
            </list>
        </property>
        <property name="map">
            <map>
                <entry key="entry" value="this is entry"></entry>
                <entry key="ref" value-ref="user6"></entry>
            </map>
        </property>
        <property name="set">
            <set>
                <value>this is a set</value>
                <ref bean="user6"></ref>
            </set>
        </property>
    </bean>
	<bean id="user6" class="com.leishida.spring.User">
        <property name="score">
            <bean class="com.leishida.spring.Score">
                <property name="english" value="100"/>
                <property name="math" value="90"/>
            </bean>
        </property>
    </bean>
```

**测试：**

```java
    @org.junit.Test
    public void test9() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object complexObject = context.getBean("complexObject");
        System.out.println(complexObject);  
        //ComplexObject(email={email=123456@qq.com}, list=[list类型, User(name=null, age=0, score=Score(English=100, Math=90))], map={entry=this is entry, ref=User(name=null, age=0, score=Score(English=100, Math=90))}, set=[this is a set, User(name=null, age=0, score=Score(English=100, Math=90))])
    }
```

The value of a map key or value, or a set value, can also be any of the following elements:

> Map的key或value或者set的value可以是一下任何一种元素：
>
> bean | ref | idref | list | set | map | props | value | null

**Collection Merging  集合合并**

The Spring container also supports merging collections. An application developer can define a parent <list/>, <map/>, <set/> or <props/> element and have child <list/>, <map/>, <set/> or <props/> elements inherit and override values from the parent collection. That is, the child collection’s values are the result of merging the elements of the parent and child collections, with the child’s collection elements overriding values specified in the parent collection.

> Spring容器还支持合并集合。应用程序开发人员可以定义父元素<list/>、<map/>、<set/>或<props/>元素，并拥有子元素<list/>、<map/>、<set/>或<props/>元素从父集合继承和覆盖值。也就是说，子集合的值是父集合和子集合的元素合并的结果，子集合的元素覆盖父集合中指定的值。

## 2.7 Bean Definition Inheritance Bean定义继承

A bean definition can contain a lot of configuration information, including constructor arguments, property values, and container-specific information, such as the initialization method, a static factory method name, and so on. A child bean definition inherits configuration data from a parent definition. The child definition can override some values or add others as needed. Using parent and child bean definitions can save a lot of typing. Effectively, this is a form of templating.

> bean定义可以包含很多配置信息，包括构造函数参数、属性值和特定于容器的信息，比如初始化方法、静态工厂方法名等等。子bean定义从父定义继承配置数据。子定义可以根据需要覆盖某些值或添加其他值。使用父bean和子bean定义可以节省大量输入。实际上，这是模板的一种形式。

If you work with an `ApplicationContext` interface programmatically, child bean definitions are represented by the `ChildBeanDefinition` class. Most users do not work with them on this level. Instead, they configure bean definitions declaratively in a class such as the `ClassPathXmlApplicationContext`. When you use XML-based configuration metadata, you can indicate a child bean definition by using the `parent` attribute, specifying the parent bean as the value of this attribute. The following example shows how to do so:

> 如果您以编程方式使用ApplicationContext接口，则子bean定义由ChildBeanDefinition类表示。大多数用户在这个级别上不使用它们。相反，它们在类(如ClassPathXmlApplicationContext)中声明式地配置bean定义。当您使用基于xml的配置元数据时，您可以通过使用父属性来指示子bean定义，并将父bean指定为该属性的值。下面的例子演示了如何做到这一点:



# 3. Testing 测试

#  4. Data Access 数据访问

# 5. webServlet

# 6. WebReactive

# 7. Integration 集成

# 8. Languages 语言

