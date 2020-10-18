Before we have enum, how do we define constant?
```java
public class DayDemo {

public static final int MONDAY =1;

public static final int TUESDAY=2;

public static final int WEDNESDAY=3;

public static final int THURSDAY=4;

public static final int FRIDAY=5;

public static final int SATURDAY=6;

public static final int SUNDAY=7;

}
```

Because we are using integer to represent a constant, it's easy to confuse them with a regular integer.

Now we use enum to define constant:

```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

We can define a enum in independent file, or inside of another class.

How to use it?

```java
public class EnumDemo {

    public static void main(String[] args){
        //直接引用
        Day day =Day.MONDAY;
    }

}
//定义枚举类型
enum Day {
    MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

A little bit similar to static fields.

### Under the Hood

After compilation, the enum `Day` is compiled to a class file: `Day.class`.

Now we de-compile it 

```java
final class Day extends Enum
{
    //编译器为我们添加的静态的values()方法
    public static Day[] values()
    {
        return (Day[])$VALUES.clone();
    }
    //编译器为我们添加的静态的valueOf()方法，注意间接调用了Enum也类的valueOf方法
    public static Day valueOf(String s)
    {
        return (Day)Enum.valueOf(com/concordia/enumdemo/Day, s);
    }
    //私有构造函数
    private Day(String s, int i)
    {
        super(s, i);
    }
     //前面定义的7种枚举实例
    public static final Day MONDAY;
    public static final Day TUESDAY;
    public static final Day WEDNESDAY;
    public static final Day THURSDAY;
    public static final Day FRIDAY;
    public static final Day SATURDAY;
    public static final Day SUNDAY;
    private static final Day $VALUES[];

    static 
    {    
        //实例化枚举实例
        MONDAY = new Day("MONDAY", 0);
        TUESDAY = new Day("TUESDAY", 1);
        WEDNESDAY = new Day("WEDNESDAY", 2);
        THURSDAY = new Day("THURSDAY", 3);
        FRIDAY = new Day("FRIDAY", 4);
        SATURDAY = new Day("SATURDAY", 5);
        SUNDAY = new Day("SUNDAY", 6);
        $VALUES = (new Day[] {
            MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
        });
    }
}
```
Six things that the compiler did:

- Created a class extends java.lang.Enum
- Added private constructor, parameters are the string of the enum, and an index
- public static final fields with type of `Day`, and an array `$VALUES[]`
- Initialization  of all fields, and `$VALUES[]`
- Added static method `public static Day[] values()`
- Added static method `public static Day valueOf(String s)`

The fields means the compiler generate instance for each enum.

**About java.lang.Enum**

java.lang.Enum has its methods:

| Return Type                 | Method Name                                    |                            Description                           |
|-----------------------------|------------------------------------------------|:----------------------------------------------------------------:|
| int                         | compareTo(E o)                                 |                        To determine order                        |
| boolean                     | equals(Object other)                           |                   To compare an object to enum                   |
| Class<?>                    | getDeclaringClass()                            |                           Return class                           |
| String                      | name()                                         |                              Return                              |
| int                         | ordinal()                                      | Return the position of being declared |
| String                      | toString()                                     |                Return field name of string, we can have a name in declaration                |
| static<T extends Enum<T>> T | static valueOf(Class<T> enumType, String name) |             Return a enum with type and name             |

`ordinal()`, it returns the order index of enum being declared. Eg: MONDAY return 0, TUESDAY return 1, etc. 

`compareTo(E o)` is comparing enum, the default is comparing the ordinal number.

`name()` is similar to `toString()`, output the string format of fields.

`public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable`

The constructor of java.lang.enum can only be called by compiler.


Demo of above methods:
```java
public class EnumDemo {

    public static void main(String[] args){

        //创建枚举数组
        Day[] days=new Day[]{Day.MONDAY, Day.TUESDAY, Day.WEDNESDAY,
                Day.THURSDAY, Day.FRIDAY, Day.SATURDAY, Day.SUNDAY};

        for (int i = 0; i <days.length ; i++) {
            System.out.println("day["+i+"].ordinal():"+days[i].ordinal());
        }

        System.out.println("-------------------------------------");
        //通过compareTo方法比较,实际上其内部是通过ordinal()值比较的
        System.out.println("days[0].compareTo(days[1]):"+days[0].compareTo(days[1]));
        System.out.println("days[0].compareTo(days[2]):"+days[0].compareTo(days[2]));

        //获取该枚举对象的Class对象引用,当然也可以通过getClass方法
        Class<?> clazz = days[0].getDeclaringClass();
        System.out.println("clazz:"+clazz);

        System.out.println("-------------------------------------");

        //name()
        System.out.println("days[0].name():"+days[0].name());
        System.out.println("days[1].name():"+days[1].name());
        System.out.println("days[2].name():"+days[2].name());
        System.out.println("days[3].name():"+days[3].name());

        System.out.println("-------------------------------------");

        System.out.println("days[0].toString():"+days[0].toString());
        System.out.println("days[1].toString():"+days[1].toString());
        System.out.println("days[2].toString():"+days[2].toString());
        System.out.println("days[3].toString():"+days[3].toString());

        System.out.println("-------------------------------------");

        Day d=Enum.valueOf(Day.class,days[0].name());
        Day d2=Day.valueOf(Day.class,days[0].name());
        System.out.println("d:"+d);
        System.out.println("d2:"+d2);
    }
 /**
 Outputs:
   day[0].ordinal():0
   day[1].ordinal():1
   day[2].ordinal():2
   day[3].ordinal():3
   day[4].ordinal():4
   day[5].ordinal():5
   day[6].ordinal():6
   -------------------------------------
   days[0].compareTo(days[1]):-1
   days[0].compareTo(days[1]):-2
   clazz:class com.concordia.enumdemo.Day
   -------------------------------------
   days[0].name():MONDAY
   days[1].name():TUESDAY
   days[2].name():WEDNESDAY
   days[3].name():THURSDAY
   -------------------------------------
   days[0].toString():MONDAY
   days[1].toString():TUESDAY
   days[2].toString():WEDNESDAY
   days[3].toString():THURSDAY
   -------------------------------------
   d:MONDAY
   d2:MONDAY
   */

}
enum Day {
    MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

**values and valuedOf methods**

In java.lang.Enum, there is no `values()`, so it's added in only concrete enum class.

In java.lang.Enum, there is `valuesOf(Class<T> enumType, String name)` method. but in concrete enum class, the method is `valueOf(String name)`.  and it's calling the super class' method.

Demo of those two methods:

```java
Day[] days2 = Day.values();
System.out.println("day2:"+Arrays.toString(days2));
Day day = Day.valueOf("MONDAY");
System.out.println("day:"+day);

/**
 outputs:
 day2:[MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY]
 day:MONDAY
 */
 ```
So `values()` return all instance of enum type by an array.

`valueOf()` return on instance with the name passed.

Note: Because `values()` is added only in concrete enum, so if we upcast a concrete enum to `Enum`, it will have no `values()` method.

But we can have another way, using `getEnumConstants()` of `Class` type.
```java
//正常使用
Day[] ds=Day.values();
//向上转型Enum
Enum e = Day.MONDAY;
//无法调用,没有此方法
//e.values();
//获取class对象引用
Class<?> clasz = e.getDeclaringClass();
if(clasz.isEnum()) {
    Day[] dsz = (Day[]) clasz.getEnumConstants();
    System.out.println("dsz:"+Arrays.toString(dsz));
}

/**
   输出结果:
   dsz:[MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY]
 */
```

### Add methods and customized constructor

```java
public enum Day2 {
    MONDAY("星期一"),
    TUESDAY("星期二"),
    WEDNESDAY("星期三"),
    THURSDAY("星期四"),
    FRIDAY("星期五"),
    SATURDAY("星期六"),
    SUNDAY("星期日");//记住要用分号结束

