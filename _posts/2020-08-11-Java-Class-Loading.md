
#### 1 Class Lifecycle
Class loading mechanism includes Loading, Verification, Preparation, Resolution, Initialization, Using, and Unloading:

![alt](https://kgrppa.ch.files.1drv.com/y4mzgAryamdqU_v5y5AUIf8Gsbni3tnnAL-UBHtoLwZdFt2gmEF5_jjtB1mt9s6_CHTDAJYKaRoJq00o560WEGh0oUYiukGbgVCDSh3CjY_Z48qEDrwNPW_0Onj41TJyQEjj-s3F2FAwhXw8s6X1qFnNKjOHjXFCQxl2URlsZpRTJLP2viz3wfuA4VSr1UsYhx0R0M4NG6M_GDKQ4gfHkjTjQ?width=627&height=223&cropmode=none)

Loading:
1. Get binary stream from fully qualified className.
2. Create a class object to represent this class.

Verification: Check bytes

Preparation: Allocate memory to static fields and give initial value (int 0).

Resolution: symbol reference to direct reference.(need more research)

Initialization: 

#### 2. Initialization

**When to initiate?**

Note here is not instantiating.

- before creating an object
- invoke class's static method
- using class' static field (not final)
- reflection method invoking
- initiate a subclass of it
- When jvm starts, the class that main() reside in.

**What to do for initiating?**

Run "static" code, give static fields first value.

If there is "static" code in a class, there will be a `<clinit>()` method, initiation is the execution of this method.



#### 3. Loading
**When to load?**
.java files are compiled to .class before to run.
But JVM only load the .class file when the program need it.
There might be overhead to load .class at runtime, but there are more benefits:
- Polymorphism is based on dynamic loading(Need more research)
- Dynamic proxy is based on dynamic loading
- Hot deploying

**Class Loaders in Java**

Class loader: The code that get the binary stream of target class by fully qualified className. **Class loaders are independent from JVM.**

**How many kinds of class loader?**

![alt](https://larppa.ch.files.1drv.com/y4m2B6MKDRjUdqNqjEQgXPAMEDHO2XOqZjD68kNMniXoxoY7FBhCh3Cx7HbxYsl_1p_1Uz6tm7KjBcekNADr9qAADrXzOGdj6gE3P5xcgnz45zUYXB1Gv4U3qn8ZPrhmsADLn241NmLc3O1zhTZpdkpZr11rBn_k_ZQAZ0Sds-tJHFYMp6aOoDy5Jf0Tgmb8Q6Sj9Z-PJOyerUCK2EP0Jp4GQ?width=334&height=406&cropmode=none)

- BootstrapClassLoader: Used to load classes in <JAVA_HOME>/jre/lib, it's implemented by C++
- ExtClassLoader: Used to load classes in <JAVA_HOME>/jre/lib/ext and java.ext.dirs
- AppClassLoader: Used to load classes in ClassPath, including c lasses of dependencies.
- User defined class loader: Customized class loader, extends AppClassLoader，override findClass() method.

The steps that JVM starts and create class loaders:
- we run `java **.class` 
- operating system find java program, 
- java executable file will find JRE program and jvm.dll
-  initiate JVM. 
-  After initiating JVM, the first class loader is created: Bootstrap Loader, written by C++
-  Bootstrap load ExtClassLoader that inside of Launcher.class.
-  Setting ExtClassLoader's parent to `null`, null means it's BootstrapClassLoader
-  Bootstrap load AppClassLoader.class that inside of Launcher.class.
-  Setting ExtClassLoader's parent to `AppClassLoader`

AppClassLoader and ExtClassLoader are static class inside of Launcher.class.

**Note**: Launcher\$ExtClassLoader.class and Launcher\$AppClassLoader.class are all loaded by Bootstrap Loader, but the `parent` of AppClassLoader is not BootstrapClassLoader.

Launcher.java constructor:
```java
public Launcher() {
        Launcher.ExtClassLoader var1;
        try {
            //加载扩展类类加载器
            var1 = Launcher.ExtClassLoader.getExtClassLoader();
        } catch (IOException var10) {
            throw new InternalError("Could not create extension class loader", var10);
        }

        try {
            //加载应用程序类加载器，并设置parent为extClassLoader
            this.loader = Launcher.AppClassLoader.getAppClassLoader(var1);
        } catch (IOException var9) {
            throw new InternalError("Could not create application class loader", var9);
        }
        //设置默认的线程上下文类加载器为AppClassLoader
        Thread.currentThread().setContextClassLoader(this.loader);
        //此处删除无关代码。。。
        }
```

**Why we need three kinds of class loaders?**

For security, because `<JAVA_HOME>/jre/lib`, `<JAVA_HOME>/jre/lib/ext` and `classPath` have different level of security requirement, we would have separate class loader for them. By the way before java 1.2, there is only BootstrapClassLoader.

**Why we need user class loader?**
- Except `<JAVA_HOME>/jre/lib`, `<JAVA_HOME>/jre/lib/ext` and `classPath`, there might be other kinds of class source, eg. Classes bytes from database, cloud, etc.
- Encryption: If you encrypt binary class file after complication, to prevent from de-compilation, then you need a customized class loader.



**Parent Delegation**


After Launcher starts main(), the classes will be loaded into JVM at runtime.
If there is no explicitly class loading, the default **initial class loader** is AppClassLoader.
Here comes **Parent delegation**: 
- AppClassLoader first will give the fully qualified className to ExtClassLoader, 
- ExtClassLoader will give it to BootstrapClassLoader, 
- if BootstrapClassLoader cannot find the class in <JAVA_HOME>/jre/lib, it return back to ExtClassLoader, 
- then ExtClassLoader start finding desired class, if it cannot find, it will return back to AppClassLoader
- AppClassLoader will try to find class in ClassPath.

```java
//双亲委派模型的工作过程源码
protected synchronized Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException{
    // First, check if the class has already been loaded
    Class c = findLoadedClass(name);
    if (c == null) {
        try {
            if (parent != null) {
                c = parent.loadClass(name, false);
            } else {
                c = findBootstrapClassOrNull(name);
            }
        } 
        catch (ClassNotFoundException e) {
            // ClassNotFoundException thrown if class not found
            // from the non-null parent class loader
            //父类加载器无法完成类加载请求
        }
 
        if (c == null) {
            // If still not found, then invoke findClass in order to find the class
            //自己进行类加载
            c = findClass(name);
        }
    }
 
    if (resolve) {
        //判断是否需要链接过程，参数传入
        resolveClass(c);
    }
 
    return c;
}
```



**Why we need parent delegation?**
- First of all, we do need at least three class loader, in this condition, parent delegation is to guarantee one application has the same class name space, to avoid collision.
- Parent delegation guarantee that core classes in `<JAVA_HOME>/jre/lib`, `<JAVA_HOME>/jre/lib/ext` has the higher priority, prevent colliding class being loaded.

**How String.class can be visible to Foo.class?**

Two concept:
- Initial class loader: The class loader that starts loading the desired class.
- Defined class loader: The real class loader that load the class.

Answer:
- One initial class loader means one name space, it loads one class only once.
- Even String.class is finally loaded by BootstrapClassLoader, BootstrapClassLoader is the defined class loader, but String.class and Foo.class' initial class loader are the same, this guarantee the unique of String.class.
- Note class A(String.class) is visible to class B(MyClass.class), if they are the same initial class loader, and A's defined ClassLoader is higher or equal to B's ClassLoader. But B(MyClass.class) is not visible to A(String.class).

#### 4. User's class loader

User's customized class loader extends ClassLoader, not AppClassLoader, because AppClassLoader is a static class inside Launcher.class, so we cannot extends it, instead, we extends ClassLoader.class, it is all the Java implemented class loaders' base class, and override `findClass()` and `getClassBytes()`.

```java
public class MyClassLoader extends ClassLoader
{
    public MyClassLoader()
    {
        
    }
    
    public MyClassLoader(ClassLoader parent)
    {
        super(parent);
    }
    
    protected Class<?> findClass(String name) throws ClassNotFoundException
    {
    	File file = new File("D:/People.class");
        try{
            byte[] bytes = getClassBytes(file);
            //defineClass方法可以把二进制流字节组成的文件转换为一个java.lang.Class
            Class<?> c = this.defineClass(name, bytes, 0, bytes.length);
            return c;
        } 
        catch (Exception e)
        {
            e.printStackTrace();
        }
        
        return super.findClass(name);
    }
    
    private byte[] getClassBytes(File file) throws Exception
    {
        // 这里要读入.class的字节，因此要使用字节流
        FileInputStream fis = new FileInputStream(file);
        FileChannel fc = fis.getChannel();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        WritableByteChannel wbc = Channels.newChannel(baos);
        ByteBuffer by = ByteBuffer.allocate(1024);
        
        while (true){
            int i = fc.read(by);
            if (i == 0 || i == -1)
            break;
            by.flip();
            wbc.write(by);
            by.clear();
        }
        fis.close();
        return baos.toByteArray();
    }
}
```


An example of Code Hot Swap:

Create a Foo.java
```java
public class Foo{
    public void sayHello() {
        System.out.println("hello world! (version one)");
    }
}
```

Create a timed job:
```java
public static void main(String[] args) {
        //创建一个2s执行一次的定时任务
        new Timer().schedule(new TimerTask() {
            @Override
            public void run() {
                String swapPath = MyClassLoader.class.getResource("").getPath() + "swap/";
                String className = "com.example.Foo";

                //每次都实例化一个ClassLoader，这里传入swap路径，和需要特殊加载的类名
                MyClassLoader myClassLoader = new MyClassLoader(swapPath, Sets.newHashSet(className));
                try {
                    //使用自定义的ClassLoader加载类，并调用printVersion方法。
                    Object o = myClassLoader.loadClass(className).newInstance();
                    o.getClass().getMethod("printVersion").invoke(o);
                } catch (InstantiationException |
                        IllegalAccessException |
                        ClassNotFoundException |
                        NoSuchMethodException |
                        InvocationTargetException ignored) {
                }
            }
        }, 0,2000);
    }
```

we can change Foo.class at runtime:
```java
public class Foo{
    public void sayHello() {
        System.out.println("hello world! (version two)");
    }
}
```

Don't have to restart program, because each time, we create a new class loader, it will load Foo.class again every time.

Output:
```java
hello world! (version one)
hello world! (version one)
hello world! (version one)
hello world! (version two)
hello world! (version two)
hello world! (version two)
```

#### 5. How JDBC/Tomcat violate parent delegation?


> Whether a loader violate parent delegation, is whether firstly it give its parent to load the class. In Java, all three main class loader don't violate it, but if customized ClassLoader override **loadClass()** and do not let its parent first loadClass, that's violating prevent delegation.



**JDBC**

Actually I think JDBC doesn't violate parent delegation.

if we see normal JDBC code:
```java
Class clz = Class.forName("com.mysql.jdbc.Driver");
Driver d = (Driver) clz.newInstance();
```
The initial of `com.mysql.jdbc.Driver` is `AppClassLoader`, it will ask to its parent, and parent, BootstrapClassLoader cannot find the class, finally AppClassLoader start to findClass().

Above is following parent delegation.

And for SPI, the code will be like:
```java
Connection connection = 
DriverManager.getConnection("jdbc:mysql://xxxxxx/xxx", "xxxx", "xxxxx");
```
JDBC interface: `java.sql.Driver` is loaded by BootstrapClassLoader, it will also load DriverManager, DriverManager will find an appropriate Concrete Driver class to give `ServiceLoader.class` to load, let's see what ServiceLoader.class does:
```java
public static <S> ServiceLoader<S> loadInstalled(Class<S> var0) {
        ClassLoader var1 = ClassLoader.getSystemClassLoader();

        ClassLoader var2;
        for(var2 = null; var1 != null; var1 = var1.getParent()) {
            var2 = var1;
        }

        return load(var0, var2);
    }
```
`var1` is AppClassLoader, it will go up to BootstrapClassLoader to load class, then failed, it load concrete driver class itself. So it's still parent delegation.


**Tomcat**

Sometimes we need to have different version of the same class, we need isolate the name space of classes, so to violate parent delegation.

For Tomcat:
- Web container has its libraries, which is different from applications' library(eg. Java core)
- One Web container may have multiple wep applications, different applications rely on different version of dependencies, so we should guarantee the different versions of dependencies are isolated.
- Same version of dependencies should be able to be shared in different applications
- Tomcat should support code hot change, eg. .jsp, whenever we change jsp, don't want to restart container.

From above sakes, Tomcat cannot use parent delegation, because parent delegation is to guarantee one correct version of libraries.

Tomcat class loaders:
![alt](https://jqrppa.ch.files.1drv.com/y4mMD1jSRb9_4prvyZNQL9ePnbGOSojPSh0GGrCEIh4uvnTEGqR8i4hi0GsMh3KOhyp91izDQP7nFNI5jKxghLzLm9nJVl9DURssnE11WQixgsV-LDp0eLYOgw-8xc_SqRXIUcbw3koN009EkTJKxpHlPtTqFO_kVEiUJbJm-Alp7wAG7reQBddqtXLPWmrXj3tEQXYOYI9CT2-F81ypcan2w?width=462&height=655&cropmode=none)

- First three class loaders are the same with Java core, they are following parent delegation. 
- Tomcat has five more self defined ClassLoaders, they have override `loadClass()` method, and don't ask parent loader to load class.
- CommonClassLoader、CatalinaClassLoader、SharedClassLoader and WebappClassLoader load /common/\*、/server/\*、/shared/\*, and /WebApp/WEB-INF/\*
- WebappClassLoader and JsperLoader often have multiple instances, one web application has one WebappClassLoader, one .jsp file has one JsperLoader.

Introduce four class loaders:
- commonLoader：Tomcat's basic class loader, loads classes in `/common/*`, they are visible to Webapp and container
- catalinaLoader：not visible to webapp
- sharedLoader: visible to webapp, not visible to container
- WebappClassLoader: each web application's private class loader, only visible for current application.

> CommonClassLoader能加载的类都可以被Catalina ClassLoader和SharedClassLoader使用，从而实现了公有类库的共用，而CatalinaClassLoader和Shared ClassLoader自己能加载的类则与对方相互隔离。

> WebAppClassLoader可以使用SharedClassLoader加载到的类，但各个WebAppClassLoader实例之间相互隔离。

> 而JasperLoader的加载范围仅仅是这个JSP文件所编译出来的那一个.Class文件，它出现的目的就是为了被丢弃：当Web容器检测到JSP文件被修改时，会替换掉目前的JasperLoader的实例，并通过再建立一个新的Jsp类加载器来实现JSP文件的HotSwap功能。

> 双亲委派模型要求除了顶层的启动类加载器之外，其余的类加载器都应当由自己的父类加载器加载。

Therefore, in order to isolate container class loader, WebappClassLoaders, Tomcat violate parent delegation.