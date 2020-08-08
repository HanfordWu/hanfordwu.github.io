Spring Web MVC framework is designed around a DispatcherServlet that handles all the HTTP request and responses. The workflow of Spring MVC is showing here:

![alt](https://github.com/HanfordWu/hanfordwu.github.io/blob/master/static/img/spring_dispatcherservlet.png?raw=true)

Following is the sequence of a request to DispatcherServlet:
- Tomcat starts, find web.xml configuration, initiate DispatcherServlet. Often it maps to all url pattern: `/`
- Tomcat receiving a HTTP request, map to DispatcherServlet.
- DispatcherServlet consult HandlerMapping(A bean configured in springMVC xml) to call appropriate Controller.
- The controller take the request and calls the appropriate service methods.
- The DispatcherServlet will take help from ViewResolver to pickup the defined view for the request.
- Once the view is determined, DispatcherServlet pass the model data to view and rendered.

All the mentioned components, i.e. HandlerMapping, Controller, ViewResolver are parts of WebApplicationContext, which is an extension of plain ApplicationContext with some extra necessary for web applications.
![alt](https://github.com/HanfordWu/hanfordwu.github.io/blob/master/static/img/mvc-contexts.gif?raw=true)

The configuration of those components can be done in springMVC.xml. But we need to tell DispatcherServlet where is it. 

At the same time, we need specify the location of applicationContext.xml, to let Tomcat load the container of spring.

Note that springMVC.xml only configure component scan of controller.
applicationContext.xml only configure component scan of services.

The whole web.xml will be:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
            http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

  <display-name>Archetype Created Web Application</display-name>
<!--  前端控制器  -->

  <servlet>
    <servlet-name>survey</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!--    服务器启动的时候加载springMVC配置文件 -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
<!--  配置前端控制器拦截路径, 这里不要配置/*， 因为/*把静态的东西也给拦截了-->
  <servlet-mapping>
    <servlet-name>survey</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

<!--  通过上下文参数配置spring核心配置文件-->

  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>

<!--  通过一个监听器来加载spring核心配置文件，实现bean的创建-->

  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>


<!--  配置一个过滤器，实现编码控制  -->
  <filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>utf-8</param-value>
    </init-param>
  </filter>

<!--  配置过滤器的mapping, 过滤器可以拦截所有   -->
  <filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```
