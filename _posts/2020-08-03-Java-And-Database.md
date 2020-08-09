Introduction of how Java handle various database

## JDBC

JDBC - Java Database Connectivity. Sun's programmer come up with JDBC to unify the way of Java handling database. Each of the database implements concrete classes of JDBC interface to let JAVA programmer use(driver), for example, com.mysql.jdbc.driver or com.mysql.cj.jdbc.driver.

> Difference of two drivers: The new driver class is com.mysql.cj.jdbc.Driver’. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary occurs due to the name of the class that implements “java.sql.Driver” in MySQL Connector/J has changed from com.mysql.jdbc.Driver to com.mysql.cj.jdbc.Driver. The old class name has been deprecated.

By JDBC interfaces, no matter which database is used, MySQL, Oracle or MS SQLServer, any other relational database, we can use the same Java code by loading different drivers.

Procedure of native JDBC manipulating:
1. Load database driver class.
2. Use url, username, password to get connection: 
   `connection = DriverManager.getConnection(url, username, password)`
3. Use connection to Create statement: `stmt = connection.createStatement()`
4. Define sql clause.
5. Execute sql: `stmt.execute(sql)`, return `ResultSet` if it's a query.
6. Iterate `ResultSet`.
7. Close `ResultSet`, close `stmt`, close `connection`.

JDBC is the basis to connect database, technics like ORM, JPA are based on it.

## Problems of native JDBC:

- Every time create a new connection, and close it, which is expensive.
- Lots of duplicate code.(we can encapsulate to an util)
- Most important: Native JDBC is using database tables as view, but Java is object oriented, the transformation of table and object is done by programmer.

For the 1st problem, we have database connection pool technics

## Pooled Connection

i.e. C3P0, DBCP, Druid, HikariCP, et.

Basic idea is simple, there are more features:
- Multiplexing
- Connection detecting
- Dynamic capacity

For the 2nd and 3rd problems, we use ORM or JPA framework.

## ORM
Object-Relational Mapping.
Use object model to map database. We are trying to avoid SQL, but deal with objects' fields and methods.

i.e. Hibernate, MyBatis, etc.

Hibernate comes with the following features:

- HQL (Hibernate Query Language)− Hibernate is a lightweight framework as it does not contain additional functionalities, and it uses only those functionalities required for object-relational mapping.
- Caching
- Auto-Generation− Hibernate provides a feature of automatic table generation. It means a programmer need not worry about the query implementation, i.e., Hibernate does on its own.
- Lazy Loading

MyBatis comes with the following features:

- Inline SQL − No pre-compiler is needed, and you can have full access to all of the features of SQL.
- Dynamic SQL − MyBatis provides features for dynamic building SQL queries based on parameters.

### Difference between them:
> Hibernate is an ORM, meaning (at its most basic level) it maps instances of java objects to actual rows in a database table. Generally, for pojo's retrieved via Hibernate: any manipulations and modifications to these pojo's will appear in the database. Hibernate will generate and execute the relevant SQL at an appropriate time.

>Mybatis (at its most basic level) is simply a tool for piecing together and executing SQL that is stored in xml files. It does not map instances of Java objects to rows in a database table, rather it maps Java methods to SQL statements, and therefore it is not an ORM. It can also return pojo's of course, but they are not tied to any kind of a persistence context.

## JPA
Java Persistence API, aim to provides simpler programming model. Because of different kinds of ORM frameworks, SUN wants to unify the standard of ORM, so JAP is came up, to unify the way of using different ORM frameworks.

i.e. Hibernate JPA, Spring, OpenJPA.