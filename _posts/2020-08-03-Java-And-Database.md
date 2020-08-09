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

JDBC is the basis to connect database