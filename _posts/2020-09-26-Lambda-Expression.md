### Introduction
Java Lambda expression can be used to simplify the expression of functional interface's anonymous implementation, but not only this. This post I want to summarize related content of lambda expression.

### Lambda and Anonymous Classes

New a thread in jdk7:
```java
// JDK7 匿名内部类写法
new Thread(new Runnable(){// 接口名
	@Override
	public void run(){// 方法名
		System.out.println("Thread run()");
	}
}).start();
```
Runnable is a functional interface, so
Lambda expression:
```java
// JDK8 Lambda表达式写法
new Thread( () -> System.out.println("Thread run()") ).start();
```
Example 2: User defined Comparator to compare the length of String.
```java
// JDK7 匿名内部类写法
List<String> list = Arrays.asList("I", "love", "you", "too");
Collections.sort(list, new Comparator<String>(){// 接口名
    @Override
    public int compare(String s1, String s2){// 方法名
        if(s1 == null)
            return -1;
        if(s2 == null)
            return 1;
        return s1.length()-s2.length();
    }
});
```
Comparator is a functional interface, so Lambda expression:
```java
// JDK8 Lambda表达式写法
List<String> list = Arrays.asList("I", "love", "you", "too");
Collections.sort(list, (s1, s2) ->{// 省略参数表的类型
    if(s1 == null)
        return -1;
    if(s2 == null)
        return 1;
    return s1.length()-s2.length();
});
```
The complier will be able to infer the type of s1 and s2, so we don't have to specify the type.

So two neccessary points:
-  Funtional interface
-  Type inference

More example of Lambda Expression:
```java
// Lambda表达式的书写形式
Runnable run = () -> System.out.println("Hello World");//1 non-parameter
ActionListener listener = event -> System.out.println("button clicked");//2 with parameter
Runnable multiLine = () -> {// 3 code block
    System.out.print("Hello");
    System.out.println(" Hoolee");
};
BinaryOperator<Long> add = (Long x, Long y) -> x + y;// 4 same with 5
BinaryOperator<Long> addImplicit = (x, y) -> x + y;// 5 type inference
```

### User Defined Funtional Interface

```java
// 自定义函数接口
@FunctionalInterface
public interface ConsumerInterface<T>{
	void accept(T t);
}
```
So we can write:

`ConsumerInterface<String> consumer = str -> System.out.println(str);`

Inside of one class, we can utilize the functional interface we wrote:

```java
class MyStream<T>{
	private List<T> list;
    ...
	public void myForEach(ConsumerInterface<T> consumer){// 1
		for(T t : list){
			consumer.accept(t);
		}
	}
}
MyStream<String> stream = new MyStream<String>();
stream.myForEach(str -> System.out.println(str));// 使用自定义函数接口书写Lambda表达式
```

Above simulates stream API, so that we can do something to each element of the stream.

### Pre-defined Functional Interface

Java SE privide many functional interface. Here are four basic types:
- Supplier<T>: No input, return result of type T
- Consummer<T>: Input type of T, no output
- Functional<T, R>: Input type of T, return result of type R
- Predicate<T>: Input type of T, return boolean

If a function has many input parameters, there is no pre-defined functional interface that we can use, then we have to define our user defined functional interface, so that we can have as many parameters as we want.


Examples of pre-defined functional interface:

