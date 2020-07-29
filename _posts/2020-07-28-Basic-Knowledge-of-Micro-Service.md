## Monolithic Application
Monolithic Application is what I learned in the beginning, even though I don't know this architecture name. 

In spring framework, a http request comes from browser, springMVC controller get it, call any service it needs in the same module, service call any mapper it needs in the same module, DAO get the data from database. One module only needs one machine to deployment(cluster may be different). This is **Monolithic Application**, which works well for applications those are not very complicated.

**Cons:** 
All kinds of service located in the same module, in high concurrency scenario, some service may affect other kinds of service.

A tomcat server can only handle less than thousand concurrency, so we separate static resource from service request using Nginx. But some of the service may still be high concurrency, affecting other services.

## Separate Functionalities into modules
Each functionality has entire services it needs.
Obviously, different functionality may share some same services, it's duplicate development. If one service is maintain by one team, it's gonna be hard to maintain in different module.

## Distributed Services
Divide services(not functionalities) into modules, they call with each other.

**Cons:**
- Calls(dependencies) between modules become very complicated.
- Hard to decide which start first or later.

## Service Orientated Application(SOA)
Adding a center to register, subscribe, monitor services. But modules still calls modules directly, just have to tell center their activities.

## Micro-Services

![alt](https://i.imgur.com/kyKHmHx.png)

Very similar to SOA, but some specifications:
- Single responsibility
- Micro: Smaller granularity than SOA. Actually SOA is trying not to separate services too small.
- Service oriented: Each service(module) provide RESTful interface(http), so the implementation or even language are independent.
- Independent deployment.
- Fault tolerance: One failure only affect one service, we may also have backups.
- High extensible

**Dubbo architecture:**
![alt](https://upload-images.jianshu.io/upload_images/1765294-a09997cd3fa9e449.png?imageMogr2/auto-orient/strip|imageView2/2/w/701/format/webp)

- Service provider
- Consumer
- Registry
- Monitor
- Container

**SpringCloud:**
![alt](https://upload-images.jianshu.io/upload_images/1765294-1a0931e21e14a817.png?imageMogr2/auto-orient/strip|imageView2/2/w/798/format/webp)

## Two ways of procedure invocation
- Remote Procedure Call(RPC): Customized data format, based on native TCP, fast, efficient.
- Http: Defined data format, being able to make use of RESTful API.

Service A need Service B to do some job, Service A get the information from Registry, call API of service B, B return result to A.

## Frameworks
| Functionalities       | Dubbo            | SpringCloud                                                   |
|----------------|------------------|---------------------------------------------------------------|
| Register Center   | Zookeeper、Redis | Netflix Eureka                                                |
| Invocation   | RPC              | Rest API                                                      |
| Gateway       | null             | Netflix Zuul                                                  |
| Breaker | null         | Netflix Hystrix                                               |
| Configuration Center       | null             | Spring Cloud Config                                           |
| Invocation tracer     | null             | Spring Cloud Sleuth                                           |
| Message Bus       | null             | Spring Cloud Bus                                              |
| Data Flow         | null             | Spring Cloud Stream encapsulate Redis,Rabbit、Kafka to send or receive message |
| Batch  Task     | null             | Spring Cloud Task                                             |