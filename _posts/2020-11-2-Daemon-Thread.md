### Daemon Thread and User Thread

- User thread: High priority, if there is a user thread running, JVM will keep running.
- Daemon thread: Low priority, if there is no user thread running, JVM will exit, even though there still daemon thread is running.


Daemon thread serve for user thread, so if there is no user thread, daemon thread will also be useless. Once all user threads done, JVM will exit, so daemon threads will be killed.

Note:

- It's not allowed to do IO task in daemon thread, because daemon thread cannot control its exiting, there will be problems if exit suddenly.
- There will be no problem if infinite loop exit in daemon thread.
- Daemon thread can do `Thread.join()` to prevent the exit of JVM.


### What we do in Daemon thread?

We use daemon thread to do background support, like garbage collecting, deleting un-used cache, etc. It's all about serving for user thread.

In this sense, most JVM threads are daemon thread.


### How to create Daemon thread?

Create a thread, **before** start it, do `thread.setDaemon(true)`.

```java
NewThread daemonThread = new NewThread();
daemonThread.setDaemon(true);
daemonThread.start();
```
In Java, the Daemon status is "inherited" by default. Threads inherit the daemon status from the thread that created them.


How to check the daemon status of a thread?

`isDaemon()`

```java
@Test
public void whenCallIsDaemon_thenCorrect() {
    NewThread daemonThread = new NewThread();
    NewThread userThread = new NewThread();
    daemonThread.setDaemon(true);
    daemonThread.start();
    userThread.start();

    assertTrue(daemonThread.isDaemon());
    assertFalse(userThread.isDaemon());
}
```