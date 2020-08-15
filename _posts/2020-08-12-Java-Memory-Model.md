### JMM: Java memory model




![alt](https://jgrppa.ch.files.1drv.com/y4m9nsK7zx46L2RAMER87BB_ZqeHC87hTGZXq_338L0f3ieuIkVAmOBEFj7KWXi_ZRDcF4-mhwj7rXRNYlbZGJ5_qhnEhZ7s3ayp26aRUCR9Xhgafz1CYSQ_Q9q9VGc9RcDqj9luircyiMjb4rzP0RFqiwRG1nam_PlBkCsJR0dBjLiNglAWtO6ZI8HQDYZVEjQ5nloP65pORAG1Cawmd30rQ?width=505&height=427&cropmode=none)

Thread Stack:  It's the same thing JVM stack in JVM memory model, But I would say thread stack is the copy of JVM stack which is in the cache.

Heap:

- Primary type will be copied to thread stack
- Reference type will copy reference to thread stack, but the object stay at heap.



For JVM, everything is stored at main memory.

For a thread, it's a unit that CPU can run. It has its conceptual working memory, corresponding to cache. 

Working memory has the variables that thread needs, which are copied from main memory.

If multi-thread are using a same object in heap, they copy its data member and method instructions to each work memory, the variable change in each working memory is not visible to other working memory. 

![alt](https://jwrppa.ch.files.1drv.com/y4mSfpgAtMuIzsk22JxZacgno_FsvYoODYWyZz08ONfDOaXGQTu2r3BxO3csNXxnnlxuZBm4P9abIuaEqb4pYe9NH0_p14tNIiUWTCLBlaGPgMUQxK9vhUXIzvjM4750kLmPpyeSE8uTTQd0-AZzsWqWrjs29p7qRlRIE4DJhOxILQjYCrTsbiqPacI-EIQax1q97idSO2xBIToCYdip_AaUg?width=452&height=398&cropmode=none)

There are eight operations in JMM:
- lock: function on variables in main memory, mark a variable to be monopoly by one thread.
- unlock: similar to lock, but release variables from monopoly.
- read: function on variables in main memory, copy variable from main memory.(We can don't consider methods instructions because methods instructions never change, we can directly copy them to cache).
- load: function on variables in working memory, put the read data to working memory.
- use: function on variables in working memory, whenever CPU meet an instruction that needs a variable it will use.
- Assign: function on working memory, whenever CPU meet an instruction assigning to a variable.
- Store:  function on variables in working memory, copy it.
- Write: function on variables in main memory, write the copied variable to main memory.

All above operations are atomic.
> **Atomic** means the operation will be complete once it starts.
> **Visibility** means the change in one thread is visible for all other threads.
> **Happen-before** Any operation that following **Happen-after** means that they have are following an order to do something.



Keywords in Java regarding thread safety

- volatile: variables that are marked as volatile, if the variable got changed in one thread, it will be updated instantly to main memory, and all its copy in other threads will be marked invalid, so next time other thread use it, they have to access main memory again. This is referring to the words **Visibility**. It's **Visibility** and **Happen-before**.
- synchronized: can be added to function or blocks, means locked(monopoly) by one thread which get the "lock". It is **Happen-before** and **Atomic**

> volatile guarantee the **Visibility** of primary type, for reference type, volatile doesn't guarantee the referenced object is visible for all threads, Only guarantee the pointing itself visible.






### JVM Model

![alt](https://karppa.ch.files.1drv.com/y4mJpUW5-74VDJRHLlOLEb2OxCX0Gu3ZyApUs9Uhh_FqYI1rfpEwZYuJmdEUDAWnPiRnX2ZzEP9q8EKXLhvLH1fPKo9lHShnqfPxQB9uJx9HPs0JfAk17jbV60E4X76F8FNMa8jLgNuLVm8NZXLaFJj1deLBJZhCGvhXQIDOJ-kxhZi2PtnRZA9lK_wdSOhbnDoKw9jsaYrk1PGUbPYv44DEQ?width=766&height=350&cropmode=none)

![alt](https://iqrppa.ch.files.1drv.com/y4m0dOwtd5SisFTEtT9C0XrMkd6efdlkSxGhwJNmuWtzbVl4eM62mKhrfUQASqeFAFjW3D0et0M4IwnWtrGTQic3x6RWDHdS1BlXny_8MHlvA5bxYWYD-T4NcWlX-jsrejZ-ncm-ncB-LLGukvhARx8JyJOVw1VMKOglRcTOMm98L8OnOlt-MNHXEq4ThcI11WYE2s7dTZeIZONmPDAp64egw?width=709&height=451&cropmode=none)

#### Program Counter Register

- thread private, each thread has one PC, its lifecycle is the same as the thread. 
- If JVM is running java code, it points to current instruction address, if JVM is running native method it points to null.

Errors: OutOfMemoryError

#### JVM Stack

- It's corresponding to thread stack in JMM
- thread private, each thread has one PC, its lifecycle is the same as the thread.
- each method has one stack frame, to store local variables, operation stack, dynamic links, method exit. etc.
- local variables store kinds of primary type, reference type(points to heap). the size of local variable table is determined in compiling time and don't change when it's running.

Errors:
- StackOverflowError
- OutOfMemoryError


#### Native Method Stacks

- similar to JVM stack, but for native methods

Errors: StackOverflowError, OutOfMemoryError

#### Heap

- Shared by all threads
- Not necessary to be consecutive address, can be extended dynamically.
- In terms of GC, divided into Young generation, old generation, and Eden, From Survivor, To Survivor space. in new generation.

Heap is where the object reside, one objects in heap includes the following information:
- Object head information: 
Mark Word(HashCode, GC age, lock status, etc.), and a pointer pointing to **Method Area** where has the class information. (and methods)
- Instance Data: Object's non-static member and its value. including data member extends from superclass.
- Aligned fill: fill extra data to be aligned.

Errors: OutOfMemoryError

#### Method Area

- Store class information, Constant, JIT compiled code, etc.
- Not necessary to be consecutive address,can be extended dynamically.
- GC mainly working on constant collecting and class unloading.

**Runtime Constant Pool**

- Part of Method Area
- Store constants in class
- String class' inter() to combine String constants

**Method Table**
In method area, each class has a method table, recording the entry of the implementation of the method.

If we have a class `Dog`:
```java
public class Dog{
    public String toString() {
        return "I'm a Dog";
    }

    public void sayHello() {
        System.out.println("sayHello");
    }
}
```

The object is in the heap, including non-static data member, 

Whenever a non-private/static method of an object is called, the object is stored in heap, and it has the class pointer pointing to **Method Area** to find the class information, the class information has a **Method table**, find the same name and parameter entry, go to fetch instructions into JVM stack, then start to run methods.



![alt](https://kqpqsq.ch.files.1drv.com/y4mR06b7-uo_DfTxp-H9PeA3s65B9UCmxZz5A6ZiPBnQWJlOslGP2h77qf7EqlPWWK5XhkrDfKpP0uRi8HGmVbh7i5-OrMhoLGJo2wo1jkiJs1vvLWwSHpXwtewDMmSkyARkdh3k34MTznPEJCE_lB9ktQX1X0r99s6PpGlzQDbH83a889pLuaaRaA4-2JB6f0J_0d1p76gj1XGRzvPmUwWoA?width=400&height=283&cropmode=none)



#### Direct Memory

- Not part of JVM, not any model of JMM, outside of JVM
- For NIO to create Buffer, assigned by native function. Pointed by DirectByteBuffer in Java heap.


> JIT - Just-in-tim compiler, javac compile .java file to .class file, (byte code).  JIT or Interpreter compile byte code to machine code. JIT is for enhance the speed of second compiling

![alt](https://igrppa.ch.files.1drv.com/y4mYzKl6rKHiB5-4px4V6L4EZhxpVzg_Wus6tNVc43lwVE1fOvkZFYCQVjbhpm6sPIP95qLfb_bMvxsXjgrChNxZtS7B8gqdzFuTxO8pv9C2Tzw2Cb8PAtkYTXMr0RqCUZD6xIxalFcyxqCwhHryZaVza1j7n6liDy1aYiCMLDNw_D6HVppUFrjtUnl1elFuWrl0wf_KaL8O3rEXoRbkecabg?width=1389&height=967&cropmode=none)