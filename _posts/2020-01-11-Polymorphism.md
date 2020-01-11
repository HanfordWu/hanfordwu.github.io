# Polymorphism

What is Polymorphism?
when you write:
``Animal d = new Dog()``, you are using father referrer to child object, when you override method eat() of Animal in Dog, d.eat() will execute overridden method in Dog. If eat() is not overridden, then execute eat() of Animal. This is Polymorphism.

Why Polymorphism?
1. We know interface and abstract class is useful, Polymorphism is the base of those implementation.
2. In order to simplify inheritance, Java only allow single inheritance, in this case Polymorphism provide flexible implementation base on single inheritance.

Why we use father refer to child object?
Simplify code.
For example, factory pattern, we have an interface for all concrete product, when we create a product, and call one of its method, we don't have to specify which product we have (when we have a lot products, this will be a lot of code), only use abstract product.method() is OK. In a word, 

Implementation type:
1. static Polymorphism: by overload
2. dynamic Polymorphism: by override


## Overload
1. Methods are in the same class or subclass(cause subclass has all inherited methods, but father class do not know the overload method)
2. Methods have same name
3. Methods can have different number and types of parameters.
4. Methods can have different return type
5. Methods can have new visibility.

Different constructors are overload.

## Override
Referring father and child methods.
two sames two smalls one big:
1. same method name, same parameters
2. child method return type smaller than father method; Child method's exception smaller than father method.
3. Child method visibility bigger than father.(visibility can be different when overriding)

Points:
1. static method cannot be overridden, but it can be declared again.
2. If one method cannot be inherited, it cannot be overridden

Here is an compiling error:
```java
class Animal{
   public void move(){
      System.out.println("Animal can move");
   }
}
 
class Dog extends Animal{
   public void move(){
      System.out.println("Dog can walk and run");
   }
   public void bark(){
      System.out.println("Dog can bark");
   }
}
 
public class TestDog{
   public static void main(String args[]){
      Animal a = new Animal(); // Animal 对象
      Animal b = new Dog(); // Dog 对象
 
      a.move();// 执行 Animal 类的方法
      b.move();//执行 Dog 类的方法
      b.bark();
   }
}
```
result:
```
TestDog.java:30: cannot find symbol
symbol  : method bark()
location: class Animal
                b.bark();
```
Because b is declared as Animal, but animal has no bark() method. b.move() has no problem, but actually it execute dog's move(), event though b is declared as Animal.