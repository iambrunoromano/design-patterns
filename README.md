# Design Patterns

The purpose of this simple repository is dual:

1. track the Design Patterns I'm studying
2. provide a clear code-based explanation of the major Design Patterns

Each Design Pattern is briefly described and an example of it is presented. The language used is Java.

Methods implementation and attribute values are skip to leave the code readable.

# Creational Design Patterns

## Abstract Factory

The Abstract Factory design pattern provides and easy way to create families of related objects without specifying their
concrete classes.

<details>
  <summary>Click to know more about the Abstract Factory</summary>


Identify the first group of classes that behave in a similar way but differ for what they do represent:

```
class FirstClassFirstVersion{
    void doSomething(){}
}

class FirstClassSecondVersion{
    void doSomething(){}
}
```

Define then an interface with the common behaviours for the mentioned classes:

```
interface DoSomethingInterface{
    void doSomething();
}
```

and then let all the classes implement this interface:

```
class FirstClassFirstVersion implements DoSomethingInterface{
    @Override
    void doSomething(){
        System.out.println("New [First Version] doSomething method!");
    }
}

class FirstClassSecondVersion implements DoSomethingInterface{
    @Override
    void doSomething(){
        System.out.println("New [Second Version] doSomething method!");
    }
}
```

Identify the second group of classes that behave in a similar way but differ for what they do represent:

```
class SecondClassFirstVersion{
    void doSomethingElse(){}
}

class SecondClassSecondVersion{
    void doSomethingElse(){}
}
```

Define then an interface with the common behaviours for the mentioned classes:

```
interface DoSomethingElseInterface{
    void doSomethingElse();
}
```

and then let all the classes implement this interface:

```
class SecondClassFirstVersion implements DoSomethingElseInterface{
    @Override
    void doSomethingElse(){
        System.out.println("New [First Version] doSomethingElse method!");
    }
}

class SecondClassSecondVersion implements DoSomethingElseInterface{
    @Override
    void doSomethingElse(){
        System.out.println("New [Second Version] doSomethingElse method!");
    }
}
```

Now that all the classes that behave in the same way are grouped around common interfaces we can define a new interface
for the coming factory classes.

Namely the `AbstractFactory`:

```
interface FactoryInterface{
    DoSomethingInterface createDoSomething();
    DoSomethingElseInterface createDoSomethingElse();
}
```

We can now create several `Factories` that will take care of creating objects implementing `DoSomethingInterface`
or `DoSomethingElseInterface`:

```
class FirstVersionFactory{
    @Override
    public DoSomethingInterface createDoSomething() {
        return new FirstClassFirstVersion();
    }
    
    @Override
    public DoSomethingElseInterface createDoSomethingElse() {
        return new SecondClassFirstVersion();
    }
}

class SecondVersionFactory{
    @Override
    public DoSomethingInterface createDoSomething() {
        return new FirstClassSecondVersion();
    }
    
    @Override
    public DoSomethingElseInterface createDoSomethingElse() {
        return new SecondClassSecondVersion();
    }
}
```

Since the two factories `FirstVersionFactory` and `SecondVersionFactory` implement the same interaface, they provide an
interchangeable and loose coupled way of creating objects implementing `DoSomethingInterface`
or `DoSomethingElseInterface`.
</details>

## Builder

The Builder design pattern provides a simple step-by-step process to construct complex related objects hiding the
construction process to the client code.

<details>
  <summary>Click to know more about the Builder</summary>


Identify the classes which requires numerous step-by-step initialization of fields and nested objects and the relative
long constructor:

```
class FirstClass {
    private int firstField;
    private int secondField;
    private int thirdField;
    ...
}

class SecondClass {
    private int firstField;
    private int secondField;
    private int thirdField;
    ...
}
```

Create a new `Builder` interface that declares all the methods that all the single builder classes will need to provide
their own implementation of:

```
interface Builder {
    void reset();
    void firstField(FirstField firstField);
    void secondField(SecondField secondField);
    void thirdField(ThirdField thirdField);
    ...
}
```

As you may notice the `Builder` interface provides methods useful during both construction of `FirstClass`
and `SecondClass` objects.

Create the new builder classes that implement the building steps for the single chosen (product - `FirstClass`
and `SecondClass`) class:

