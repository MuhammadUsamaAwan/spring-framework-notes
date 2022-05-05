# Spring

## Setting up Development Environment

For Spring you need:

1. Java application server (like Tomcat)
1. Java Integrated Development Enivornment (like Eclipse)

Connect Eclipe with Tomcat by adding a new server at the bottom of Eclipse on the servers tab.<br>
To add Spring to your project, first download Spring, copy jar files to your project, then add jar files [right click project > properties > java build path > add jars then add all jars].<br>

<br>

## 1. XML Based Configuration

### Inversion of Control (Ioc)

```xml
<!-- 1. Configuring Metdata-->
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
        <!-- id is used to identify bean -->
        <!-- class is fully qualified classname -->
        <!-- To get qualified name go to class right click class name > copy qualified name -->
    </bean>

</beans>
```

```java
// 2. Load the spring configuration file
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
// 3. Retrieve bean from spring container
SomeClass someClass = context.getBean("someClass", SomeInterface.class);
// 4. Call methods on the bean
someClass.someMethod();
// 5. Close the context
context.close();
```

### Dependency Injection (DI)

```java
// 1. Define the dependency interface and class
// Create an interface and a class
```

```java
// 2. Define a private field for the dependency
private OtherClass otherClass;
// 3a. Create a constructor in your class for injection
// Quick tip to generate constructor right click > source > generate constructor using fields
public SomeClass(OtherClass otherClass) {
    this.otherClass = otherClass;
}
// 3b. Create setter method in your class for injection
// Quick tip to generate setter right click > source > generate getters and setters > check setter method
public void setSomeClass(SomeClass otherClass) {
    this.otherClass = otherClass;
}
// use this dependency
```

```xml
<!-- 4. Configure the dependency injection in Spring config file -->
<!-- this set is not required in Java Annotaion Method -->
<bean id="otherBean" class="packageName.OtherClass">
</bean>

<bean id="bean" class="packageName.SomeClass">
    <!-- Constructor injection -->
    <constructor-arg ref="otherBean">
</bean>

<bean id="bean" class="packageName.SomeClass">
    <!-- Setter injection -->
    <!-- name = setSomeClass minus set and lower case next letter -->
    <property name="someClass" ref="otherBean">
</bean>
```

```java
// 4. Call the method(s) from dependency
someClass.getOtherClass();
```

### Injecting Literal Values

```java
// 1. Add new fields
private String name;
// 2. Add setter method
public void setName(String name) {
    this.name = name;
}
```

```xml
<!-- 3. Configure the injection in Spring config file -->
<bean id="..." class="...">
    <property name="name" value="valueOfName">
</bean>
```

### Injecting Values from Property File

```xml
<!-- 1. Load property file -->
<context:property-placeholder location="classpath:name.properties">
<!-- 2. Reference values from property file -->
<bean id="..." class="...">
    <property name="name" value="{name}">
</bean>
```

### Bean Scope

Default scope for a bean is singleton (one instance of the bean). All request for that bean will return a share reference to the same bean.
| Scope | Description |
|---|---|
| singleton | single instance of the bean |
| prototype | new isntance for each container request |
| request | scoped to a HTTP web request |
| session | scoped to a HTTP web session |
| global-session | scoped to a global HTTP web session |

```xml
<bean id="..." name="..." scope="prototype">
</bean>
```

### Bean Lifecycle

Container Started > Bean Instantiated > Dependencies Injected > Internal Spring Processing > Your Custom Init Method<br>
You can add custom code when during bean initialization and destruction

```xml
<bean init-method="doStartupStuff" destroy-method="doCleanupStuff">
</bean>
```

<br>

## 2. Annotation Based Configuration

### Inversion of Control (Ioc)

```xml
<!-- 1. Configuring Metadata -->
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

     <context:component-scan base-package="nameOfTheClass">

</beans>
```

```java
//2. Add Java annotation to the class
@Component("someClass") // someClass = beanId
@Component // beanId = someClass (classname first letter lowercased)
public class SomeClass implements SomeInterface {
}
```

```java
// 2. Load the Spring configuration file
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
// 3. Retrieve bean from Spring container
SomeClass someClass = context.getBean("someClass", SomeInterface.class);
// 4. Call methods on the bean
someClass.someMethod();
// 5. Close the context
context.close();
```

### Dependency Injection (DI)

```java
// 1. Define the dependency interface and class
// Create an interface and a class
```

```java
// 2. Define a private field for the dependency
// @Autowired   3a. Field Injection
private OtherClass otherClass;
// 3b. Contructor injection
@Autowired
public SomeClass(OtherClass otherClass) {
    this.otherClass = otherClass;
}
// 3c. Setter injection
@Autowired
public void setOtherClass(OtherClass otherClass) {
    this.otherClass = otherClass;
}
// 2d. Method Injection
@Autowired
public void anyMethod(OtherClass otherClass) {
}
```

```java
// 4. Call the method(s) from dependency
someClass.otherClassMethod();
```

### @Qualifier Annotation

```java
@Autowired
@Qualifier("beanId") //incase of multiple implementation
```

### Injecting Values into Fields

```java
@Component
@PropertySource("classpath::path/file.properties")
public class SomeClass implements SomeInterface {
}
```

```java
@Value("nameValue")
@Value("${nameValueFromFile}")
@Value("${nameValueFromFile: some default}")
private Sting name;
```

### Bean Scope

```java
@Component
@Scope("prototype")
```

### Bean Lifecycle

```java
@Component
public class SomeClass implements SomeInterface {
    @PostContruct
    public void doStartUpStuff() {
    }
    @PreDestroy
    public void doCleanUpStuff() {
    }
}
```

<br>

## 3. Java Based Configuration

```java
// 1. Create a Java class and annotate as  @configuration
@Configuration
// 2. Add component scanning support
@ComponentsScan("packageName")
public class SomeConfig {
}
```

```java
// 2. Read Spring config from java file
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SomeConfig.class);
// 3. Retrieve bean from Spring container
SomeClass someClass = context.getBean("someClass", SomeInterface.class);
// 4. Call methods on the bean
someClass.someMethod();
// 5. Close the context
context.close();
```

### Defining Beans

```java
@Configuration
@ComponentsScan("packageName")
public class SomeConfig {
    // 1. Define method to expose bean
    @Bean
    public OtherClass otherClass() { //otherClass will be bean Id
    }
    // 2. Inject Bean dependency
    @Bean
    public SomeClass someClass() {
        return new SomeClass(otherClass());
    }
}
```

```java
// 3. Read Spring config from java file
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SomeConfig.class);
// 4. Retrieve bean from Spring container
SomeClass someClass = context.getBean("someClass", SomeInterface.class);
// 5. Call methods on the bean
someClass.otherClass();
// 6. Close the context
context.close();
```

### Injecting Values from Properties File

```java
// 1. Load properties file in spring config
@Configuration
@ComponentsScan("packageName")
@PropertySource("classpath::file.properties")
public class SomeConfig {
}
```

```java
@value("${name}")
private String name;
```
