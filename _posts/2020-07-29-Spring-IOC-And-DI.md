Spring IOC and DI
### 1. Coupling in code
In Java Web, we may need different service or DAO objects in different places. For example, service A need DAO B, If we have the concrete implementation of B,  we may `new B()`,  which is coupling.

### 2. Factory Pattern
To reduce coupling, we can use factory pattern. Pass the name of dependency to class A to get an object B. In this case, we only need a string in class A, and also the interface of B, to write method, but for now we don't have to have the implementation of B, there is no compiling dependency.

### 3. Container
Factory may provide singleton bean objects, every time somewhere needs a bean, factory give the bean that already there, so factory needs a container to store them: `Map<String, Object>`.

### 4. IOC
Inverse of Control, before we use factory pattern, A has the control of creating concrete object of B, by using factory pattern, A only know the name of the dependency, don't know which concrete object will be created by the factory. The factory play the role of framework, it controls the creation of concrete objects, so we call it IOC.

### 5. Spring Container
- Top interface level of Spring container: public interface BeanFactory
- Commonly used container interface: public interface ApplicationContext
> Difference of two container: 
> - BeanFactory is the super interface of  ApplicationContext
> - BeanFactory is bean multition pattern, ApplicationContext is bean singleton pattern.
> - BeanFactory create beans when they needed, destroy them when there is no reference. ApplicationContext create all beans when the container is created, destroy them when the container is destroyed.

Mostly, we need ApplicationContext interface.
`ApplicationContext container = new ClassPathXmlApplicationContext();` We have create a container(factory) explicitly, then we can call `container.getBean("name")` to get the dependency. **But, in spring, we don't have to use it explicitly, we define beans' dependencies in xml file or annotation(Autowire).**

There are mainly three implementations of ApplicationContext interface:
- public class ClassPathXmlApplicationContext: Based on xml configuration, can be used to load xml file under class path(Under java or resource folder)
- public class FileSystemXmlApplicationContext: Baded on xml configuration, can be used to load xml file in any absolute path of the machine(need access permission). This is not often used, because absolute path changes in different machine.
- AnnotationConfigApplicationContext: Based on Annotation.

Hierarchy of container:
![alt](https://i.imgur.com/tXcMsMe.png)
![alt](https://i.imgur.com/4Cb2Kf1.png)

### 6. Spring xml configuration
Location of xml: Under resource folder, create `ApplicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```
bean id: the key of the Map(container)
bean class: The concrete class of bean, factory will use it and reflection to create bean object.

### 7. Beans instantiation

Spring provide configuration class to produce beans, but here I'm going to introduce the way of instantiating beans in xml file.
In xml file, there are three ways of instantiating beans :
> 1. Default

`<bean id="accountService" class="ca.concordia.service.impl.AccountServiceImpl"/>`
If we don't specify factory-method in bean tag, it will call non-parameter constructor of bean class.

> 2. Write a static method to crate bean, specify factory-method in bean tag.

`<bean id="accountService" class="ca.concordia.factory.StaticFactory" factory-method="createAccountService"></bean>`
```java
public class StaticFactory { 
    public static IAccountService createAccountService(){ 
        return new AccountServiceImpl(); 
        } 
    }
```
> 3. Instead of static method, we can use normal method, but treat the class that the method belong to as a bean.
```xml
`<bean id="instancFactory" class="ca.concordia.factory.InstanceFactory"></bean>` 
` <bean id="accountService" factory-bean="instancFactory" factory-method="createAccountService"></bean>`
```

### 8. Dependency Injection(DI)
The dependencies are data member of anywhere needed(composition instead of 'new' objects), so dependency injection refers to data member injection.
We use xml file or annotation(Autowire) to inject dependencies. Here introducing xml configuration dependency injection:
> 1. Constructor Injection

```xml
<bean id="accountService" class="ca.concordia.service.impl.AccountServiceImpl"> 
    <constructor-arg name="name" value="张三"></constructor-arg> 
    <constructor-arg name="age" value="18"></constructor-arg> 
    <constructor-arg name="birthday" ref="now"></constructor-arg> 
</bean> 
<bean id="now" class="java.util.Date"></bean>
```
Note that birthday is referencing another bean(dependency).

> 2. Use set() method inject. (dependencies must have a setter)

```xml
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"> 
        <property name="name" value="test"></property> 
        <property name="age" value="21"></property> 
        <property name="birthday" ref="now"></property> 
</bean> 
<bean id="now" class="java.util.Date"></bean>
```

> 3. p Name space injection
