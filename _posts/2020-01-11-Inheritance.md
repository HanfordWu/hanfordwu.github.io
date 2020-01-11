# Inheritance:
1. Relationship of inheritance: is-a. Father is more general, child is more concrete.
   
Example:
企鹅： 属性（姓名， id）， 方法（吃，睡，自我介绍）
老鼠：属性（姓名，id），方法（吃，睡，自我介绍）

There are many duplicate code among above two classes, it's gonna be overstaffed in the future. Hard to maintain.
Inheritance extract the similarity of classes to be a father class.

2. 继承到底继承了什么？有什么特点？

        一个类里面有4种东西：
        属性（包括类属性和实例属性）、
        方法（包括类方法和实例方法）、
        构造器/构造方法
        初始化块（包括类的初始化块和实例的初始化块）。
        子类继承父类的时候，到底继承了什么？
        1、子类继承父类所有的属性（除了private）
        2、子类继承父类（除private）所有的方法，（子类方法如果不调用 super.所复写方法名称 ，那么对应父类方法将不会执行）
        final方法不可以被继承
        static方法不可以被继承，随着类的加载而加载，继承毛线。但是如果权限允许子类还是可以用。
        3、子类可以通过super，表示父类的引用，调用父类的属性或者方法。
        （构造函数和代码块是无法被继承）（类的代码块是没有任何声明和函数名的被大括号包裹的块）
        对于private这点非常好理解，因为private的访问权限是本类中嘛，就算通过super也不能访问private的private属性。但是private变量始终存在于父类中， 所以仍然可以通过对应的get方法获取，get是public嘛。

        作者：阿敏其人
        链接：https://www.jianshu.com/p/fbcc60ff12c6
        来源：简书
        著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

        通过以上文章的代码示例也可以了解父类，子类，对象的初始化过程，非常经典。

3. 继承的好处是是可以提高效率，抽取封装。 缺点是提高了类之间的耦合性（继承的缺点，耦合度高就会造成代码之间的联系越紧密，代码独立性越差）
4. 在 Java 中，类的继承是单一继承，也就是说，一个子类只能拥有一个父类，所以 extends 只能继承一个类。
5. 使用 implements 关键字可以变相的使java具有多继承的特性，使用范围为类继承接口的情况，可以同时继承多个接口（接口跟接口之间采用逗号分隔）
6. super关键字：我们可以通过super关键字来实现对父类成员的访问，用来引用当前对象的父类。
7. final 关键字声明类可以把类定义为不能继承的，即最终类；或者用于修饰方法，该方法不能被子类重写
8. Default (without any keyword) visibility means that your class visible inside package where it defined. There is no private class.
9. 每个类都至少有一个构造函数，如果没写构造函数，编译器会自动加上一个无参构造函数。如果写了有参构造器，编译器就不会再加无参构造器，此时该类没有无参构造器，因此继承它的子类的构造器里必须显式调用super(parameter)。
10. 子类创建对象的时候，调用子类构造函数，子类的各种构造函数中都有一个隐式的父类无参构造函数， 这个函数执行后就相当于把该继承的内容都在新的唯一的子类对象中创建了，父类和子类里的this.都是指向这个对象。例如未重写的public方法，public 成员。子类继承相当于类在加载时准备了子类和父类的所有信息（成员和方法），时刻准备创建对象的时候使用。当然也可以显式调用父类构造函数和其他方法。
11. 当父类构造函数是无参私有时，子类仍然可以隐式调用父类构造函数，即使它是私有的。
12. 初始化一个对象的时候，肯定是先开辟一个物理空间，开辟好后，把物理空间地址，返回给this，这个时候，在通过this对物理空间具体值进行修改。
13. One class can be a static class. When class B is a inner class of A, B can be static class, means B is a static member of A. If a static method new Class B, B must be static.
14. Class's data member without specify public or private, it's public by default.
15. 类中可以有静态代码块，普通代码块。类加载的时候执行静态代码块，类实例化的时候首先执行普通代码块，再执行构造函数。



