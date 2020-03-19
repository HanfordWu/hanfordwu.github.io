#### I. hashCode()
In `Java.lang.Object`, there is a function `hashCode()`, Its source code is:
```java
public native int hashCode();
```
It is a native method, a native method is a Java method whose implementation is written in another programming language such as C/C++.
Every object has a default hash code which is related to the address of the object(depends on the implementation of JVM). Usually different object get different hash code. But no guarantee.

#### II. The purpose of hashCode()
If we use an object as a key of hash table, HashTable hash the **hash code** to get the **hash value**. 
Below is the source code of `java.util.HashMap` put() method:
```java
//HashMap doesn't allow duplicate entries. 
public V put(K key, V value) {
        if (key == null)
            return putForNullKey(value);
        //first `hash(key.hashCode())`, then get the entry index, 
        int hash = hash(key.hashCode());
        int i = indexFor(hash, table.length);
        //The entry = table[i], it is the start of a LinkedList. Then iterate the LinkedList to check whether the key is the same with keys in the entry. 
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            //Each check, first compare the hash value(even different hash value may have the same entry), if it does, then check the address key(putting) == key(already there), or key.equals(key).
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
              //If it does, update the value of that pair.
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
```
Points:
**In HashMap, same key means two key objects are equal, so they must have the same hashCode. Otherwise we may not be able to get entry back from HashMap.**
- If two objects are equals(), hashCode must be the same. Otherwise, HashMap may not find the entries.
- If two objects' hashCode are not the same, they are must be not equal.
- If two objects' hashCode are the same, they are not guaranteed equal.
- If two objects are not equals, hashCodes are not guaranteed the same.

#### III. equals() and hashCode()
When we override equals() method, we must override hashCode() method as well.

```java
class People{
    private String name;
    private int age;
     
    public People(String name,int age) {
        this.name = name;
        this.age = age;
    }  
     
    public void setAge(int age){
        this.age = age;
    }
    //We override the equals method, and compare name and age.
    @Override
    public boolean equals(Object obj) {
        // TODO Auto-generated method stub
        return this.name.equals(((People)obj).name) && this.age== ((People)obj).age;
    }
}
 
public class Main {
 
    public static void main(String[] args) {
         
        People p1 = new People("Jack", 12);
        System.out.println(p1.hashCode());
             
        HashMap<People, Integer> hashMap = new HashMap<People, Integer>();
        hashMap.put(p1, 1);
         
        System.out.println(hashMap.get(new People("Jack", 12)));//get null
    }
}
```
In above example, we override the equals method, but we didn't override the hashCode method. p1 and p2 they are equal, but they are different object, has different address(hashCode), so that we cannot get key = People("Jack", 12) back. Therefore, we should override hashCode(), let hashCode() and equals() have the consistency.
**Note on overriding hashCode():**
When we compare two objects, we compare the properties, so, when we rewrite hashCode() method, we can use the hash code of object properties. And, we should use the stable properties(not change).
```java
@Override
    public int hashCode() {
        // we are using age and name to calculate hashCode
        return name.hashCode()*37+age;
    }
```
But if we change age in the middle of the program:
```java
public static void main(String[] args) {
         
        People p1 = new People("Jack", 12);
        System.out.println(p1.hashCode());
         
        HashMap<People, Integer> hashMap = new HashMap<People, Integer>();
        hashMap.put(p1, 1);
         // we change age here, so p1's hashCode changed
        p1.setAge(13);
        //we cannot get the value of p1
        System.out.println(hashMap.get(p1));
    }
```