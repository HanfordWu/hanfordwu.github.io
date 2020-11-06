
First, review something:

![alt](https://www.tutorialspoint.com/java/images/exceptions1.jpg)

Above, "other exception" is checked exception, they can be detected by compiler. Runtime exception is unchecked exception.

1. What is the difference between Error and Exception?

Error often belong to JVM, like system crash, memory overflow, stack overflow, etc. The compiler cannot detect error, applications cannot catch error, once error happens, applications are stopped.

Exception can be caught and handled, then program resume. If not handled, it will be handled by default handler, and thread is stopped.

Error:
1. java.lang.Error: Base class of error.
2. java.lang.ClassCircularityError: Class circular dependent error.
3. java.lang.ClassFormatError: When JVM is trying to read a class file, and the format of the class file is incorrect, JVM throws this error.
4.  java.lang.ExceptionInInitializerError: When JVM is doing initialization of the program, executing static blocks.
5.  java.lang.IllegalAccessError
6.  ...

Exception:
1. ClassCastException(runtime exception)
2. IndexOutOfBoundsException(runtime exception)
3. NullPointerException(runtime exception)
4. ArrayStoreException(runtime exception)
5. ClassNotFoundException(compile time exception)
6. IOException(compile time exception)

-------
2. What is the difference between runtime Exception(unchecked exception) and compile-time Exception(checked exception)?

The compiler cannot detect runtime Exception, but can detect non-runtime Exception. If program has runtime Exception, that is programmer's fault for sure.

For non-runtime Exception, It is mandatory to provide try-catch or try finally block to handle checked Exception and if not to do so, it cannot be compiled. This Exception is often caused by the environment outside of program, like no file.


3. What's the difference of throw and throws?

Throw can be used inside of methods. Throws is used on methods' signature.

4. For customer's exception, it should extend runtime exception or compile time exception?

If a method is likely to fail and chances of failure is more than 50% it should throw Checked Exception to ensure alternate processing in case it failed. Otherwise use runtime exception.


Actually, we should handle all the possible exceptions, but it's impossible make all exception be checked by compiler, so compiler only alert the checked exception. For unchecked exception, the checking should be done by programmer when they think it's possible and necessary.



### How JVM handle Exception?

If there is a runtime Exception, an Exception object will be created, it contains name, description, and the state of the program when Exception happen. JVM will search any handler for the Exception, if yes, let the handler do it, if no, JVM will use default handler handle it, and stop the thread.

Now we come back to the main topic.


### Uncaught Exception and thread ceased

A method that cause an exception.
```java
public static void causeNPE() {
        String s = null;
        s.length();
}
```

sleeping method:
- we let main thread sleep for a while when other thread has exception.
- Make sure other thread can be run.
```java
public static void makeThreadSleep(long durationInMillSeconds) {
        try {
            Thread.sleep(durationInMillSeconds);
        } catch (InterruptedException e) {
            System.out.println("makeThreadSleep interrupted");
            e.printStackTrace();
        }
}
```

Information collecting method:

```java
public static void dumpAllThreadsInfo() {
        Set<Thread> threadSet = Thread.getAllStackTraces().keySet();
        for(Thread thread: threadSet) {
            System.out.println("dumpAllThreadsInfo thread.name=" + thread.getName()
                    + ";thread.state=" + thread.getState()
                    + ";thread.isAlive=" + thread.isAlive()
                    + ";group=" + thread.getThreadGroup()
            );
        }
}
```
A method to calculate time:
```java
public static String getTimeForDebug() {
        SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
        return sdf.format(new Date());
}
```

The scenario is, sub thread created un-checked exception, we check what happen in main thread.

The follow method creating a new thread, and call exception method above.
```java
private static void startErrorThread() {
    new Thread(new Runnable(){

        @Override
        public void run() {
            System.out.println("startErrorThread currentThread.name=" + Thread.currentThread().getName()
            + "; happened at " + Utils.getTimeForDebug());
            Utils.causeNPE();
        }
    }).start();
    Utils.makeThreadSleep(10 * 1000);
    System.out.println("Thread main sleepFinished at " + Utils.getTimeForDebug());
    Utils.dumpAllThreadsInfo();
}
```

Output:
```java
//异常发生 输出线程名称和发生异常的时间
startErrorThread currentThread.name=Thread-0; happened at 16:59:04
//异常崩溃的信息
Exception in thread "Thread-0" java.lang.NullPointerException
  at Utils.causeNPE(Utils.java:35)
  at Main$3.run(Main.java:115)
  at java.lang.Thread.run(Thread.java:748)
//主线程睡眠结束(对比时间，确定差为10秒)    
Thread main sleepFinished at 16:59:14
//主线程不受影响，继续执行操作
dumpAllThreadsInfo thread.name=Attach Listener;thread.state=RUNNABLE;thread.isAlive=true;group=java.lang.ThreadGroup[name=system,maxpri=10]
dumpAllThreadsInfo thread.name=Reference Handler;thread.state=WAITING;thread.isAlive=true;group=java.lang.ThreadGroup[name=system,maxpri=10]
dumpAllThreadsInfo thread.name=Monitor Ctrl-Break;thread.state=RUNNABLE;thread.isAlive=true;group=java.lang.ThreadGroup[name=main,maxpri=10]
dumpAllThreadsInfo thread.name=Signal Dispatcher;thread.state=RUNNABLE;thread.isAlive=true;group=java.lang.ThreadGroup[name=system,maxpri=10]
dumpAllThreadsInfo thread.name=Finalizer;thread.state=WAITING;thread.isAlive=true;group=java.lang.ThreadGroup[name=system,maxpri=10]
dumpAllThreadsInfo thread.name=main;thread.state=RUNNABLE;thread.isAlive=true;group=java.lang.ThreadGroup[name=main,maxpri=10]
//进程结束
Process finished with exit code 0
```
results:
- Sub-thread (thread-0) exit because of uncaughtException.
- Main thread is not affected, after 10s sleeping, it print out thread information.

What if the uncaughtException happen in main thread?

Sub-thread is not affected by the exit of main thread.

When does JVM exit?

JVM will exit after all non-daemon thread exit. By default, all threads are non-daemon.

Regarding JVM:

> Uncaught exceptions are handled in shutdown hooks just as in any other thread, by invoking the uncaughtException method of the thread’s ThreadGroup object. The default implementation of this method prints the exception’s stack trace to System.err and terminates the thread; it does not cause the virtual machine to exit or halt.



### Multi-thread exception handling

In multi-thread program, all threads are not allowed to have un-caught checked exception. That means all thread must handle their checked exception. Because `java.lang.Runnable.run()` have no declaration of any exception. 

But threads can still throw unchecked exception. When unchecked exception is thrown, the thread ceased. This does not affect any other thread. Actually, other thread do not know the exception happens. Because for JVM, a thread is an independent code, the exceptions should be handled by threads themselves.

However, if we want to handle an unchecked exception from outside of the thread, JVM provide a callback mechanism that we can do this. This is provided by Thread class, setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler eh) method. We can set a callback: UncaughtExceptionHandler in the exception thread. UncaughtExceptionHandler has an interface: `public void uncaughtException(Thread t, Throwable e)`.

By this way, we can handle the exception in the thread.

