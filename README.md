# Design Patterns

The purpose of this simple repository is dual:

1. track the Design Patterns I'm studying
2. provide a clear code-based explanation of the major Design Patterns

Each Design Pattern is briefly described and an example of it is presented. The language used is Java.

Methods implementation and attribute values are skip to leave the code readable.

# Creational Design Patterns

Creational design patterns allow several object creation mechanisms that increase flexibility and code reuse.

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

Structural design patterns allow objects and classes assembling while keeping the resulting structures flexibles and efficient.  

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

The Composite design pattern allows the creation of objects in a tree-like structure allowing to work on the tree as it was a singular object.

<details>
  <summary>Click to know more about the Composite</summary>

Imagine that the business logic requires a tree structure of classes where we can have simple nodes and composite nodes. 

Simple nodes can't have subelements while composite nodes can, and those can be simple or composite nodes.

Let's define an interface for the generic node classes:

```
interface Node {
    void firstMethod();
    void secondMethod();
}
```

Let's define some simple node classes:

```
class FirstNode implements Node {
    @Override
    void firstMethod() {
        System.out.println("firstMethod implementation of FirstLeaf class")
    }
    
    @Override
    void secondMethod() {
        System.out.println("secondMethod implementation of FirstLeaf class")
    };
}

class SecondNode implements Node {
    @Override
    void firstMethod() {
        System.out.println("firstMethod implementation of SecondLeaf class")
    }
    
    @Override
    void secondMethod() {
        System.out.println("secondMethod implementation of SecondLeaf class")
    }; 
}
```

We declare now a new class for the composite node:

```
class Composite implements Node {
    protected List<Node> children = new ArrayList<>();
    
    void add(Node node) {
        children.add(node);
    }

    void add(Node... nodes) {
        children.addAll(Arrays.asList(nodes));
    }

    void remove(Node node) {
        children.remove(node);
    }

    void remove(Node... nodes) {
        children.removeAll(Arrays.asList(components));
    }

    void clear() {
        children.clear();
    }
    
    @Override
    void firstMethod() {
        for(Node node: children) {
            node.firstMethod();
        }
    }    
    
    @Override
    void secondMethod() {
        for(Node node: children) {
            node.secondMethod();
        }
    }
}
```

As one may notice the Composite class allows insertion and remotion of child subelements.

Also, as `Composite` class implements `Node` class, we can add other composite nodes to the list of children of another composite node, creating in this way a hierarchy tree.

Third and most important feature: with the override of both `firstMethod` and `secondMethod` from the `Composite` class we can easily propagate the execution of both methods from the root node (which must be a composite node) to all the composite and simple nodes down the tree. In this way we can deal with the whole hierarchy of objects as one single object iself.

</details>

## Decorator
  
The Decorator design pattern allows behaviour attaching to an object by placing it into a special wrapper object.

<details>
  <summary>Click to know more about the Decorator</summary>

  Let's define an interface that declares the methods we want all the useful classes to implement:
  
```
interface DoSomething {
    void firstMethod();
    void secondMethod();
}
```
  
Therefore a simple class implementing such an interface should be:

```
class BaseClass implements DoSomething {
    @Override
    void firstMethod() {
        System.out.println("firstMethod implementation of BaseClass class");
    }
    
    @Override
    void secondMethod() {
        System.out.println("secondMethod implementation of BaseClass class");    
    }
}
```

Instead a BaseDecorator class will possess a field of type DoSomething and will implement DoSomething so it can have the same interface of DoSomething objects to the external world while using an object with the same interface to accomplish the assigned tasks:
  
```
class BaseDecorator implements DoSomething {
    private DoSomething doSomething;
  
    public BaseDecorator(DoSomething doSomething) {
        this.doSomething = doSomething;
    }
    
    @Override
    void firstMethod() {
        doSomething.firstMethod();
    }
    
    @Override
    void secondMethod() {
        doSomething.secondMethod();    
    }
}
```
  
We can now create some other decorator that will perform as well as BaseDecorator but exteding the behaviours of the overriden firstMethod and secondMethod:
  