```
class FirstBuilder implements Builder {
    private FirstClass firstClass;
    
    private FirstField firstField;
    private SecondField secondField;
    private ThirdField thirdField;
    ...
    
    @Override
    void reset() {
        this.firstField = null;
        this.secondField = null;
        this.thirdField = null;
    }
    
    @Override
    void firstField(FirstField firstField) {
        this.firstField = firstField;        
    }
    
    @Override
    void secondField(SecondField secondField) {
        this.secondField = secondField;
    }
    
    @Override
    void thirdField(ThirdField thirdField) {
        this.thirdField = thirdField;
    }
    
    ...
    
    FirstClass build() {
        return new FirstClass(firstField,secondField,thirdField,...);
    }
}

class SecondBuilder implements Builder {
    private SecondClass secondClass;
    
    private FirstField firstField;
    private SecondField secondField;
    private ThirdField thirdField;
    ...
    
    @Override
    void reset() {
        this.firstField = null;
        this.secondField = null;
        this.thirdField = null;
    }
    
    @Override
    void firstField(FirstField firstField) {
        this.firstField = firstField;        
    }
    
    @Override
    void secondField(SecondField secondField) {
        this.secondField = secondField;
    }
    
    @Override
    void thirdField(ThirdField thirdField) {
        this.thirdField = thirdField;
    }
    
    ...
    
    SecondClass build() {
        return new SecondClass(firstField,secondField,thirdField,...);
    }
}
```

Create then a new `Director` class that, given a `Builder`-implementing class, delegates the construction according the
chosen recipe:

```
class Director {    
    public void buildFirstRecipe(Builder builder) {
        builder.firstField(new FirstField());
        builder.secondField(new SecondField());
        builder.thirdField(new ThirdField());
    }
    
    
    public void buildSecondRecipe(Builder builder) {
        builder.secondField(new SecondField());
        builder.firstField(new FirstField());
        builder.thirdField(new ThirdField());
    }
}
```

Now we can finally decouple the client code from the recipe used to create an object of class `FirstClass`
and `SecondClass`:

```
class Demo {
    public static void main(String[] args) {
        Director director = new Director();
        FirstBuilder firstBuilder = new FirstBuilder();
        director.buildFirstRecipe(firstBuilder);
        FirstClass firstClass = firstBuilder.build();
    }
}
```

If you want now to use another recipe you can just change one line of code of what you just read, completely decoupling
the recipe needed to build something from the actual builder:

```
class Demo {
    public static void main(String[] args) {
        Director director = new Director();
        FirstBuilder firstBuilder = new FirstBuilder();
        director.buildSecondRecipe(firstBuilder);
        FirstClass firstClass = firstBuilder.build();
    }
}
```

If you want instead to build another type of object but maintaing the same `firstRecipe` you can change only the used
builder:

```
class Demo {
    public static void main(String[] args) {
        Director director = new Director();
        SecondBuilder secondBuilder = new SecondBuilder();
        director.buildFirstRecipe(secondBuilder);
        SecondClass secondClass = secondBuilder.build();
    }
}
```

In this way the steps are completely decoupled from the managing of them during the construction phase, therefore a
change in the steps' body or recipe's body is hidden with respect to the client code.

</details>

## Factory Method

The Factory method design pattern provides an easy introduction for new classes of objects that offer similar
functionality with respect to the previous ones.

<details>
  <summary>Click to know more about the Factory Method</summary>

Imagine you use a specific class with specific methods all over your codebase:

```
class FirstProductClass {
    void doSomething(){};
}

class SecondProductClass {
    void doSomething(){};
}
```

Substitution of the `FirstProductClass` with the `SecondProductClass` could require extensive code refactor.

To avoid this problem one could define a `Product` interface that classes like the latter have to implement:

```
interface Product 
    void doSomething();  
}

class FirstProductClass implements Product {
    @Override
    void doSomething() {
        System.out.println("New [First Product Class] doSomething method!");
    };
}
  
class SecondProductClass implements Product {
    @Override
    void doSomething() {
        System.out.println("New [Second Product Class] doSomething method!");
    };
}
```

Now create an abstract `Factory` class that will be extended by the specific `ConcreteFactory` required for each
product:

```
abstract class Factory {
    void someOtherMethodWithCommonImplementation(){
        //do something
    }
    abstract Product createProduct();
}
```

Create then the concrete factories extending the `Factory` class.

Because of the `abstract` method seen, the `ConcreteFactory` classes will need to specify their own implementation of
the method `abstract Product createProduct();`:

```
class FirstConcreteFactory extends Factory {
    @Override
    Product createProduct() {
        return new FirstProductClass();
    }
}
  
class SecondConcreteFactory extends Factory {
    @Override
    Product createProduct() {
        return new SecondProductClass();
    }
}
```

Client code should then use only the interface `Product` to refer any of the products and the class `Factory` to refer
to any factor.

In this way the change between factories and therefore products created is seamless and the codebase is always ready for
the introduction of a new `Factory` or `Product`:

```
class Demo {
    private Factory factory;
    private Product product;
    public static void main(String[] args) {
        this.factory = new FirstConcreteFactory();
        this.product = this.factory.createProduct();
    }
}
```

If I want to use `SecondConcreteFactory` and therefore create a new object of class `SecondProductClass` I will need to
change just one line of the latter code:

```
class Demo {
    private Factory factory;
    private Product product;
    public static void main(String[] args) {
        this.factory = new SecondConcreteFactory();
        this.product = this.factory.createProduct();
    }
}
```

</details>

## Prototype

The Prototype design pattern delegates the creation of an exact copy (private fields included) of an existing object to
the object itself, decoupling the client code from the realization of the copy.

<details>
  <summary>Click to know more about the Prototype</summary>

An object supporting the creation of its exact copy (namely `cloning`) is called `prototype`.

Let's define an abstract class that represents the common interface we want to give all the cloneable objects:

```
abstract class Cloneable {
    public int firstField;
    ...
    public Cloneable () {}
    public Cloneable (Cloneable target) {
        if (target != null) {
            this.firstField = target.firstField;
            ...
        }
    }
    public abstract Cloneable clone();   
}
```

Let's define then some cloneable object classes:

```
class FirstCloneableClass extends Cloneable {
    public FirstCloneableClass (FirstCloneableClass target) {
        if (target != null) {
            ...
        }
    }
    @Override
    public FirstCloneableClass clone() {
        return new FirstCloneableClass(this);
    }
}

class SecondCloneableClass extends Cloneable {
    public SecondCloneableClass (SecondCloneableClass target) {
        if (target != null) {
            ...
        }
    }
    @Override
    public SecondCloneableClass clone() {
        return new SecondCloneableClass(this);
    }
}
```

In this way we can use the `Cloneable` abstract class to declare variables. Each one will be able to properly clone
itself:

```
class Demo {
    public static void main(String[] args) {
        Cloneable firstCloneable = new FirstCloneableClass();
        // set here values of firstCloneable fields
        Cloneable secondCloneable = new SecondCloneableClass();
        // set here values of secondCloneable fields
        Cloneable anotherCloneable = firstCloneable.clone(); 
        // anotherCloneable is now a clone of class FirstCloneableClass
        anotherCloneable = secondCloneable.clone(); 
        // anotherCloneable is now a clone of class SecondCloneableClass 
    }
}
```

</details>

## Singleton

The Singleton design pattern guarantees that a specific class has only one instance letting the client code to access
only to this instance.


<details>
  <summary>Click to know more about the Singleton</summary>

The Singleton design pattern is defined by two main characteristics.

1. have on the class that should have only one instance a private constructor
2. the class should implement some static method that evaluates if to call the private constructor

```
class Singleton {
    private static Singletong instance;
    private int value;
    private Singleton(int value) {
        this.value = value;
    }
    public static Singleton getInstance(int value) {
        if(instance==null) {
            return new Singleton(value);
        }
        return instance;
    }
}
```

</details>

# Structural Design Patterns

## Adapter

The Adapter design pattern allows two objects with different interfaces to work together.

<details>
  <summary>Click to know more about the Adapter</summary>

The Adapter converts the interface of an object in a way another object can work with that.

Let's suppose we have a class that represents an unmodifiable system which requires to execute some `doSomething` method
of the passed object to expose some functionality:

```
class Closed { // some class closed to modification
    void exposeFunctionality(ExpectedClass expected) {
        expected.doSomething();
    }
}
```

The `Expected` will therefore be:

```
class Expected {
    public void doSomething() {
    }
}
```

Let's imagine now we have another class named `Difficult` we want to let the `Closed` class use that:

1. doesn't expose a `doSomething` method
2. is closed to modification

```
class Difficult {
    public void firstMethod() {
    }
    public void secondMethod() {
    }
    public void thirdMethod() {
    }
    ...
}
```

The `Adapter` will therefore be:

```
class Adapter extends Expected {
    private Difficult difficult;
    public Adapter(Difficult difficult) {
        this.difficult = difficult;
    }
    @Override
    public void doSomething() {
        // let's suppose that the doSomething method can be logically equivalent
        // to a combination of some methods of the Difficult class
        difficult.firstMethod();
        difficult.secondMethod();
        difficult.thirdMethod();
        ...
    }
}
```

The client code can therefore be using the `Difficult` class with the `Closed` class:

```
class Demo {
    public static void main(String[] args) {
        Closed closed = new Closed();
        Expected expected = new Expected();
        Difficult difficult = new Difficult();
        Adapter adapter = new Adapter(difficult);
        closed.exposeFunctionality(expected); // as it is meant to be
        closed.exposeFunctionality(adapter); // using the adapter indirectly using difficult
    }
}
```

