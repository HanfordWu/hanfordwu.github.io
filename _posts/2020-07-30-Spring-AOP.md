AOP: Aspect Oriented Programming

### Dynamic Proxy

First, let's talk about Proxy pattern and decorator pattern:
**Decorator pattern:** A is decorated by decorator B, then A is still A, but become stronger.
**Proxy Pattern:** A is original object, B is another object and is a proxy of A, B strength the functionalities of A.

Cons of Static proxy:
- Proxy B must implement A's interface, if we have lots of A to be proxy, B has to implements many many interfaces, or, we can create proxy C,D,E,F..., Neither is a good way.
- When A's interface needs change, we have to change many B's places.

If we already have class A, we want add some operations to A's function, but don't want to change A, we can use dynamic proxy. For instance: logging, interceptor, etc.

**Dynamic Proxy:**
B strengthen functionalities of A **at run time**.

Making use of JVM class loading mechanism, generate proxy B's class code, for an interface(A's interface) or an object(A's object).


Two ways of dynamic proxy:
- Based on interface: Require A is implementing an interface.
- Based on subclass: Require A is not a 'final' class.
```java
A a = new A();

InterfaceA proxyB = (InterfaceA) Proxy.newProxyInstance(a.getclass().getClassLoader(), a.getclass().getInterfaces(), new InvocationHandler(){
    @Override
    public object invoke(Object proxy, Method method, Object[] args) {

        try{
            //Before-advices goes here
            Object result = method.invoke(a, args)
            //Post-advices goes here

            return result;
        }catch(){
            //error-advice goes here
        }finally{
            //after-advice goes here
        }      
    }
})

//Now we have the proxy B object, it has all the strengthened method.
```
The second way: based on subclass, omit it here.

### Spring AOP
Glossaries:
> Joinpoint: 

All those methods of A.
> Pointcut: 

The methods that been strengthened.

> Advice:

Those code that added before/after/error/finally A's method being called.

> Aspect:

Pointcut combine with Advice

***Why AOP?***
There are some public code that we want to be executed in all method(logging, interceptor, mapperDAO-only sql different), if we write those public code in every method, it's gonna hard to maintain.
Therefore, by AOP, we make public code to be "advices", added into Pointcut that need those advices.

*Spring decide which way of dynamic proxy based on class A's implementation*

### XML AOP configuration
```xml
<?xml version="1.0" encoding="UTF-8"?> 
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/
XMLSchema-instance" 
xmlns:aop="http://www.springframework.org/schema/aop" 
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> 

<!-- First we crate advice bean, in this bean class, we define all advice code(methods) -->
<bean id="txManager" class="ca.concordia.utils.TransactionManager"> <property name="dbAssit" ref="dbAssit"></property> </bean>

<aop:aspect id="txAdvice" ref="txManager"> 
<!--配置通知的类型要写在此处--> 
<aop:pointcut expression="execution( public void ca.concordia.service.impl.AccountServiceImpl.transfer( java.lang.String, java.lang.String, java.lang.Float) )" id="pt1"/>
</aop:aspect>
<aop:before method="beginTransaction" pointcut-ref="pt1"/>
<aop:after-returning method="commit" pointcut-ref="pt1"/>
<aop:after-throwing method="rollback" pointcut-ref="pt1"/>
<aop:after method="release" pointcut-ref="pt1"/>

</beans>
```

Around Advice:
```xml
<aop:config> 
<aop:pointcut expression="execution(* ca.concordia.service.impl.*.*(..))" id="pt1"/> 
<aop:aspect id="txAdvice" ref="txManager"> 
<!-- 配置环绕通知 --> 
<aop:around method="transactionAround" pointcut-ref="pt1"/> 
</aop:aspect> 
</aop:config>
```

### Annotation AOP configuration

At spring configuration class add `@EnableAspectJAutoProxy`
```java
@Configuration 
@ComponentScan(basePackages="ca.concordia") 
@EnableAspectJAutoProxy 
public class SpringConfiguration { }
```

At Advice class add `@component` and  `@Aspect`

Before advice methods add `@before(“pointcut expression")`
Post advice methods add `@AfterReturning(“pointcut expression")`
Error advice methods add `@AfterThrowing(“pointcut expression")`
After advice methods add `@After(“pointcut expression")`