```java

/**
     * 接口：interface Supplier<T> 提供者
     * 定义一个Lambda表达式，无输入，生产(返回)一个T类型的值
     */
    @Test
    public void example1(){
        //返回Integer类型的值
        Supplier<Integer> supplier = () -> 10;
        System.out.println(supplier.get()); //打印：10
 
        //返回异常类的对象
        Supplier<Exception> exceptionSupplier = () -> new Exception("异常");
        System.out.println(exceptionSupplier.get().getMessage()); //打印：异常
    }
 
    /**
     * 接口：interface Consumer<T> 消费者
     * 定义一个Lambda表达式，消费(输入)一个T类型的值，无返回
     */
    @Test
    public void example2(){
        //accept 接收参数并调用接口的方法
        Consumer<Integer> consumer = x -> System.out.println(x);
        consumer.accept(15); //打印：15
 
        //andThen 先调用原Consumer，再调用andThen方法中指定的Consumer
        Consumer<Integer> secondConsumer = y -> System.out.println(y + y);
        consumer.andThen(secondConsumer).accept(10); //打印： 10 20, 这里用了andThen接口，用于连接两个consumer，连接好之后，进行accept
    }
 
    /**
     * 接口：interface Function<T,R> 函数
     * 定义一个Lambda表达式，输入一个T类型的参数，返回一个R类型的值
     */
    @Test
    public void example3(){
        //apply 传入一个T类型的值，返回一个R类型的值
        Function<Integer, Double> function = x -> x + 10.5;
        System.out.println(function.apply(10)); //打印：20.5
 
        //两个Function
        Function<String, String> fun1 = str1 -> {
            System.out.println("fun1");
            return str1 + "1";
        };
        Function<String, String> fun2 = str2 -> {
            System.out.println("fun2");
            return str2 + "2";
        };
 
        //compose 先执行compose中的函数，再把返回值当作参数执行原函数
        String str1 = fun1.compose(fun2).apply("张三");
        System.out.println(str1); //打印：fun2 fun1 张三21
 
        //andThen 先执行原函数，再把原函数的返回值当作参数执行andThen中的函数
        String str2 = fun1.andThen(fun2).apply("张三");
        System.out.println(str2); //打印： fun1 fun2 张三12
    }
 
    /**
     * 接口：interface Predicate<T> 断言
     * 定义一个Lambda表达式，输入一个T类型的参数，返回一个true/false
     */
    @Test
    public void example4(){
        //test 测试输入的参数是否满足定义的lambda表达式
        Predicate<String> pre1 = s -> "test".equals(s);
        System.out.println(pre1.test("test")); //打印：true
        System.out.println(pre1.test("test_")); //打印：false
 
        //and 原Predicate接口和and方法中指定的Predicate接口要同时为true，结果才为true，同逻辑运算符&&一样
        Predicate<String> pre2 = s -> s.length() == 4;
        System.out.println(pre1.and(pre2).test("test")); //打印：true
        System.out.println(pre1.and(pre2).test("ssss")); //打印：false
 
        //or 原Predicate接口和or方法中指定的Predicate接口有一个为true，结果就为true，同逻辑运算符||一样
        System.out.println(pre1.or(pre2).test("ssss")); //打印：true
 
        //negate 对结果取反再输出
        System.out.println(pre1.negate().test("test")); //打印：false
    }
```

Java SE privide extended functional interface:
Eg: DoubleSupplier, DoubleConsumer, DoubleFunction, etc. So that we don't have to cast the returned result.

### Method Reference

Method reference is actually a labda expression, it replaces the lambda expression, make code more simple.
```java
@Test
    public void example1(){
 
        //使用Lambda表达式
        Consumer<String> consumer1 = x -> System.out.println(x);
        consumer1.accept("Lambda表达式");
 
        //使用方法引用
        Consumer<String> consumer2 = System.out::println;
        consumer2.accept("方法引用::");
```
In method reference, we are refering `println` method of System.out, `println` method can replace lambda expression `x -> System.out.println(x)`. 

There are five types of method references:
1. instance's method reference: instance::methodName;
2. Class's static method reference: ClassName::methodName;
3. Class's instance's method reference: ClassName::methodName;
4. Constructor reference: ClassName::new;
5. Array reference: ClassName[]::new;

```java
/**
 * 引用类
 */
class Human{
    private String name;
    private int age;
 
    public Human(){ }
    public Human(String name, int age) {
        this.name = name;
        this.age = age;
    }
 
    public static String getNationality(){
        return "中国";
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public int getAge() {
        return age;
    }
 
    public void setAge(int age) {
        this.age = age;
    }
}
```

```java
/**
     * 引用对象的实例方法
     */
    @Test
    public void example3(){
        Human human = new Human("小白",20);
 
        //使用Lambda表达式
        Supplier<String> supplier1 = () -> human.getName();
        System.out.println(supplier1.get());
 
        //使用方法引用
        Supplier<String> supplier2 = human::getName;
        System.out.println(supplier2.get());
    }
 
    /**
     * 引用类的静态方法
     */
    @Test
    public void example4(){
        //使用Lambda表达式
        Supplier<String> supplier1 = () -> Human.getNationality();
        System.out.println(supplier1.get());
 
        //使用方法引用
        Supplier<String> supplier2 = Human::getNationality;
        System.out.println(supplier2.get());
    }
 
    /**
     * 引用类的实例方法名
     */
    @Test
    public void example5(){
        Human human = new Human("小白",20);
 
        //使用Lambda表达式
        Function<Human, String> function1 = p -> p.getName();
        System.out.println(function1.apply(human));
 
        //使用方法引用
        Function<Human, String> function2 = Human::getName;
        System.out.println(function2.apply(human));
    }
 
    /**
     * 引用构造方法
     */
    @Test
    public void example6(){
 
        //使用Lambda表达式
        Supplier<Human> supplier1 = () -> new Human();
        System.out.println(supplier1.get() instanceof Human); //true
 
        //使用方法引用
        Supplier<Human> supplier2 = Human::new;
        System.out.println(supplier2.get() instanceof Human); //true
    }
 
    /**
     * 引用数组
     */
    @Test
    public void example7(){
        //使用Lambda表达式
        Function<Integer, String[]> function1 = x -> new String[]{x.toString()};
        System.out.println(function1.apply(10) instanceof String[]); //true
 
        //使用方法引用
        Function<Integer, String[]> function2 = String[]::new;
        System.out.println(function2.apply(10) instanceof String[]); //true
    }
```