```
class OtherDecorator implements DoSomething {
    private DoSomething doSomething;
  
    public OtherDecorator(DoSomething doSomething) {
        this.doSomething = doSomething;
    }
    
    @Override
    void firstMethod() {
        extendFirstMethod();
        doSomething.firstMethod();
    }
    
    @Override
    void secondMethod() {
        extendSecondMethod();
        doSomething.secondMethod();    
    }
  
    void extendFirstMethod() {
        System.out.println("Exteding firstMethod behaviour");
    }
  
    void extendSecondMethod() {
        System.out.println("Exteding secondMethod behaviour");
    }
}
```

As one may notice the client code can easily use the BaseClass, the BaseDecorator or the OtherDecorator as all implementing the DoSomething interface:

```
class Demo {
    public static void main(String[] args) {
        DoSomething base = new BaseClass();
        base.firstMethod(); // only executing BaseClass' firstMethod implementation
        BaseDecorator baseDecorator = new BaseDecorator(base);
        baseDecorator.firstMethod(); // only executing BaseClass' firstMethod implementation
        OtherDecorator otherDecorator = new OtherDecorator(baseDecorator);
        otherDecorator.firstMethod(); // executing extendFirstMethod and then BaseClass' firstMethod implementation
    }
}
```
  
Extension of BaseDecorator or OtherDecorator behaviour is therefore decoupled from the existing funcionalities: the introduction of a new decorator needs to take care only about the introduction of new functionalities. The use of the decorator pattern allows than to attach the defined functionality to the chosen DoSomething implemeting class: this reduces the number of defined classes in cases where we may need different combinations of several functionalities.

</details>

## Facade
  
The Facade design pattern provides a simplified interface to an existing piece of code which may consist of a complex set of classes.

<details>
  <summary>Click to know more about the Facade</summary>

The design pattern is very straighfoward. Image you have a set of classes that expose functionalities that might be used together to achieve a certain goal:
  
```
class FirstClass {
  ...
}
  
class SecondClass {
  ...
}
  
class ThirdClass {
  ...
}
```
  
Instead of letting the client code compose the functionalities exposed by those classes to achieve the desired goal, expose to client code a single entrypoint that will take care to execute all the necessary steps to achieve the defined goal:
  
```
class FacadeClass {
  public void achieveGoal() {
      // do here all the required steps using FirstClass, SecondClass, ThirdClass
  }
  ...
}
```

This design pattern simply forces the producer of a certain piece of code to expose in a simple and managed way all the functionalities that may be interesting for the client, discouraging the delivery of code as a set of separate components which have to be used and managed on the client side.

</details>

## Flyweight

The Flyweight design pattern allows more objects to fit in a defined amount of memory by sharing common parts of internal state.

<details>
  <summary>Click to know more about the Flyweight</summary>

Objects usually have internal state divided in intrisic and extrinsic state. The intrinsic state is a constant set of data that can't be changed but only read. The rest of object state, that can be read and altered from outside, is called the extrinsic state.
  
The Flyweight design pattern optimizes memory usage by collecting the intrinsic state of several objects into a collection of few objects called flyweights that are then referenced by the several objects holding the extrinsic state.

Imagine we have a class with some fields representing the intrinsic state of some other class objects:

```
class Intrinsic {
    private String firstField;
    private String secondField;
    ...
}
```

Imagine we have a class with some fields representing the extrinsic state:
  
```
class Extrinsic {
    private String fieldOne;
    private String fieldTwo;
    ...
    private Intrinsic intrinsic;
    ...
}
```
  
As one may notice the Extrinsic class contains an attribute to the intrinsic one. In this way each Extrinsic object will reference one of the few Intrinsic ones, and eahc time a new intrinsic one is needed it will be created and stored without duplicates:
  
```
class IntrinsicFactory {
    static Map<String,Intrinsic> intrinsicTypes = new HashMap<>();
    static Intrinsic getIntrinsic(String firstField, String secondField,String fieldOne, String fieldTwo) {
        Intrinsic intrinsic = intrinsicTypes.get(firstField);
        if(intrinsic==null) {
            intrinsic = new Intrinsic(firstField, secondField);
            intrinsicTypes.put(firstField, intrinsic);
        }
        return intrinsic;
    }
}
```

Using the IntrinsicFactory the client code can always memorize and control the intrinsic states, with the condition of being one for each possible value of `firstField`. Therefore, since Intrinsic objects are referenced in Extrinsic ones, the memory used will be shared across the latter.
  
</details>

## Proxy
  
