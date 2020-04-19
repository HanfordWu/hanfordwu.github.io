# I. Process
**Questions**:
- Right software? Right needs?
- Plan? Design?
- Tested?
- Sustain further development/maintaination?

Waterfall model: traditional.
#### Development model:
1. Prototyping: elicit
2. Iterative and incremental dev: focus few issues at a time
3. Spiral: risk
4. Rapid application dev: productivity
5. Extreme programming: productivity

#### Predictive vs Adaptive
**Predictive**: Detail plan, predicted, documented, descriptive, change control board
**Adaptive(agile)**: Cope with change, prescriptive, exactly for next week, plan for next month.

#### Predictive:
**Entities**:
- Actor: Skills, resposibilities
- Atifact: Product, deliverables
- Task: unit of work

**Disadvantages**:
- costly step back for errors
- ripple effect, one change results many other changes
- accumulation of unstable information


**IBM's Rational Unifies Process:**
Iterative development.

#### Adaptive(agile):
**Keywords**: customer satisfaction, welcome change, working software, daily cooperation between business and developers, highly motivated individuals, face to face communication, continuous improving, self-orgnized team, sustainable.
*Managing change*: Distribute the the feedback
_Builds_(incremental dev): deliver software in short timebox. Have all necessary activities.
_Release each build_
_Re-evaluate each build_: sustainability for further dev and maintenance
_Good for small team(<10)_
**Cons**:
- has to be incorporated into existing
- has to reorgnize, or degrading quality
- easily degenerate into the build and fix approach
- design error is hard to remove
- elicit more changes than normal

**Solutions**:
- Gloabl build plan
- Refactoring
- Architecture baseline

No good for:
- Large team
- Distributed dev efforts
- Mission life-critical efforts
- Low changes
- Junior developers

Good for:
- Senior developer
- High changes
- Small team

# II. Extreme Programming (Embrace changes)
### Key features:
- Planning Game
- Small releases
- System metaphor: Globle plan
- Simple design
- Testing
- Refactoring
- Pair programming
- Collective ownnership
- Continuous Integration
- Sustainable pace
- On-site customer
- Coding stantards

### Value of XP:
**Communication**: collaboration of      users and programmers, frequent verbal communication, feedback.
**Simplicity**: Coding for needs of today
**Feedback**: From system, customers, team members.
**Courage**: Change habits, admit mistakes, have work questioned, throw away bad code.
**Respect**: Others and self.
**Embrace change**:

# III. Revision Control
**Reasons**:
- Keep track of versions and new changes
- Provide common repository
- Handle concurrent changes
- Rollback
- Compare

**Distribution**:
1. Local VCS
2. Centralized repository:
   Pros: Single server, clients checkout any version remotely, distributed *revisions*
   Cons: Sigle point failure, file access concurrency.
3. Distributed repository
   Pros: One central server, any client can be a server, no single point failure

# IV. Coding Convention
**Why**:
- Improve internal quality
- Maximizing productivity
- Improve sustainability
  
**How**:
- Follow a set of rules
- Peer review
- Refactoring activities

**Basic rules**:
- Clear & consistent
- Descriptive names
- Comments

# V. API Doc Generation
#### JavaDoc:
Automation, coupled with programming, efficient, browsable, productivity.
Require extra dedication.
**Comments** must appear immediately before classes, interfaces, cosntructors, methods, data members declarations.
**Tags**: @version @since @author @see @param @return @throws {@link reference}

# VI. Unit Testing - Junit
#### Error => Fault => Failure => Incident
Fault identification and fault correction is **Debugging**.
**Testing**: Identifying possible system failure, then design test case to prove not exist.
**Unit testing => Integration Testing => Function Testing => Acceptance Testing => Installation Testing**

**Pros:**
- Facilitate changes: Test after refatoring or any changes.
- Simplifies integration
- Living documentation: Helps to understand API
- Distribute feedback
- Confidence building
- Force analysis
  
Testing Framework: Automate, efficient

**Three phases of test case**:
1. Set up the context
2. Call the tested method
3. Observe if the method behave correctly

# VII. MVC
### Model:
1. Real world representation
2. Data and behavior
3. State
4. Respond requests about its state from view
5. Respond instructions to change state from controller
6. Notify

### View:
1. Render model
2. Update: Push from model or Pull from view

