# Spring知识点集合（来自《Spring实战（第4版）》）
---

## 一、Spring核心

1.POJO与javaBean（解释来自维基百科）

POJO是一个Java对象，除了Java语言规范强制的限制之外，不受任何限制的约束; 即一个POJO 不应该扩展预先指定的类、实现预先指定的接口、包含预先指定的注解（考虑实际因素许多POJO仍需要注解来实现正常的功能，例如持久化，所以如果在添加任何注释之前对象（实际上是类）是POJO，并且如果注释被删除则返回到POJO状态，那么它仍然可以被认为是POJO）。

javaBean是一个序列化的POJO，有一个无参数的构造函数，并允许访问使用属性getter和setter方法遵循一个简单的命名约定（类必须实现Serializable接口是一个真正的JavaBean）。

**综上，POJO的定义范围包含了JavaBean**

2.Spring降低Java开发复杂度的策略

- 基于POJO的轻量级和最小侵入性编程；
- 通过依赖注入和面向接口实现松耦合；
- 基于切面和惯例进行声明式编程；
- 通过切面和模板减少样板式代码。

3.依赖注入（DI）

DI能够让相互协作的软件组件保持松散耦合

设值注入（使用get/set方法）、构造注入（作为构造函数的参数注入）

- 基于xml配置

	![](https://i.imgur.com/hCvxOQK.png)
- 基于Java配置（通过编写配置类实现）

	![](https://i.imgur.com/BfClLf1.png)
- 基于注解

Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。

ClassPathXmlApplicationContext 作为应用上下文适用于通过xml文件进行配置的注入方式，对于基于Java的配置，Spring提供了AnnotationConfigApplicationContext。

4.面向切面（AOP）

AOP可以把遍布应用各处的功能分离出来形成可重用的组件

面向切面编程往往被定义为促使软件系统实现关注点的分离一项技术。系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。如果将这些关注点分散到多个组件中去，代码将会带来双重的复杂性：1.实现系统关注点功能的代码将会重复出现在多个组件中；2.组件会因为那些与自身核心业务无关的代码而变得混乱。

AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。所造成的结果就是这些组件会具有更高的内聚性并且会更加关注自身的业务，完全不需要了解涉及系统服务所带来复杂性。总之，AOP能够确保POJO的简单性。

![](https://i.imgur.com/DgDh9wv.png)
（可以联想到模板方法模式）

5.spring容器

![](https://i.imgur.com/Usj6rsP.png)

在Spring应用中，对象由Spring容器创建和装配，并存在于容器之中

容器是Spring框架的核心。Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。毫无疑问，这些对象更简单干净，更易于理解，更易于重用并且更易于进行单元测试。

Spring容器并不是只有一个。Spring自带了多个容器实现，可以归为两种不同的类型。bean工厂（由org.springframework.beans.factory.eanFactory接口定义）是最简单的容器，提供基本的DI
支持。应用上下文（由org.springframework.context.ApplicationContext接口定义）基于BeanFactory构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。很明显，应用上下文的方式更受欢迎。

应用上下文

- AnnotationConfigApplicationContext：从一个或多个
基于Java的配置类中加载Spring应用上下文。
- AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
- ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
- FileSystemXmlapplicationcontext：从文件系统下的一个或多个XML配置文件中加载上下文定义。
- XmlWebApplicationContext：从Web应用下的一个或多个XML配置文件中加载上下文定义。

一些例子：

ApplicationContext context = new FileSystemAplicationContext("c:/xxx.xml");//在指定的文件系统下查找xml文件

ApplicationContext context = new ClassPathXmlApplicationContext("xxx.xml");//在所有的类路径上（包含JAR文件）下查找xml文件

ApplicationContext context = new AnnotationConfigApplicationContext(com.xxx.xxx.class);//加载配置类

bean的生命周期

1. Spring对bean进行实例化；
2. Spring将值和bean的引用注入到bean对应的属性中；
3. 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBean-Name()方法；
4. 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入；
5. 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext()方法，将bean所在的应用上下文的引用传入进来；
6. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessBeforeInitialization()方法；
7. 如果bean实现了InitializingBean接口，Spring将调用它们的after-PropertiesSet()方法。类似地，如果bean使用init-method声明了初始化方法，该方法也会被调用；
8. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessAfterInitialization()方法；
9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
10. 如果bean实现了DisposableBean接口，Spring将调用它的destroy()接口方法。同样，如果bean使用destroy-method声明了销毁方法，该方法也会被调用。

6.spring模块

![](https://i.imgur.com/3LMBbPO.png)

spring核心容器

容器是Spring框架最核心的部分，它管理着Spring应用中bean的创建、配置和管理。它包含了Spring bean工厂（提供DI功能），基于bean工厂又实现了多种的Spring应用上下文（为配置Spring实现了多种方式），此外，此模块也提供了许多企业服务（比如Email、JNDI访问、EJB集成和调度）。所有的Spring模块都构建于核心容器之上。配置应用时，其实会隐式地使用这些类。

spring的AOP模块

在AOP模块中，Spring对面向切面编程提供了丰富的支持。这个模块是Spring应用系统中开发切面的基础。与DI一样，AOP可以帮助应用对象解耦。借助于AOP，可以将遍布系统的关注点（例如事务和安全）从它们所应用的对象中解耦出来。

数据访问与集成

抽象和封装了大量通过JDBC数据库访问的方法，使得数据访问代码不再庞杂。另外还提供了ORM模块，Spring没有尝试去创建自己的ORM解决方案，而是对许多流行的ORM框架进行了集成，包括Hibernate、Java Persisternce API、Java Data Object和iBATIS SQL Maps。Spring的事务管理支持所有的ORM框架以及JDBC。本模块同样包含了在JMS（Java Message Service）之上构建的Spring抽象层，它会使用消息以异步的方式与其他应用集成。除此之外，本模块会使用Spring AOP模块为Spring应用中的对象提供事务管理服务。

Web与远程调用

MVC（Model-View-Controller）模式是一种普遍被接受的构建Web应用的方法，它可以帮助用户将界面逻辑与应用逻辑分离。除了集成现有的各大MVC框架（struts等），它还自带了一个强大的MVC框架（Spring MVC）。除了面向用户的Web应用，该模块还提供了多种构建与其他应用交互的远程调用方案。Spring远程调用功能集成了RMI（Remote MethodInvocation）、Hessian、Burlap、JAX-WS，同时Spring还自带了一个远程调用框架：HTTP invoker。Spring还提供了暴露和使用REST API的良好支持。

Instrumentation

Spring的Instrumentation模块提供了为JVM添加代理（agent）的功能。具体来讲，它为Tomcat提供了一个织入代理，能够为Tomcat传递类文件，就像这些文件是被类加载器加载的一样。

测试

Spring提供了测试模块以致力于Spring应用的测试。通过该模块，你会发现Spring为使用JNDI、Servlet和Portlet编写单元
测试提供了一系列的mock对象实现。对于集成测试，该模块为加载Spring应用上下文中的bean集合以及与Spring上下文中的bean进行交互提供了支持。

7.Spring Portfolio

整个Spring Portfolio包括多个构建于核心Spring框架之上的框架和类库。概括地讲，整个Spring Portfolio几乎为每一个领域的Java开发都提供了Spring编程模型。

- Spring Web Flow建立于Spring MVC框架之上，它为基于流程的会话式Web应用（可以想一下购物车或者向导功能）提供了支持。
- Spring Web Service提供了契约优先的Web Service模型，服务的实现都是为了满足服务的契约而编写的。
- Spring Security利用Spring AOP为Spring应用提供了声明式的安全机制。
- Spring Integration提供了多种通用应用集成模式的Spring声明式风格实现。
- Spring Batch用于开发一个批处理应用，使用Spring强大的面向POJO的编程模型。
- Spring Data使得在Spring中使用任何数据库都变得非常容易。
- Spring Social更多的是关注连接（connect），而不是社交（social）。它能够帮助你通过REST API连接Spring应用，其中有些Spring应用可能原本并没有任何社交方面的功能目标。
- Spring Mobile是Spring MVC新的扩展模块，用于支持移动Web应用开发。
- Spring for Android旨在通过Spring框架为开发基于Android设备的本地应用提供某些简单的支持。
- Spring Boot大量依赖于自动配置技术，它能够消除大部分（在很多场景中，甚至是全部）Spring配置。它还提供了多个Starter项目，不管使用Maven还是Gradle，这都能减少Spring工程构建文件的大小。

## 二、Bean装配

1.方式

- 在XML中进行**显式**配置。
- 在Java中进行**显式**配置。
- **隐式**的bean发现机制和自动装配。

2.自动化装配

- 组件扫描（component scanning）：Spring会自动发现应用上下文中所创建的bean。
- 自动装配（autowiring）：Spring自动满足bean之间的依赖。

2.1创建bean

@Component注解表明该类会作为组件类，并告知Spring要为这个类创建bean。

@ComponentScan注解启用组件扫描，@ComponentScan默认会扫描与配置类相同的包及其以下的子包，去寻找带有@Component注解的类，并为其创建bean。

可以使用XML来启用组件扫描： <context:component-scan>

2.1.1为组建扫描的bean命名

- @Component("newNameOfBean")
- @Named("newNameOfBean")（Spring支持将@Named作为@Component注解的替代方案。两者之间有一些细微的差异，但是在大多数场景中，它们是可以互相替换的）

Spring应用上下文中所有的bean都会给定一个ID。尽管没有明确地为SgtPeppersbean设置ID，但Spring会根据类名为其指定一个ID（也就是将类名的第一个字母变为小写）。

2.1.2设置组件扫描时的多个基础包

@CompnentScan("xxx")
@CompnentScan(basePackages={"xxx", "yyy", "zzz"})
@CompnentScan(basePackagesClasses={xxx.class, yyy.class, zzz.class})【xxx，yyy，zzz等类所在的基础包】

2.2装配bean

@Autowired注解实现自动装配。@Autowired注解不仅能够用在构造器上，还能用在属性的Setter方法上。

@Autowired(required=false)，此时，Spring会尝试执行自动装配，但是如果没有匹配的bean的话，Spring将会让这个bean处于未装配的状态。

如果有多个bean都能满足依赖关系的话，Spring将会抛出一个异常，表明没有明确指定要选择哪个bean进行自动装配。

@Inject注解来源于Java依赖注入规范，该规范同时还为我们定义了@Named注解。@Inject可替换@Autowired

3.通过Java代码装配bean

尽管在很多场景下通过组件扫描和自动装配实现Spring的自动化配置是更为推荐的方式，但有时候自动化配置的方案行不通，因此需要明确配置Spring。比如说，你想要将第三方库中的组件装配到你的应用中，在这种情况下，是没有办法在它的类上添加@Component和@Autowired注解的，因此就不能使用自动化装配的方案了。

@Configuration注解表明这个类是一个配置类，该类应该包含在Spring应用上下文中如何创建bean的细节；

@Bean注解会告诉Spring这个方法将会返回一个对象，该对象要注册为Spring应用上下文中的bean。方法体中包含了最终产生bean实例的逻辑。默认情况下，bean的ID与带有@Bean注解的方法名是一样的。在本例中，bean的名字将会是sgtPeppers。如果你想为其设置成一个不同的名字的话，那么可以重命名该方法，也可以通过name属性指定一个不同的名字【@Bean(name="newBeanName")】

4.通过xml装配bean【已过时，不多讲】

![](https://i.imgur.com/aRjCe25.png)

5.混合装配bean

基于java配置类

@Import注解导入类资源，可用于导入配置类，@Import(config.class)

@ImportResource注解导入资源文件，可用于导入xml配置文件，@ImportResource("xxx.xml")

基于xml

使用<bean>导入java配置类、使用<import>导入其他xml文件

![](https://i.imgur.com/Qo1EPJc.png)

**尽可能使用自动化配置，以避免显式配置所带来的维护成
本。但是，如果确实需要显式配置Spring的话，应该优先选择基于
Java的配置，它比基于XML的配置更加强大、类型安全并且易于重
构。**

## 三、高级装配

1.Spring的profile机制实现生产环境与开发环境的切换

2.条件化生成bean

@Conditional(xxx.class)【xxx.class可以是任意的实现了Condition接口的类】

	public interface Condition{
		boolean matches(ConditionContext ctxt, AnnotatedTypeMetadata metadata);
	}
	//这个接口实现起来很简单直接，只需提供matches()方法的实现即可。如果matches()方法返回true，那么就会创建带有@Conditional注解的bean。如果matches()方法返回false，将不会创建这些bean。

CondtionContext是一个接口，通过它可以辅助条件的决策

	public interface ConditionContext{
		BeanDefinitionRegistry getRegistry();
		ConfigurableListableBeanFactory getBeanFactory();
		ResourceLoader getResourceLoader();
		ClassLoader getCladdLoader();
	}
- 借助getRegistry()返回的BeanDefinitionRegistry检查bean定义；
- 借助getBeanFactory()返回的ConfigurableListableBeanFactory检查bean是否存在，甚至探查bean的属性；
- 借助getEnvironment()返回的Environment检查环境变量是否存在以及它的值是什么；
- 读取并探查getResourceLoader()返回的ResourceLoader所加载的资源；
- 借助getClassLoader()返回的ClassLoader加载并检查类是否存在。

AnnotatedTypeMetadata也是一个接口，可以检查带有@Bean注解的方法上还有什么其他的注解。

3.自动装配的歧义性

@Primary注解表示bean装配出现歧义时，使用@Primary注解标识的bean(即首选的bean)，它并不能完全解决歧义性的问题，只能缩小可选择的bean的范围

@Qualifier注解可以得到限定的唯一无歧义的bean，@Qualifier("beanId")，值可以为bean的ID，或者通过@Qualifier修改过的限定名

使用自定义注解以区别不同的bean

4.bean的作用域

在默认情况下，Spring应用上下文中所有bean都是作为以单例（singleton）的形式创建的。也就是说，不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例。

在大多数情况下，单例bean是很理想的方案。初始化和垃圾回收对象实例所带来的成本只留给一些小规模任务，在这些任务中，让对象保持无状态并且在应用中反复重用这些对象可能并不合理。

作用域：

- 单例（Singleton）：在整个应用中，只创建bean的一个实例。
- 原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例。
- 会话（Session）：在Web应用中，为每个会话创建一个bean实例。
- 请求（Rquest）：在Web应用中，为每个请求创建一个bean实例。

单例是默认的作用域，但是正如之前所述，对于易变的类型，这并不合适。如果选择其他的作用域，要使用@Scope注解，它可以与@Component或@Bean一起使用。

	@Component
	@Scope(ConfigurableBeanFactory.SCOPE_ROTOTYPE)
	//@Scope("prototype")
	public class Notedpad{ ... }

@Scope(value=ConfigurableBeanFactory.SCOPE_ROTOTYPE, proxyMode=ScopedProxyMode.INTERFACES)//作用域代理(proxyMode)能够延迟注入请求和会话作用域的bean

5.注入外部值

@PropertySource注解可以引用属性源文件（app.properties），避免硬编码

使用占位符“${ ... }”，@Value("${ ... }")，使用占位符，需要装配PropertySourcesPlaceholderConfigurer bean

6.使用Spring表达式语言（Spring Expression Language，SpEL）装配

## 四、AOP

通知

- 前置通知（Before）：在目标方法被调用之前调用通知功能；
- 后置通知（After）：在目标方法完成之后调用通知，此时不会关心方法的输出是什么；
- 返回通知（After-returning）：在目标方法成功执行之后调用通知；
- 异常通知（After-throwing）：在目标方法抛出异常后调用通知；
- 环绕通知（Around）：通知包裹了被通知的方

术语

- 连接点（Join point），在应用执行过程中能够插入切面的一个点
- 切点（Poincut），切点的定义会匹配通知所要织入的一个或多个连接点
- 切面（Aspect），切面是通知和切点的结合。通知和切点共同定义了切面的全部内容——它是什么，在何时和何处完成其功能
- 引入（Introduction），允许我们向现有的类添加新方法或属性
- 织入（Weaving），是把切面应用到目标对象并创建新的代理对象的过程，切面在指定的连接点被织入到目标对象中。在目标对象的生命周期里有多个点可以进行织入：

	- 编译期：切面在目标类编译时被织入。这种方式需要特殊的编译器。AspectJ的织入编译器就是以这种方式织入切面的。
    - 类加载期：切面在目标类加载到JVM时被织入。这种方式需要特殊的类加载器（ClassLoader），它可以在目标类被引入应用之前增强该目标类的字节码。AspectJ 5的加载时织入（load-time-weaving，LTW）就支持以这种方式织入切面。
	- 运行期：切面在应用运行的某个时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象动态地创建一个代理对象。Spring AOP就是以这种方式织入切面的。