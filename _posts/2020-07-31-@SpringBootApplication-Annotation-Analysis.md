### Three ways of starting a SpringBoot application
1. 
```java
@SpringBootApplication
public class FistSpringApplication {

    public static void main(String[] args) {
        SpringApplication.run(FistSpringApplication.class, args);
    }
}
```
2.
```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication springApplication = new SpringApplication(MyApplication.class);
        //Here we can set parameters, like web port.
        Map<String, Object> properties = new LinkedHashMap<>();
        //Here 0 means using random port.
        properties.put("server.port", 0);
        springApplication.setDefaultProperties(properties);
        springApplication.run(args);
    }
}
```
3. Using a builder
```java
@SpringBootApplication
public class FistSpringApplication {

    public static void main(String[] args) {
       new SpringApplicationBuilder(FistSpringApplication.class)
                .properties("server.port=80")
                .run(args);
    }
}
```

What's inside of `run()`:
![alt](https://upload-images.jianshu.io/upload_images/14890912-9033992b92f41435?imageMogr2/auto-orient/strip|imageView2/2/w/787/format/webp)

The method create a container(AnnotationConfigServletWebServerApplicationContext), register it(refresh(context)).

What does `run()` do?
- Load all class inside `META-INF/spring.factories` of dependencies. (@EnableAutoConfiguration)
- Load spring listener
- Deduce the type of the container, initiate container. (E.g. If dependencies include spring-boot-starter-web, it will start web container)
- Scan, load, register components
- Return container



This container manages all the applications that is running:
```java
AnnotationConfigServletWebServerApplicationContext context = new AnnotationConfigServletWebServerApplicationContext();
context.register(MyApplication.class);
context.refresh()
```
After above code, a tomcat container is running.

What's inside of `@SpringBootApplication`?
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```

> `@SpringBootApplication`

It is a sub-interface of `@Configuration`, so, entry class is a spring configuration class.

> `@EnableAutoConfiguration`

This annotation will activate spring spi, read all components in dependencies. Without it, Spring boot only know components inside the @ComponentScan package's components, don't know components inside of those starters.
All starters must have `META-INF/spring.factories`file, specify it's configuration class that hope to be scanned.

> `@ComponentScan`
Specify user's components package, default is the package that entry class reside in. Equivalent to `<context:component-scan base-package="" />`


Recall: 
Tomcat server first find WEB-INF/web.xml, create dispatcher servlet according to web.xml. In context-param, define the position of spring configuration file, so that tomcat can find spring beans' configuration, to create spring container.


```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>
        /someLocation/ApplicationContext.xml
    </param-value>
</context-param>
```
So we have servlet container(tomcat), spring container, springMVC container, 

Division of labor: SpringMVC container manage web components, e.g. @Controller, Spring container manage @Service and DAO

springMVC container is the sub-container of spring container. SpringMVC components can be injected with spring components, but Spring components cannot be injected with springMVC components.


