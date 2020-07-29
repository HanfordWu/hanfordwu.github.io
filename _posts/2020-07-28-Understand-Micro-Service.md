Micro-service:
## 1. Monolithic Application
Monolithic Application is what I learned in the beginning, even though I don't know this architecture name. In spring framework, a http request comes from browser, springMVC controller get it, call any service it needs in the same module, service call any mapper it needs in the same module, DAO get the data from database. One module only needs one machine to deployment(cluster may be different). This is **Monolithic Application**, which works well for applications those are not very complicated.

Cons: All kinds of service located in the same module, in high concurrency scenario, some service may affect other kinds of service.
More explanation: A tomcat server can only handle less than thousand concurrency, so we separate static resource from service request using Nginx. But some of the service may still be high concurrency, affecting other services.

## 2. Separate Functionalities into modules
Each functionality has entire services it needs.
Obviously, different functionality may share some same services, it's duplicate development. If one service is maintain by one team, it's gonna be hard to maintain in different module.

## 3. Distributed Services
Divide services(not functionalities) into modules, they call with each other.
Cons:
- Calls(dependencies) between modules become very complicated.
- Hard to decide which start first or later.

## 4. Service Orientated Application(SOA)
Adding a center to register, subscribe, monitor services. But modules still calls modules directly, just have to tell center their activities.

## 5. Micro-Services
Very similar to SOA, but some specifications:
- Single responsibility
- Micro: Smaller granularity than SOA. Actually SOA is trying not to separate services too small.
- Service oriented: Each service(module) provide RESTful interface(http), so the implementation or even language are independent.
- Independent deployment.


![alt](https://i.imgur.com/kyKHmHx.png)

## 6. Two ways of procedure invocation

- Remote Procedure Call(RPC): Customized data format, based on native TCP, fast, efficient.
- Http: Defined data format, being able to make use of RESTful API.

