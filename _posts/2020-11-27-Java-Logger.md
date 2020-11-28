There are ton of information when the system is running.

Some information we want to know, some we don't.

What we want to know usually? Depends on what kind of system, in general:

- User's behavior (info)
- Software System got exception(error)
- Software System crash (fatal)
- Developer's debug information (debug)

They have different level and different target people. They also should have different output place and format. For example, info, error, fatal should go to file, debug information should go to console. So we cannot use "System.out.println("")" or "System.err.println("")" for all of them. And, the file should be separate according to time and size.



Above needs are common for all developer, so we have logging framework.

Maybe we only need to one framework, it implements some classes and let us to use, something like:
```java
Logger logger = new Logger();
logger.error("We got an error")
```
It's better it provides some configuration to let us customize the format, the location of file, the size, etc.

This is log4j.

After log4j, sun implemented its own logging framework, JUL(Java Util Logging), similar to log4j.

Then log4j become one of apache project. Apache publish an interface of logging system based on log4j. The interface is JCL(Jakarta Commons Logging), just like JDBC, every implementation that follows JCL can be used in every user's application that is using JCL interface.

JUL and Log4j follows JCL. JCL is the standard, JUL and Log4j are implementations.

But JUL is not good enough.

The author of Log4j, created another interface(standard) for logging system, Slf4j(Simple Logging Facade for Java). It's easier for the developers to use.

But at the beginning, there no implementation for Slf4j, developers cannot use it. Therefore, the author, Ceki Gülcü, write "adapter"s(slf4j-jdk 14, slf4j-log4j 12), those adapters are borrowing the implementation of JCL, adapters translate the configuration and logs to log4j and JUL, let them to output. 

Slf4j is like a facade, the developers are using Slf4j interface to write code, and the logs are transmitted to JUL or log4j to output by 

So, in our project, we can use the dependencies: Slf4j + slf4j-log4j 12 + log4j, or, Slf4j + slf4j-jdk 14 + JUL.

The code we write:

```java
public void testSlf4j() {
    Logger logger = LoggerFactory.getLogger(Object.class);
    logger.error("123");
}
```

But, if we are using some framework, e.g. Spring, it's using JCL inside of the framework, and our code is using Slf4j + ..., the log will have two logging system, they may have different format or produce different files.

For this case, Ceki Gülcü write another adapter, it can translate existing logging code to Slf4j logging system, so that developer's log and Spring's log go to the same file with same format.


Next, Ceki Gülcü write a real implementation of Slf4j: Logback. Logback is better than log4j and JUL.


Apache does not stop, it introduce a new logging system, Log4j2, it's not implementing JCL, so it can not be compatible with log4j.

Log4j2 has its own API: log4j-api and implementation: log4j-core.

There is also adapters to integrated with Slf4j and JCL. 

Log4j2 or Logback, it's up to our preference.