The Proxy design pattern provides an object acting as a substitute for a real service introducing new functionalities that can be used before or after the service use.

<details>
  <summary>Click to know more about the Proxy</summary>
  
A Proxy class can solve the tedious problem of code repetition. Imagine you have some service needing complex initilization before its usage:
  
```
class Service {
  
    void initFirstStep() {
        // do first step of the initialization
    }
  
    void initSecondStep() {
        // do second step of the initialization
    }
  
    void initThirdStep() {
        // do third step of the initialization
    }
    ...
  
    void doSomethingFirst () {
        // do something first
    }
  
    void doSomethingSecond () {
        // do something second
    }
}
```
  
Imagine you need to explicitly call all the init step methods before using the two methods `doSomethingFirst` and `doSomethingSecond` which are the real service functionalities implementation. In such a case the risk is to duplicate and scatter the initialization code all over the code base, each time client code needs to use either `doSomethingFirst` or `doSomethingSecond`.

An easy solution to this problem is to create an interface that is specifically thought for the service's functionalities:
  
```
interface DoSomething {  
    void doSomethingFirst ();  
    void doSomethingSecond ();
}
```
  
If our `Service` implements DoSomething we can override the two functionality methods:
  
```
class Service implements DoSomething {  
    
    void initFirstStep() {
        // do first step of the initialization
    }
    ...
  
    @Override
    void doSomethingFirst () {
          // do something first
    }
  
    @Override
    void doSomethingSecond () {
        // do something second
    }
}
```
  
Therefore our proxy class shoud be implementing the same `DoSomething` interface but dealing with the nasty service initiliazion each time it is required:
  
```
class ServiceProxy implements DoSomething {
    private Service service;
  
    public ServiceProxy(Service service){
        this.service = service;
    }
  
    @Override
    void doSomethingFirst () {
        service.initFirstStep();
        service.initSecondStep();
        service.initFirstStep();
        service.doSomethingFirst();
    }
  
    @Override
    void doSomethingSecond () {
        service.initFirstStep();
        service.initSecondStep();
        service.initFirstStep();
        service.doSomethingSecond();
    }
}
```
  
In this way the client code can easily use the ServiceProxy as it was the Service itself:
  
```
class Demo {
    public static void main(String[] args) {
        DoSomething service = new Service();
        DoSomething serviceProxy = new ServiceProxy(service);
        serviceProxy.doSomethingFirst(); // in this way we execute doSomethingFirst from service not taking care to explicitly init the service itself
    }
}
```

</details>

# Behavioral Design Patterns
  
Behavioral design patterns take care of dealing with algorithms and responsibilities between different objects.

## Chain of Responsibility

The Chain of Responsibility design pattern allows requests passing along a chain of potential handlers until one of them handles the request.

<details>
  <summary>Click to know more about the Chain of Responsibility</summary>
  
The chain of handlers decouples the request sender class from the concrete receivers classes. Moreover, the chain can be dynamically composed at runtime.
  
To define an handler we need two main methods: a `link` method to link the handler to its next one and a `check` method to handle the request:
  
```
abstract class Handler {
    private Handler nextHandler;
    
    public static Handler link(Handler firstHandler, Handler... chain) {
        Handler head = firstHandler;
        for (Handler nextHandler : chain) {
            head.nextHandler = nextHandler;
            head = nextHandler;
        }
        return firstHandler;
    }
  
    public abstract boolean check(String someInput);
  
    protected boolean checkNext(String someInput) {
        if (nextHandler == null) {
            return true;
        }
        return next.check(someInput);
    }
}
```
  
As one may notice the `link` method allows dynamic handler linking and head moving. The `check` method implementation is instead delegated to the specific child handler class. The `checkNext` method instead propagates the check along the chain.  
  
A specific handler class may be:
  
```
abstract class SpecificHandler extends Handler {  
    public abstract boolean check(String someInput) {
        if(someInput.length()>255){
          return false;
        }
        return checkNext(someInput);
    }
}
```
  
The `checkNext` method to propagate the request handling can be inserted at the beginning or at the end of the `check` method. This allows an handler along the chain to change the order of checks.
  
    
```
class Demo {
    public static void main(String[] args) {
        Handler handler = Handler.link(new SpecificHandler(), new SpecificHandler()); // setting up the chain
        handler.check("test"); // triggering check responsibility chain
    }
}
```

</details>

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