### Controller:
1. Receive user input (in a listener method, call appropriate method in contrller)
2. Initiate calls on appropriate model objects (using model's reference)
3. Instruct model to perform actions
4. One controller may associate with one view
5. May spawn new views

# VIII. Refactoring
**Defination**: A diciplined technique for restructuring existing code without changing its external behavior.
**When**: Continuously, as early as possible.
**Targets:** Bad smells and Anti-patterns
**Why**: Quality, productivity, sustainability, maintainability.
**How**: Behavior-preserving transformation, test-refactor-test.

### Examples:
1. Bring downcast to ahead:
   ``` Object lastReading(){return readings.lastElement()}``` => ```Readings lastReading(){return (Reading)readings.lastElement()}```
2. Consolidate conditional expression:
   ```
   if(condition 1) return 0
   if(condition 2) return 0
   if(condition 3) return 0
   ...
   ```
   To
   ```
   if(isNotEligible()) return 0
   ```
3. Consolidate duplicate conditional fragments:
   ```
   if(condition){
       //do sth
       send();
   } else {
       //do sth
       send()
   }
   ```
   To
   ```
   if(condition){
       //do sth
   } else {
       //do sth
   }
       send()
   ```
4. Rename methods to descriptive
5. Pull up field: If all sub-class have same field, pull it to super-class.
6. Push down methods: If only few of sub-classes has particular method, drag it down to sub-classes.

Refactorings in project:
1. Move command verification to controller
2. Move game phases from controller to player class
3. Revise commond verification to independent functions instead of many if() clauses.
4. Rename methods to be more descriptive
5. Re-orgnize the folder structure, separate controller, model, and views.
6. Move defend command enterring from player to command controller. Because denfend is following attack command closely, we put defend enterring in the player class, but in build3 we decide move it to controller in order to be consistant.

# IX. Design Patterns
> *Each pattern describes a problem which occurs over and over again, then describes the core of the solution — Christopher Alexander*

Solution does not describe a particular concrete implementation, it's more a template, prividing general arragement of elements.

**Three types:**
1. **Creational**: Create objects for you, instead of you instantiate directly.
   <ins>Factory pattern, Abstract factory pattern, Builder pattern, Prototype pattern, Singleton pattern.</ins>
2. **Structural**: Compose groups of objects into larger structures.
   <ins>Decorator, Adapter, Bridge, Coposite, Facade, Flyweight, Proxy</ins>
3. **Behavioral**: Define the communication between objects and how the flow is controlled.
   <ins>Observer, Strategy, TemplateMethod, etc.</ins>
   

## Creational:
Factory pattern, Abstract factory pattern, Builder pattern, Prototype pattern, Singleton pattern.

### Fatory Pattern
Return an instance of one of several possible classes depending on the data provided to it, instead of using **new**.
```
Interface Shape {
    draw();
}
Class Circle implements Shape{
    @override
    draw();
}
Class Square implements Shape{
    @override
    draw();
}
Class ShapeFactory {
    privite ShapeFactory(){}
    static public Shape getShape(String shape){
        if(shape.igonoreCaseEqual("circle"){
            return new Circle();
        }
        if(shape.igonoreCaseEqual("square"){
            return new Circle();
        }
        return null;
    }
}
Class Driver{
    public static void main(String[] args){
        Shape shape1 = ShapeFactory.getShape("circle");
        shape1.draw();
        Shape shape2 = ShapeFactory.getShape("square");
        shape2.draw();
    }
}
```
**Problem Context:**
1. Various kinds of objects are to be used.
2. A class uses a hierarchy(sub-class and its interface) of classes to specify which objects it creates.
3. A class can't anticipate the kind of objects it must create.
4. You want localize the knowledge of which class get created.

**Recognization:**
1. Base class is abstract or interface
2. Parameters are passed to tell which object to return
3. Sub-classes share same method but different implementation

### Builder pattern
Separate the construction of a complex object(products) from its representation, so that the same construction process can create different representations.

**Compositions:**
1. **Product**: Final object we want, it has different representations, each representation we create a concrete builder for it.
2. **Builder**: Abstract class or Interface for creating parts of a product, it has contruction methods(calling setters of products), 
3. **Concrete Builder**: Specific builder build specific product's representation.
4. **Director:** Define a specific construction plan(order) using builder's interface.
```
//Product
Class shelter{
    roof, structure, floor
    setters();
}
//Builder abstract class
Class ShelterBuilder{
    shelter, 
    Construction methods();
    getter();
}
//Concrete builder
Class PolarShelterBuilder{
    implemtation of construction methods(){
        shelter.setter();//polar
    };
}
Class TropicalShelterBuilder{
    implemtation of construction methods(){
        shelter.setter();//tropical
    };
}
//Director
Class Explore{
    builder,
    setBuilder(Builder builder);
    constructProduct(){
        builder.construction methods();
    }
    getter();
}
//Main
Class Main{
    main{
        Product product1, product2,
        Builder product1Builder = new TropicalShelterBuilder, 
        Builder product2Builder = new PolarShelterBuilder;

        Director explore = new Explore;

        explore.setBuilder(product1Builder);
        explore.constructProduct();
        product1 = explore.getter();

        explore.setBuilder(product2Builder);
        explore.constructProduct();
        product2 = explore.getter();
    }
}
```
**Problem Context:**
1. Creating a complex object(shelter) is independent of the parts(roof, structure, floor) and how they are assembled(director).
2. Construction process must allow different representations for the object to be constructed(director.setBuilder(concrete builder))
3. Those products have same structure and interface, eventhough their internal components are different(depend on the different concrete builders).
4. Providing construction abstraction(by director)

### Singleton pattern
***A gloable point of access***
- Private constructor
- Public and static getInstance

Implementations:
1. Lazy loading:(without synchronize)
   ```
    public class Singleton {  
        private static Singleton instance;  
        private Singleton (){}  
    
        public static Singleton getInstance() {  
        if (instance == null) {  
            instance = new Singleton();  
        }  
        return instance;  
        }  
    }
2. Lazy loading:(with synchronize)
   ```
   public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
    public static synchronized Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
3. Hunger loading:
   ```
   public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
4. double-checked locking
5. registery mode
6. Enum mode

## Structural:
Adapter, Composite, Facade, Brige, Decorator
### Adapter pattern
One client class D only know object A's interface, now we have object B porviding similar service, we want use object B without change methods of D or B, then we can create an adapter C, this adapter extends from A, override the methods(interface) of C, in the overrided method, call B's interface to achieve same functionality of this method.
A — Target
B — Adaptee
C — Adapter
D — Client
C is extending from A, bridging B and D.
**Example:**
```
//Class A
Class SquarePeg{
    insert();
}
//Class B
Class RoundPeg{
    insertHole();
}
//Adapter
Class SquareToRound extends SquarePeg{
    private RoundPeg roundPeg;
    SquareToRound(RoundPeg roundPeg){
        this.roundPeg = roundPeg;
    }
    insert(){
        roundPeg.insertHole();
    }
}
//Driver(client, only use squarePeg.insert())
Class AdapterDriver{
    main(){
        new SquarePeg squarePeg;
        new RoundPeg roundPeg;
        new SquareToRound squareToRound(roundPeg);
        //Using squarePeg interface insert() (no need adapter)
        squarePeg.insert();
        //Using roundPeg interface insertHole() through adapter
        squareToRound.insert();
    }
}
```
Above is "one-way" adapter, also we can create "two-way" adapter(Class adapter). Then we have roundPeg also squarePeg in adapter, and we have insert() method calling insertHole(), also insertHole() method calling insert() in "two-way" adapter.
```
//Adapter
Class TwoWayAdapter implements SquarePeg, RoundPeg{
    private RoundPeg roundPeg;
    private SquarePeg sqarePeg;
    //Two kinds of constructors
    public TwoWayAdapter(SquarePeg squarePeg){
        this.squarePeg = sqaurePeg;
    }
    public TwoWayAdapter(RoundPeg roundPeg){
        this.roundPeg = roundPeg;
    }
    insert(){
        roundPeg.insertHole();
    }
    insertHole(){
        squarePeg.insert();
    }
}
```
### Decorator pattern
**Motivation**: Add data member or methods to an object at runtime without changing original object.

**Compositions**:
1. Component(interface or abstract class)
2. Concrete component
3. Decorator(abstract class)
4. Concorete decorator

2, 3, 4, they actually are all components. Decorator implements or extends from component, concrete decorator extends from decorator.
For 3, it has a data member which is 1, so 3 is composed of 1.
```
//Component
Abstract class coffee{
    abstruct getCost();
    abstruct getIngredients();
}
//Concrete components
Class Espresso{
    getCost(){
        return 1
    }
}
Class SimpleCoffee{
    getCost(){
        return 1.25;
    }
}
//Decorator
abstract class Decorator extends Coffe{
    protected final Coffee decoratedCoffee;
    //This super-class is constructed with an "un-decoratedCoffee", in concrete decorator, we return decorated coffee's property.
    Decorator(Coffee decoratedCoffee){
        this.decoratedCoffee = decoratedCoffe;
    }
    getCost(){
        return decoratedCoffe.getCost()
    }
}
//Concrete decorator
class Milk extends Decorator{
    //implement constuctor
    Milk(Coffee decoratedCoffee){
        super(decoratedCoffee);
    }
    getCost(){
        return super.getCost + 0.5;
    }
}
//Driver
class DecoratorDriver{
    main(){
        Coffee c = new Espresso();
        c = new Milk(c);//decorating with milk
        c.getCost();
    }
}
```
## Behavioral:
### Observer pattern
**Motivation**: Certain objects need to be informed about the changes of other objects frequently.
One-to-Many
Cornerstone of MVC architeture
**Participants**:
1. Subject(Observerable)
2. Concrete subject **extends** Observerable(model)
3. Observer
4. Conctrete observer **implements** Observer(view)

### Strategy pattern
**Motivation**: Sometimes we want to change the behavior of an object depending on some conditions that are only to be determined at runtime.

**Participants**:
1. **Context class**: Contain a strategy object as a member. A setStrategy() method. A compute() method to call appropriate strategy.
2. **Strategy abstract class**: Super class of all strategies containing compute() method.
3. **Concrete strategies**: Provide different implementation for compute() method.
```
//Context class
class Calculator{
    **private** Strategy strategy;
    public setStrategy(Strategy strategy){
        this.strategy = strategy;
    }
    public executeStrategy(){
        strategy.execute();
    }
}
//Interface/abstract Strategy
interface Strategy{
    int execute(int a, int b);
}
//Concrete Strategies
class Adder implement Strategy{
    @override
    execute(int a, int b){
        return a + b;
    }
}
//Driver
class Driver{
    main(){
        Strategy adder = new Adder();
        Calculator calculator = new Calculator();
        calculator.setStrategy(adder);
        int result = calculator.execute(1, 2)
        print(result);
    }
}
```

# X. Exception Handling
Un-foreseen situation from:
External, Internal

**Defination**:
A mechanism, that allow two program components to communicate when un-foreseen situation.
**History**: error code and error handling based on error code.
Cons: 1. normal behavior mixed with error handling code. 2. return value mixed with error code.
**Exception**: A data structure
After an exception happen, exception handling mechanism take over the normal execution. If exception is properly resolved, normal execution is resumed. If not, non-deamon thread stop.

**Mechanism**:
- Throw statement halt the program, hand execution over to handling mechanism.
- One catch block has only one parameter, which is the exception or sub-type object thrown.
- Goes sequentially over the catch blocks till the first one matched.
- **"finally"** block is used to be executed whether or not exception was thrown.
- If exeption is resolved, code following finally block resumes execution.
- If no handling, thrown upto main, if main has no handler, program ends.

**Stack unwinding**: From exception happen to catching block(may be upper caller), all resources are released, collected. However, some operation cannot be recover, e.g. opened file. So to use finally block.
Besides, if exception happen in object constructor, some resource allocated may not be collected, result in leak.

The correct order of initialisation is:
1. Static variable initialisers and static initialisation blocks, in textual order, if the class hasn't been previously initialised.
2. The super() call in the constructor, whether explicit or implicit.
3. Instance variable initialisers and instance initialisation blocks, in textual order.
4. Remaining body of constructor after super().

**"throws" clause:**
Means the function will not handle this exception internally. Forcing any caller to either catch this exception, or itself declare it.
**"Handle or declare rule"** only applies to **checked exception**.
Throwable => 1. error 2. exception
Exception => 1. Runtime exception(un-checked, e.g. nullpointer, number format, etc.) 2. Checked exception

# XI. Java Generic
A language feature
Enable the defination of classes or methods those are using particular type, by accepting a **Type parameter: \<T>**

Problem of explicit casting: Compiler cannot find casting error.

Generic provide **compile-time type safety** and it **eliminate the need for explicit casts** when using type-abstract type and algorithm, e.g. Stack\<String>, Stack is a type-abstract structure, it stores "object", by generic, we can specify any type parameter, so this structure is independent from its context, and we don't have to use cast.

**Intention**: Define types and algorithms that apply in different contexts(different concrete type), so that this class can apply independently of the context of different concrete type.
<ins>Generic types are instantiated to be concrete type.</ins>

**Type erasure**: Translating generic class into executable code, checking type error, do casting, eliminating all generic tag (<>) type information at compile time.
ddad
Type bound can be any **reference type**(Integer), but not basic type(int). It gives access to the non-static method of the type bound(e.g. parseInt()).

**Wildcard:**
*Raw type VS wildcard:*
https://www.programcreek.com/2013/12/raw-type-set-vs-unbounded-wildcard-set/
If we don't use generic type, then it's raw type, we can use any type to do anything.
But if we use <?>, means certain type, but we don't know, if you want to use this method, you have to tell it(pass to it). If you don't tell me what type, you can do nothing with it. Therefore, <?>, <? extends someType>, and <? super someType> can only be used in parameter passing. E.g.
```
setMember(List<?> list){};
//calling above method
setMember(List<String> list);
```

In generic type declaration, we cannot use <?>, it dosn't make any sense.
```
class <? extends Number> SomeClass{}
```
Instead, we can use:
```
class <T extends Number> SomeClass{}
```
Then we can use T to be a type-parameter in this class.

But what's the difference of \<someType> and \<? extends someType>
Both means we can use someType or sub-someType, but second one you must tell me(pass to me) what is ?.

But what's the difference of \<T> and \<?> in parameter passing?
```
public setMember(List<?> list) 
VS
public <T> setMember(List<T> list)
```
Again, if we use \<T>, we can declare member of type T.