    private String desc;//any description from the declaration

    /**
     * 私有构造,防止被外部调用
     * @param desc
     */
    private Day2(String desc){
        this.desc=desc;
    }

    /**
     * 定义方法,返回描述,跟常规类的定义没区别
     * @return
     */
    public String getDesc(){
        return desc;
    }

    public static void main(String[] args){
        for (Day2 day:Day2.values()) {
            System.out.println("name:"+day.name()+
                    ",desc:"+day.getDesc());
        }
    }

    /**
     输出结果:
     name:MONDAY,desc:星期一
     name:TUESDAY,desc:星期二
     name:WEDNESDAY,desc:星期三
     name:THURSDAY,desc:星期四
     name:FRIDAY,desc:星期五
     name:SATURDAY,desc:星期六
     name:SUNDAY,desc:星期日
     */
}
```
So if we declare enum with parameters, we can create customized constructor to receive those parameters.

**Define abstract method**

We can define abstract method in enum class, and implement the method in each enum:
```java
public enum EnumDemo3 {

    FIRST{
        @Override
        public String getInfo() {
            return "FIRST TIME";
        }
    },
    SECOND{
        @Override
        public String getInfo() {
            return "SECOND TIME";
        }
    }

    ;

    /**
     * 定义抽象方法
     * @return
     */
    public abstract String getInfo();

    //测试
    public static void main(String[] args){
        System.out.println("F:"+EnumDemo3.FIRST.getInfo());
        System.out.println("S:"+EnumDemo3.SECOND.getInfo());
        /**
         输出结果:
         F:FIRST TIME
         S:SECOND TIME
         */
    }
}
```

By this way, we can apply polymorphism on enum.

### Enum can implement interface
```java
interface food{
    void eat();
}

interface sport{
    void run();
}

public enum EnumDemo2 implements food ,sport{
    FOOD,
    SPORT,
    ; //分号分隔

    @Override
    public void eat() {
        System.out.println("eat.....");
    }

    @Override
    public void run() {
        System.out.println("run.....");
    }
}
```

### Enum and switch case

We can use Enum in switch case:
enum Color {GREEN,RED,BLUE}

public class EnumDemo4 {

    public static void printName(Color color){
        switch (color){
            case BLUE: //无需使用Color进行引用
                System.out.println("蓝色");
                break;
            case RED:
                System.out.println("红色");
                break;
            case GREEN:
                System.out.println("绿色");
                break;
        }
    }

    public static void main(String[] args){
        printName(Color.BLUE);
        printName(Color.RED);
        printName(Color.GREEN);

        //蓝色
        //红色
        //绿色
    }
}
```
Note for each case, we don't have to write `Color.BLUE`

