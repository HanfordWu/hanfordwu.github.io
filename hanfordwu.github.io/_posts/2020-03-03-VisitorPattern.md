##### Visitor pattern summary:
Context:
- When you have an objects structure, you want to do many distinct and unrelated operations need to be performed on objects.
- The classes defining the object structure rarely change, but you often want to define new operations.

Naive way is to add different methods on each class, once you have new operation on object, you add or change it in each class. This is not good practice.

**Visitor pattern:**
Operations are moved out of the object. Have each class accept a visitor, visitor can do operations on "this" object. Operations can be defined in different way, can be extended.
In other words, visitor is used for collecting objects' information, so that we can make use of these information to do any operation (after we visit all object).
```java
class Class {
    private Set<Method> methods;
    private Set<Felids> fields;
    public void accept(Visitor visitor){
        visitor.open(this);
        forEach(methods).accept(visitor);
        forEach(fields).accept(visitor);
        visitor.close(this);
    }
}
class Method {
    private Set<Statement> statements;
    public void accept(Visitor visitor){
        visitor.open(this);
        forEach(statements).accept(visitor);
        visitor.close(this);
    }
}
class Filed {
    public void accept(Visitor visitor){visitor.visit(this)}
}
class Statement {
    public void accept(Visitor visitor){visitor.visit(this)}
}
Interface Visitor {
    public void open(Class class);
    public void open(Method method);
    public void visit(Field field);
    public void visit(Statement statement);
    public void close(Class class);
    public void close(Method method);
}
```
Essentially, visitor pattern decouples data structure and algorithms. It allows to add new algorithms without change the data structure.