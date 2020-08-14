First time I know this kind of error was Professor Ynn. teaching me iterator pattern, but to be honest I didn't care much because I am not familia with concurrency. This is my knowledge debt now I am going to payback, and talk more about iterator and iteratable.

##### The Exception

The error happen in the following code:

```java
import java.util.ArrayList;
import java.util.Iterator;

public class Test {

    public static void main(String[] args) {
        ArrayList<String> array = new ArrayList<String>();

        // 创建并添加元素
        array.add("hello");
        array.add("world");
        array.add("java");
        Iterator it = array.iterator();
        while (it.hasNext()) {
            String s = (String) it.next();
            if ("world".equals(s)) {
                array.add("javaee");
            }
        }
    }
}
```

Whenever we get the iterator of a collection, we cannot change its structure(add, remove) before we use the iterator.

This is the single thread case. If in multi-thread case, an ArrayList is shared by multiple threads(I will talk about how to share objects in multi-threads), if this ArrayList is not `synchronized`, there will also be `ConcurrentModificationException`

```java
public class Main {
    public static void main(String[] args) throws Exception {

        MyThread myThread = new MyThread();

        new Thread(myThread).start();
        new Thread(myThread).start();
        new Thread(myThread).start();
    }
}
/////////////////////////
public class MyThread implements Runnable {
    private List<String> list = new ArrayList<>();

    @Override
    public void run() {
        this.it();
        this.add();
    }

    public void add(){
        for (int i = 0; i < 10000; i++) {
            this.list.add("hello" + i);
        }
    }

    public void it(){
        for (String s : list) {
            //这里增强for就是在用iterator
        }
    }
}
```


##### Details of iterator and iteratable

Iterator interface:
```java
public interface Iterator<E> {
    boolean hasNext();
    E next();


    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    default void forEachRemaining(Consumer<? super E> var1) {
        Objects.requireNonNull(var1);

        while(this.hasNext()) {
            var1.accept(this.next());
        }

    }
}
```

We can create an iterator for an object, by passing the object to constructor of iterator. Then implement `hasNext()` and `next()` methods.


Iteratable interface:

```java

public interface Iterable<T> {
    Iterator<T> iterator();

    default void forEach(Consumer<? super T> var1) {
        Objects.requireNonNull(var1);
        Iterator var2 = this.iterator();

        while(var2.hasNext()) {
            Object var3 = var2.next();
            var1.accept(var3);
        }

    }

    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(this.iterator(), 0);
    }
}
```

If one class implements `Iteratable` interface, it can be used in enhanced for loop because of the default method.

**Why we cannot change List structure after we get the iterator of it?**

When we do `list.iterator`, all elements are pass to iterator constructor, it don't change till next time we get a new iterator. But between this, if we add or remove elements of the list, the iterator will not know the change, so the iteration will be incorrect.

We cannot prevent this thread safety issue by synchronize block, because we don't know when the user will get the iterator and how to use it.


**Solution**
In side of the implemented iterator, we can add two more methods: `addItem()`, `removeItem()`, so that we can add/remove item both in origin array and the iterator.