</details>

## Bridge

The Bridge design pattern allows to split a closely correlated set of classes into separate hierarchies named abstraction and implementation that can be develop indipendently from each other.

<details>
  <summary>Click to know more about the Bridge</summary>
  
Imagine you have a set of classes that should perform some actions based on given inputs:

```
class DoesSomethingByString {
    public void doSomething(String input) {
        reallyDoIt(input);
    }
    
    private void reallyDoIt(String input){}
}
```

Imagine now you want another class that should do the exact same thing based on some other input type:

```
class DoesSomethingByInteger {
    public void doSomething(Integer input) {
        reallyDoIt(input.toString());
    }
    
    private void reallyDoIt(String input){}
}
```

As you can see, each time we want to add use use the same feature `reallyDoIt(String input)` we need to create a new class.

This happens because we do not separate the `abstraction` (the interface with the external world of the chosen class, in our cases the methods: `doSomething(String input)` and `doSomething(Integer input)doSomething(Integer input)`) from the `implementation` (the actually exposed chosen class functionality, the method  `reallyDoIt(String input)`).

To separate abstraction from implementation one has to declare two separate interfaces:

```
interface Implementation {
    void firstFeature();
    void secondFeature(); 
    ...
}

interface Abstraction {
    void firstInputMethod();
    void secondInputMethod(); 
    ...
}
```

In this way we can have several classes that implement the business logic of the `Implementation`:

```
class FirstImplementation implements Implementation {
    @Override
    void firstFeature() {
        System.out.println("firstFeature implementation of FirstImplementation class")
    }
    
    @Override
    void secondFeature() {
        System.out.println("secondFeature implementation of FirstImplementation class")
    }; 
}

class SecondImplementation implements Implementation {
    @Override
    void firstFeature() {
        System.out.println("firstFeature implementation of SecondImplementation class")
    }
    
    @Override
    void secondFeature() {
        System.out.println("secondFeature implementation of SecondImplementation class")
    }; 
}
```
 
In the same way we can have several classes that implement the interface logic of the `Abstraction`:

```
class FirstAbstraction implements Abstraction {
    private Implementation implementation;
    
    FirstAbstraction(Implementation implementation) {
        this.implementation = implementation;
    }
    
    @Override
    void firstInputMethod() {
        System.out.println("firstInputMethod implementation of FirstAbstraction class")
    }
    
    @Override
    void secondInputMethod() {
        System.out.println("secondInputMethod implementation of FirstAbstraction class")
    }; 
}

class SecondAbstraction implements Abstraction {
    private Implementation implementation;
    
    SecondAbstraction(Implementation implementation) {
        this.implementation = implementation;
    }
    
    @Override
    void firstInputMethod() {
        System.out.println("firstInputMethod implementation of SecondAbstraction class")
    }
    
    @Override
    void secondInputMethod() {
        System.out.println("secondInputMethod implementation of SecondAbstraction class")
    }; 
}
```

As one may notice the abstraction layer needs the specific implementation to be provided, allowing to decouple the two layers one from the other. The consequence is that each abstraction class is instanciable with every implementation class and introduction of new abstraction-implementation couples remains clean and implies little pre-existing code modifications:


```
class Demo {
    public static void main(String[] args) {
        FirstImplementation firstImplementation = new FirstImplementation();
        SecondImplementation secondImplementation = new SecondImplementation();
        FirstAbstraction firstAbstraction = new FirstAbstraction(firstImplementation); //remains easy to instatiate corresponding abstraction-implementation couples
        SecondAbstraction secondAbstraction = new SecondAbstraction(secondImplementation);
        FirstAbstraction firstAbstraction = new FirstAbstraction(secondImplementation); //it's easy to instatiate new abstraction-implementation couples
        SecondAbstraction secondAbstraction = new SecondAbstraction(firstImplementation);
    }
}
```

Without the use of the Bridge design pattern we should have a class for each new couple of abstraction-implementation, therefore the instation of `new FirstAbstraction(secondImplementation)` and `new SecondAbstraction(firstImplementation)` would require the definition of two new classes.

</details>

## Composite


<details>
  <summary>Click to know more about the Composite</summary>
</details>

## Decorator

## Facade

## Flyweight

## Proxy

# Behavioral Design Patterns

## Chain of Responsibility

## Command

## Iterator

## Mediator

## Memento

## Observer

## State

## Strategy

## Template Method

## Visitor

Credits to [Refactoring Guru's design patterns](https://refactoring.guru/design-patterns)