Like functional interface, Supplier<T> don't accept parameter, Consummer<T> accept only one parameter without return, Function<T, R> accept one parameter, return one type. Therefore, method reference for pre-defined functional interface only support one parameter and one return at most, if we need more parameter, we must create user defined functional interface.

```java
public class Test {
    public static void main(String[] args) {
        Test test = new Test();
        Interface0 interface0 = test::print;//If we want to use method reference, the parameter of print and apply must be the same
        interface0.apply("1", "2", "3");
    }
    
    public void print(String s1, String s2, String s3){
        System.out.println(s1+s2+s3);
    }
    
    @FunctionalInterface
    public interface Interface0{
        void apply(String s1, String s2, String s3);
    }
}
```

### this
Because lambda expression doesn't create an anonymous class, so `this` in lambda expression will be the object that lambda expression resides:

```java
public class Hello {
	Runnable r1 = () -> { System.out.println(this); };
	Runnable r2 = () -> { System.out.println(toString()); };
	public static void main(String[] args) {
		new Hello().r1.run();//Hello Hoolee
		new Hello().r2.run();//Hello Hoolee
	}
	public String toString() { 
		return "Hello Hoolee"; 
	}
}
```
Above code, if `this` is some other object, the output will not be "Hello Hoolee".

This is the concept of closure.


### Lambda and Collections
After adding lambda expression, java 8 expands its Collections' API.
Java 8 provide `java.util.function`, which include common functional interface. This is the pre-condition of API extension of Collections.

![alt](https://gitee.com/objcoding/md-picture/raw/master/img/JCF_Collection_Interfaces.png)

Above the green ones has been added new API, so as their subClasses.

| Collection Name | Java8 new API                                                                                                                 |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
| Collection      | removeIf() spliterator() stream() parallelStream() forEach()                                                                  |
| List            | replaceAll() sort()                                                                                                           |
| Map             | getOrDefault() forEach() replaceAll() putIfAbsent() remove() replace() computeIfAbsent() computeIfPresent() compute() merge() |


##### forEach
`void forEach(Consumer<? super E> action)`

```java
// 使用forEach()结合Lambda表达式迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.forEach( str -> {
        if(str.length()>3)
            System.out.println(str);
    });
```
More API checkout: https://objcoding.com/2019/03/04/lambda/#top


### Stream API()

Stream is about functional programming.

Stream is not a data structure, it's a data view. The data source can be an array, Collection, or I/O channel.
To get a stream, we don't create by hand, but use the API of array, Collection, or I/O channel.

- `Collection.stream()` or `Collection.parallelStream()` 
- Arrays.stream(T[] array)

Typical stream types:
![alt](https://gitee.com/objcoding/md-picture/raw/master/img/Java_stream_Interfaces.png)

The features of stream:

- No storage. Again, stream is not a data structure, it's just a view of the data.
- For functional programming. Any operation on stream will not change the source data, eg. do filter for a stream, will not delete the element of being filtered, but will produce a new stream without the filtered elements. Here new stream is still a view.
- Lazy execution. Operation on stream is not executed instantly, only some "terminate" operations will trigger the all the operations.
- One stream can only be "consumed" only once.

Two kinds of operation:
1. Intermediate operations: will be lazy triggered.
2. Terminal operations: will execute all the operations on the pipeline.

| Type                    | API                                                                                                                                 |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Intermediate operations | concat() distinct() filter() flatMap() limit() map() peek() skip() sorted() parallel() sequential() unordered()                     |
| Terminal operations     | allMatch() anyMatch() collect() count() findAny() findFirst() forEach() forEachOrdered() max() min() noneMatch() reduce() toArray() |
