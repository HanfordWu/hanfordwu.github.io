In Java concurrency, if an object is shared by multiple threads, and this object has its attributes, then we say the object is not thread safe.

If the attribute is primary type, we can add `volatile` to make it visible to other thread, but if the attribute is a reference, we `volatile` cannot guarantee the safety, because `volatile` only guarantee the change if pointer to be visible, not the referred object.
So the referred object is still unsafe.

There is a situation, multiple threads are working on object A, A has an reference attribute B, threads need the same object B, and same initial value of B, which means the usage of B is independent for different threads. In this case, just like primary type attributes, we can have a copy of B in heap for each thread.

**Why we need the same object B?**
For example, in database transaction service, we need manipulate two DAOs, and we want them to share the same connection, so we can put the connection into ThreadLocal, DAOs fetch from ThreadLocal, so that they are using the same connection, so that we can do rollback for two DAOs. 
Another benefit is if one DAO close the connection, the Other DAO's connection won't be closed.

This is where `ThreadLocal.class` should be used.

```java
public final class ConnectionUtil {

    private ConnectionUtil() {}

    private static final ThreadLocal<Connection> conn = new ThreadLocal<>();

    public static Connection getConn() {
        Connection con = conn.get();
        if (con == null) {
            try {
                Class.forName("com.mysql.jdbc.Driver");
                con = DriverManager.getConnection("url", "userName", "password");
                conn.set(con);
            } catch (ClassNotFoundException | SQLException e) {
                // ...
            }
        }
        return con;
    }

}
```
