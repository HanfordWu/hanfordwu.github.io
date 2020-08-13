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


Keywords in Java regarding thread safety

- volatile: variables that are marked as volatile, if the variable got changed in one thread, it will be updated instantly to main memory, and all its copy in other threads will be marked invalid, so next time other thread use it, they have to access main memory again. This is referring to the words **Visibility**
- synchronized: can be added to function or blocks, means locked(monopoly) by one thread which get the "lock".
- final: variables that are final



- JVM Model: JVM memory 分区.