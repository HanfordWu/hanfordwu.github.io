We often say thread safety, this is regarding multiple thread are going to operate on one object, and their operations are not ware of by each other, so the variable they read and finally write is incorrect and no guarantee.

But now I'm going to talk about how to share object among threads.

First, two ways of creating a class that will be run in a new thread:

```java
//way 1
class Task implements Runnable{
	@Override
	public void run (){
		System.out.println ("Runnable interface");
	}
}
//way 2
class ThreadDemo extends Thread{
	@Override
	public void run(){
		System.out.println( "Thread class ");
	}
}
```

But we prefer way 1 over way 2 because:
- Java does not support multiple inheritance. Hence if your class had already extended some another class, it can not extend Thread class anymore.
- By implementing Runnable we can reuse the task to execute it on different threads. Also it could be run by other means like Executor.
- Implementing Runnable provide Composition and extending Thread is Inheritance . Try to favor composition over inheritance.



**Share Object Among Threads**

**Way 1: use shared data member:** if all threads run() the same code.

```java

public static void main(String[] args) {
        ShareData1 shareData1 = new ShareData1();
        new Thread(shareData1).start();
        new Thread(shareData1).start();
    }

static class ShareData1 implements Runnable {
        public int count = 0;
        public void run() {
            for (; count < 100; count++) {
                System.out.println(Thread.currentThread().getName() + ":"+count);
            }
        }
    }
}
```
Here `count` is the shared data among two threads, because we pass the same runnable object to two threads. And it's not thread safe.

**Way 2: Pass shared object to threads**

```java
public static void main(String[] args) {
        final ShareData1 shareData1 = new ShareData1();
        new Thread(new MyRunnable1(shareData1)).start();
        new Thread(new MyRunnable1(shareData1)).start();
}
static class MyRunnable1 implements Runnable{
        private ShareData1 shareData1;
        public void run() {
        }
        public MyRunnable1(ShareData1 shareData1){
            this.shareData1 = shareData1;
        }
    }
static class MyRunnable2 implements Runnable{
        private ShareData1 shareData1;
        public void run() {
        }
        public MyRunnable2(ShareData1 shareData1){
            this.shareData1 = shareData1;
        }
    }
static class ShareData1 {
....
}
```

**Way 3: Let Runnable class be a inner class:** put all shared data and needed methods in the outer class, have as many as runnable inner classes if they are doing different thing in run() method.

```java
public class MultiThreadShareData {
    private int j;
    public static void main(String[] args) {
        MultiThreadShareData multiThreadShareData = new MultiThreadShareData();
        for(int i=0;i<2;i++){
            new Thread(multiThreadShareData.new ShareData1()).start();//增加
            new Thread(multiThreadShareData.new ShareData2()).start();//减少
        }
    }
    //自增
    private synchronized void Inc(){
        j++;
        System.out.println(Thread.currentThread().getName()+" inc "+j);
    }
    //自减
    private synchronized void Dec(){
        j--;
        System.out.println(Thread.currentThread().getName()+" dec "+j);
    }
    
    class ShareData1 implements Runnable {
        public void run() {
            for(int i=0;i<5;i++){
                Inc();
            }
        }
    }
    class ShareData2 implements Runnable {
        public void run() {
            for(int i=0;i<5;i++){
                Dec();
            }
        }
    }
}
```