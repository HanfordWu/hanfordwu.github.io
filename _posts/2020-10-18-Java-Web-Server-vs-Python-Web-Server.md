Recently I am getting to know Django, I like to understand them together.

### Python WSGI

WSGI: Http server Gateway Interface

WSGI is a standard, it is the interface of Python frameworks and Http server. WSGI has no official implementation(it's standard, or protocol). If application follows WSGI and Http server follows WSGI, applications can run on all kinds of servers.

Without WSGI, our application can only run particular server, we are restricted.

With WSGI, we can choose any frameworks with any Http server. For example, you can select servers like Gunicorn, Nginx(uWSGI), or Waitress, to run Django, Flask, or Pyramid applications.


### Java Servlet API

Similar to WSGI, Java Servlet API define the interface between Http server and application.

Without Java Servlet API, SpringMVC will implement something like "SpringMVCHttpRequest" and "SpringMVCHttpResponse", using JAVA socket programming, Struts will have another implementation, they can only use their implementation. On the other hand, if Tomcat only support SpringMVC, we cannot use Tomcat + Struts2.

With Java Servlet API, we can have Tomcat + SpringMVC, or Tomcat + Struts2, or Jetty + SpringMVC, and Jetty + Struts2.


### More about Python WSGI

Http server is responsible for client request accept, management, 
Application is responsible for business logic.

For the sake of development simplification, we encapsulate many functionalities as A FRAMEWORK, like Django, Flask, Tornado. 

Http server receive requests, then call correct handler, going into correct logic.

Web applications are actually writing a WSGI function, being called by Http server, give respond to client, change the web content.


### Python Http server

- We can write a Http server using python socket.
- Open source python http servers: uWSGI, wsgiref, Mod_WSGI. If we are going to use Nginx, Apache, Lighttpd, we need to install WSGI module to support python application.
- When we are developing, we can use embed server of frameworks(Flask, Django have inbuilt http server)


### Python standard library's implementation of WSGI

wsgiref is the implementation of WSGI in Python core. simple_server module is a simple Http server.

simple_server receive request, hands over to RequestHandlerClass, then turn back to client.

### uWSGI

It's a Http server, it supports WSGI, uwsgi, http.

### Django in-built WSGI http server

The WSGIServer extends wsgiref.simple_server.WSGIServer, WSGIRequestHandler extends wsgiref.simple_server.WSGIRequestHandler

Note, Nginx is a reverse proxy server, it's delegating server, distribute http request, return files. It doesn't support any http server protocol, so we need tomcat for java application or uWSGI for python application.