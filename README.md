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


Identify the classes which require numerous step-by-step initialization of fields and nested objects and the relative
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
    private static Singleton instance;
    private int value;
    private Singleton(int value) {
        this.value = value;
    }
    public static Singleton getInstance(int value) {
        if(instance==null) {
            instance = new Singleton(value);
        }
        return instance;
    }
}
```

</details>

# Structural Design Patterns

Structural design patterns allow objects and classes assembling while keeping the resulting structures flexibles and
efficient.

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

The Bridge design pattern allows to split a closely correlated set of classes into separate hierarchies named
abstraction and implementation that can be develop indipendently from each other.

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

As you can see, each time we want to add use use the same feature `reallyDoIt(String input)` we need to create a new
class.

This happens because we do not separate the `abstraction` (the interface with the external world of the chosen class, in
our cases the methods: `doSomething(String input)` and `doSomething(Integer input)`) from
the `implementation` (the actually exposed chosen class functionality, the method  `reallyDoIt(String input)`).

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
    }
}

class SecondImplementation implements Implementation {
    @Override
    void firstFeature() {
        System.out.println("firstFeature implementation of SecondImplementation class")
    }
    
    @Override
    void secondFeature() {
        System.out.println("secondFeature implementation of SecondImplementation class")
    }
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

As one may notice the abstraction layer needs the specific implementation to be provided, allowing to decouple the two
layers one from the other. The consequence is that each abstraction class is instanciable with every implementation
class and introduction of new abstraction-implementation couples remains clean and implies little pre-existing code
modifications:

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

Without the use of the Bridge design pattern we should have a class for each new couple of abstraction-implementation,
therefore the instation of `new FirstAbstraction(secondImplementation)` and `new SecondAbstraction(firstImplementation)`
would require the definition of two new classes.

</details>

## Composite

The Composite design pattern allows the creation of objects in a tree-like structure allowing to work on the tree as it
was a singular object.

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

Also, as `Composite` class implements `Node` class, we can add other composite nodes to the list of children of another
composite node, creating in this way a hierarchy tree.

Third and most important feature: with the override of both `firstMethod` and `secondMethod` from the `Composite` class
we can easily propagate the execution of both methods from the root node (which must be a composite node) to all the
composite and simple nodes down the tree. In this way we can deal with the whole hierarchy of objects as one single
object iself.

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

Instead a BaseDecorator class will possess a field of type DoSomething and will implement DoSomething so it can have the
same interface of DoSomething objects to the external world while using an object with the same interface to accomplish
the assigned tasks:

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

We can now create some other decorator that will perform as well as BaseDecorator but exteding the behaviours of the
overriden firstMethod and secondMethod:

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

As one may notice the client code can easily use the BaseClass, the BaseDecorator or the OtherDecorator as all
implementing the DoSomething interface:

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

Extension of BaseDecorator or OtherDecorator behaviour is therefore decoupled from the existing funcionalities: the
introduction of a new decorator needs to take care only about the introduction of new functionalities. The use of the
decorator pattern allows than to attach the defined functionality to the chosen DoSomething implemeting class: this
reduces the number of defined classes in cases where we may need different combinations of several functionalities.

</details>

## Facade

The Facade design pattern provides a simplified interface to an existing piece of code which may consist of a complex
set of classes.

<details>
  <summary>Click to know more about the Facade</summary>

The design pattern is very straighfoward. Image you have a set of classes that expose functionalities that might be used
together to achieve a certain goal:

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

Instead of letting the client code compose the functionalities exposed by those classes to achieve the desired goal,
expose to client code a single entrypoint that will take care to execute all the necessary steps to achieve the defined
goal:

```
class FacadeClass {
  public void achieveGoal() {
      // do here all the required steps using FirstClass, SecondClass, ThirdClass
  }
  ...
}
```

This design pattern simply forces the producer of a certain piece of code to expose in a simple and managed way all the
functionalities that may be interesting for the client, discouraging the delivery of code as a set of separate
components which have to be used and managed on the client side.

</details>

## Flyweight

The Flyweight design pattern allows more objects to fit in a defined amount of memory by sharing common parts of
internal state.

<details>
  <summary>Click to know more about the Flyweight</summary>

Objects usually have internal state divided in intrisic and extrinsic state. The intrinsic state is a constant set of
data that can't be changed but only read. The rest of object state, that can be read and altered from outside, is called
the extrinsic state.

The Flyweight design pattern optimizes memory usage by collecting the intrinsic state of several objects into a
collection of few objects called flyweights that are then referenced by the several objects holding the extrinsic state.

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

As one may notice the Extrinsic class contains an attribute to the intrinsic one. In this way each Extrinsic object will
reference one of the few Intrinsic ones, and eahc time a new intrinsic one is needed it will be created and stored
without duplicates:

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

Using the IntrinsicFactory the client code can always memorize and control the intrinsic states, with the condition of
being one for each possible value of `firstField`. Therefore, since Intrinsic objects are referenced in Extrinsic ones,
the memory used will be shared across the latter.

</details>

## Proxy

The Proxy design pattern provides an object acting as a substitute for a real service introducing new functionalities
that can be used before or after the service use.

<details>
  <summary>Click to know more about the Proxy</summary>

A Proxy class can solve the tedious problem of code repetition. Imagine you have some service needing complex
initilization before its usage:

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

Imagine you need to explicitly call all the init step methods before using the two methods `doSomethingFirst`
and `doSomethingSecond` which are the real service functionalities implementation. In such a case the risk is to
duplicate and scatter the initialization code all over the code base, each time client code needs to use
either `doSomethingFirst` or `doSomethingSecond`.

An easy solution to this problem is to create an interface that is specifically thought for the service's
functionalities:

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

Therefore our proxy class shoud be implementing the same `DoSomething` interface but dealing with the nasty service
initiliazion each time it is required:

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

The Chain of Responsibility design pattern allows requests passing along a chain of potential handlers until one of them
handles the request.

<details>
  <summary>Click to know more about the Chain of Responsibility</summary>

The chain of handlers decouples the request sender class from the concrete receivers classes. Moreover, the chain can be
dynamically composed at runtime.

To define an handler we need two main methods: a `link` method to link the handler to its next one and a `check` method
to handle the request:

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

As one may notice the `link` method allows dynamic handler linking and head moving. The `check` method implementation is
instead delegated to the specific child handler class. The `checkNext` method instead propagates the check along the
chain.

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

The `checkNext` method to propagate the request handling can be inserted at the beginning or at the end of the `check`
method. This allows an handler along the chain to change the order of checks.

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

The Command design pattern converts requests or simple operations into objects.

<details>
  <summary>Click to know more about the Command</summary>

The Command design pattern allows the creation of a `Command` class with a delegating `execute` method:

```
abstract class Command {
    public Operator operator; // an object that can execute operations
  
    Command(Operator operator){
        this.operator = operator;
    }
  
    void doFirstOperation() {
        operator.firstOperation();
    }
  
    void doSecondOperation() {
        operator.secondOperation();
    }
  
    public abstract boolean execute(); // method to execute the command  
}
```

Therefore a set of simple `SpecificCommand` classes will be :

```
class FirstSpecificCommand extends Command {  
    FirstSpecificCommand(Operator operator){
        super(operator);
    }
  
    @Override
    public boolean execute() {
        operator.prepareToFirstAndSecondStep();
        return false;
    } 
}
  
  
class SecondSpecificCommand extends Command {  
    SecondSpecificCommand(Operator operator){
        super(operator);
    }
  
    @Override
    public boolean execute() {
        operator.prepareToSecondAndFirstStep();
        return false;
    } 
}
```

Defining several classes like SpecificCommand allows us to customize the `execute()` behaviour.

Introducing now a `CommandHistory` class to manage the stack of commands allows us to easily navigate those:

```
class CommandHistory {
    private Stack<Command> history = new Stack<>();
  
    public void push(Command command) {
        history.push(command);
    }
  
    public Command pop() {
        return history.pop();
    }
  
    public boolean isEmpty() { return history.isEmpty();}
}
```

In this way we can use all the commands like in the following demo:

```
class Demo {
    public static void main(String[] args) {
        Operator operator = new Operator();
        CommandHistory history = new CommandHistory();
        boolean someCondition = true; // init some boolean variable to add commands to history
        if(someCondition) {
            history.push(new FirstSpecificCommand(operator));
        }
        if(someCondition) {
            history.push(new SecondSpecificCommand(operator));
        }
    }
}
```

In this way we're adding several commands to the `CommandHistory` without the need for instantaneous execution. Those
can be then executed scrolling the `CommandHistory` itself.

</details>

## Iterator

The Iterator design pattern allows traversing collection's elements without exposing the underlying representation (
list, stack, tree, etc.)

<details>
  <summary>Click to know more about the Iterator</summary>

Collections are datatypes that contain group of objects. Since collections can also be graphs or trees, traversal
behaviours can be changing. The Iterator design pattern allows isolation and extraction of the traversal behaviour into
a separate object called `Iterator`.

Let's create a first `Iterator` interface which defines the main iterator methods to be implemented:

```
interface Iterator {
    boolean hasNext();
    
    FirstObject getNext();
    
    void reset();
}
```

A couple of different `ConcreteIterator` would be:

```
class FirstConcreteIterator implements Iterator {
    private int currentPosition = 0;
    private List<FirstObject> firstObjectList = new ArrayList<>();
    
    public FirstConcreteIterator(List<FirstObject> firstObjectList){
        this.firstObjectList = firstObjectList;
    }

    @Override
    public boolean hasNext() {
        return currentPosition < firstObjectList.size();
    }
    
    @Override
    public FirstObject getNext() {
        if (!hasNext()) {
            return null;
        }
        FirstObject nextFirstObject = firstObjectList.get(currentPosition);
        currentPosition++;
        return nextFirstObject;
    }
    
    @Override
    public void reset() {
        currentPosition = 0;
    }
}
```

Let's imagine now some client code would need to access to the elements of `firstObjectList` without knowing such access
algorithm implementation details:

```
class Demo {
    public static void main(String[] args) {
        List<FirstObject> firstObjectList = Arrays.asList(new FirstObject(), new FirstObject(), ...);
        FirstConcreteIterator firstConcreteIterator = new FirstConcreteIterator(firstObjectList);
        while(firstConcreteIterator.hasNext()){
            FirstObject firstObject = firstConcreteIterator.getNext();
            firstObject.doSomething();        
        }
    }
}
```

</details>

## Mediator

The Mediator design pattern allows chaotic dependencies reduction imposing objects collaboration via a mediator object
only.

<details>
  <summary>Click to know more about the Mediator</summary>

Imagine you have a series of classes that need to cooperate with each other. The nature of such cooperation can depend
on several factors. Instead of implementing the logic of collaboration and communication between classes in the classes
themselves the classes will naturally become less usable.

The creation of a mediator ceases all the direct collaborations between objects, offering to the components mutual
independence and reusability. Mediator centralizes information exchange between software components.

Let's call the classes that need to cooperate as `Components`:

```
interface Component {
    void setMediator(Mediator mediator);
    String getName();
    void perfomComponentOperation();
}
```

Therefore, the `Concrete Components` will implement the `Component` interface:

```
class FirstConcreteComponent implements Component {
    private Mediator mediator;
    
    @Override
    void setMediator(Mediator mediator){
        this.mediator = mediator;
    }
    
    @Override
    String getName(){
        return "firstConcreteComponent";
    }
    
    @Override
    void perfomComponentOperation(){
        // Here ConcreteComponent operation
    }
}

class SecondConcreteComponent implements Component {
    private Mediator mediator;
    
    @Override
    void setMediator(Mediator mediator){
        this.mediator = mediator;
    }
    
    @Override
    String getName(){
        return "secondConcreteComponent";
    }
    
    @Override
    void perfomComponentOperation(){
        // Here ConcreteComponent operation
    }
}
```

The `Mediator` will be defined by the following interface:

```
interface Mediator {
    void doSomething();
    void doSomethingElse();
    void registerComponent(Component component);
}
```

The `Concrete Mediator` will implement the `Mediator` interface:

```
class ConcreteMediator implements Mediator {
    private FirstConcreteComponent firstConcreteComponent;
    private SecondConcreteComponent secondConcreteComponent;
    
    @Override
    void registerComponent(Component component){
        component.setMediator(this);
        switch (component.getName()) {
            case "firstConcreteComponent":
                firstConcreteComponent = (FirstConcreteComponent)component;
                break;
            case "secondConcreteComponent":
                secondConcreteComponent = (SecondConcreteComponent)component;
                break;
    }
    
    @Override
    String doSomething(){
        firstConcreteComponent.perfomComponentOperation();
        secondConcreteComponent.perfomComponentOperation();
    }
    
    @Override
    String doSomethingElse(){
        secondConcreteComponent.perfomComponentOperation();
        firstConcreteComponent.perfomComponentOperation();
    }
}
```

Let's imagine now some client code using components and mediator to `doSomething` or `doSomethingElse`. In this way the
whole business logic regulating `Components` mutual rapport to perform such actions will remain centralized in
the `Mediator`:

```
class Demo {
    public static void main(String[] args) {
        Mediator mediator = new ConcreteMediator();
        mediator.registerComponent(new FirstConcreteComponent());
        mediator.registerComponent(new SecondConcreteComponent());
        mediator.doSomething();
        mediator.doSomethingElse();
    }
}
```

</details>

## Memento

The Memento design pattern allows object internal state capture through time without public exposition of those states
in an uncontrolled manner across objects.

<details>
  <summary>Click to know more about the Memento</summary>
Imagine you have a series of classes of which you want to be able to capture object's internal state in all points in time.
To do so you may need to create a new class responsible for such a task, which can easily end up in imposing public state on all the tracked classes plus public state on the tracker itself (to allow snapshot creation and reading).

Solution is to delegate snapshot state creation to state owners, called originator objects. In this way copy of the
state is performed from inside the object without granting any access to hidden state from outside.

The state snapshot of the originator is stored in an object called `Memento`, which content is accessible only to the
originator.

The entity able to store snapshots of state in the `Memento` is called `Caretaker`, and its interaction with `Memento`
is defined through a limited interface.

Let's imagine we have some `Editor` class which needs to provide a snapshot history feature. A snapshot history consists
in a list of `Command` - `Memento` pairs, each command generates a new Memento:

```
class Pair {
        Command command;
        Memento memento;
        Pair(Command c, Memento m) {
            command = c;
            memento = m;
        }
    }
```

We therefore need a class which contains and manages a list of `Pair`:

```
class History {
    private List<Pair> history = new ArrayList<Pair>();
    private Integer virtualsize = 0; // to track where we are in the history pair list
    
    public void push(Command c, Memento m) {
        if (virtualSize != history.size() && virtualSize > 0) { // if history is longer than it should be
            history = history.subList(0, virtualSize - 1); // delete last pair
        }
        history.add(new Pair(c, m)); // add a new pair
    }
    
    public boolean undo() {
        Pair pair = getUndo();
        if (pair == null) { // not trying to undo a pair at the beginnig of list
            return false;
        }
        pair.getMemento().restore();
        return true;
    }
    
    private Pair getUndo() {
        if (virtualSize == 0) { // there is no undo to be done, no pair left
            return null;
        }
        virtualSize = Math.max(0, virtualSize - 1); // position of last pair of list
        return history.get(virtualSize); // return last pair
    }
    
    public boolean redo() {
        Pair pair = getRedo();
        if (pair == null) { // not trying to redo a pair at the end of list
            return false;
        }
        pair.getMemento().restore();
        pair.getCommand().execute();
        return true;
    }
    
    private Pair getRedo() {
        if (virtualSize == history.size()) { // there is nothing to be redone, we're at end of list
            return null;
        }
        virtualSize = Math.min(history.size(), virtualSize + 1);  // position of next pair of list
        return history.get(virtualSize - 1); // return next pair
    }        
```

The `History` class takes care of:

- creating new `Command` - `Memento` pairs with the `push` method
- undoing the last `Command` - `Memento` pair with the `undo` method, retrieving the last previous state
- redoing the next `Command` - `Memento` pair with the `redo` method, retrieving the upcoming next state

We then need the `Memento` class, which will contain the `Editor` object and its last-state backup:

```
class Memento {
    private String backup;
    private Editor editor;

    public Memento(Editor editor) {
        this.editor = editor;
        this.backup = editor.backup(); // when the memento is originate the snapshot of the editor is created
    }

    public void restore() {
        editor.restore(backup);
    }
}
```

Therefore the `Editor` class will contain its own `History` object and present the two `backup` and `restore`
functionalities:

```
class Editor {
    // other fields 
    private History history; 
    
    public Editor() {
        history = new History();
        // init other fields
    }
    
    String backup() {
        // do the backup and return it in a Base64 encoded for that can only be used by the restore method
    }
    
    void restore(String backup){
        // transfer to all the other fields the value stored in the Base64 encoded backup decoding it
    }
}
```

The `Editor` will be controller by  `Command` classes which will need to implement the following interface:

```
interface Command{
    String getName();
    void execute();
}
```

An example of a `Command` class could be the following:

```
class ConcreteCommand implements Command {
    private Editor editor;
    private String otherField;
    
    public ConcreteCommand(Editor editor, String otherField){
        this.editor = editor;
        this.otherField = otherField;
    }
    
    @Override
    public String getName() {
        return otherField; // if otherField was an object return a field of it or some metadata
    }

    @Override
    public void execute() {
        // use otherField to do something, if otherField was an object methods could be used
    }
}
```

In this way we can delegate the creation of `Mementos` completely:

```
class Demo {
    public static void main(String[] args) {
        Editor editor = new Editor(); 
        History history = new History(); // generate empty history
        
        // give the first command to the editor
        createMementoAndExecuteCommand("first-value");
        // give the second command to the editor
        createMementoAndExecuteCommand("second-value");
        
        // now the history willl be:
        // 1. "first-value" command memento
        // 2. "second-value" command memento
        
        // undo the last pair
        boolean secondUndone = history.undo();
        if(secondUndone){// if undone correctly
            // give the third command to the editor
            createMementoAndExecuteCommand("third-value");
        }
    }
    
    private void createMementoAndExecuteCommand(String otherFieldValue){
        Memento memento = new Memento(editor);
        Command concreteCommand = new ConcreteCommand(editor, otherFieldValue);
        history.push(concreteCommand, memento)
    }
}
```

As one amy see from the client code example the submission of a command to the controlled `Editor` object can be easily
linked to `Memento` creation and state storage. Using then the `History` object becomes easy navigating the space of
state snapshots, undoing and redoing previous commands resetting the state. The core of such solution stays in the
internal `Memento.restore` and `Command.execute` calls done in the `History.undo` and `History.redo` methods: each time
we navigate the space of snapshots the history takes care (caretaker instead) to reset state and execute commands.

</details>

## Observer

The Observer design pattern allows observing object to be notified when an event happens on the observed object thanks
to a subscription mechanism.

<details>
  <summary>Click to know more about the Observer</summary>

Imagine we have an object that has an interesting state for a bunch of other objects. Such object can be
called `subject` or `publisher`. The objects that want news about the latter state are instead called `subscribers`. One
could use different strategies to allow `subscribers` know `publisher` state: the `subscribers` could go after
the `publisher` state regularly (wasting tons of energies and never getting the news in real time), or the `publisher`
could notify everybody about every change in its state. Far from being optimal, such solutions solve the problem wasting
a lot of energies. The solution the pattern proposes is to allow `subscribers` subscriptions to events' streams managed
by the `publisher`. In this way whenever a certain event happens on the `publisher` it can go over the
right `subscribers` and notify them with their notification method.

Let's imagine we have a basic `publisher` with some interesting internal state:

```
class Publisher {
    Map<String, List<EventListener>> listeners = new HashMap<>(); // map each list of subscribers 
    
    public Publisher(String... operations) { // when constructing add a stream with an empty array of subscribers
        for (String operation : operations) {
            this.listeners.put(operation, new ArrayList<>());
        }
    }
    
    public void subscribe(String eventType, EventListener listener) { // subscribe adding to the right list
        List<EventListener> users = listeners.get(eventType);
        users.add(listener);
    }

    public void unsubscribe(String eventType, EventListener listener) { // unsubscribe removing from the right list
        List<EventListener> users = listeners.get(eventType);
        users.remove(listener);
    }
    
    public void notify(String eventType) {
        List<EventListener> users = listeners.get(eventType);
        for (EventListener listener : users) { // notify all the subscribed event listeners
            listener.update(eventType);
        }
    }
}
```

We can now define the interface for the `EventListener` which will provider the `update` method:

```
public interface EventListener {
    void update(String eventType);
}
```

Therefore the `subscribers` will implement it:

```
public class Subscriber implements EventListener {
    private String field;

    public Subscriber(String field) {
        this.field = field;
    }

    @Override
    public void update(String eventType) {
        // do something when notified   
    }
}
```

The client code will therefore be:

```
public class Demo {
    public static void main(String[] args) {
        Publisher publisher = new Publisher();
        publisher.subscribe("first-stream", new Subscriber("first-value"));
        publisher.subscribe("first-stream", new Subscriber("second-value")); // subscribed first two to the first event stream
        publisher.subscribe("second-stream", new Subscriber("third-value"));
        publisher.subscribe("second-stream", new Subscriber("fourth-value")); // subscribed last two to the second event stream
        
        publisher.notify("first-stream"); // sends notification to "first-value" and "second-value" subscribers 
        publisher.notify("second-stream"); // sends notification to "third-value" and "fourth-value" subscribers 
    
        publisher.unsubscribe("first-stream", new Subscriber("first-value")); // removed first subscriber from first stream subscription
        publisher.notify("first-stream"); // sends notification only to "second-value" subscribers 
    }
}
```

As one may see each time the `publisher` notifies on a certain event stream all the associated `subscribers` will get
the notification and act accordingly.

</details>

## State

The State design pattern allows objects to alter their behaviour when the internal state changes.

<details>
  <summary>Click to know more about the State</summary>

Imagine you want a specific set of objects to behave differently according to the values of their internal state. In
such a case each method of those objects should contain some sort of state check, which will easily get longer and
scattered the more states we consider and the more methods we add. This design patterns allows delegation of behaviour
change of the changing-behaviour class, namely `Context`, to some `State` classes, which will contain all the necessary
state and be managed in state change.

Let's define the `State` interface, that will declare all the methods the `State` classes will need to implement to be
properly use in `Context`:

```
interface State {
    void firstMethod();
    void secondMethod();
}
```

A `FirstConcreteState` will then be something along the following example:

```
class FirstConcreteState implements State {
    
    @Override
    public void firstMethod(){
        System.out.println("FirstConcreteState.firstMethod");
    }
    
    @Override
    public void secondMethod(){
        System.out.println("FirstConcreteState.secondMethod");
    }
}
```

Another `State` implementation may be:

```
class SecondConcreteState implements State {
    
    @Override
    public void firstMethod(){
        System.out.println("SecondConcreteState.firstMethod");
    }
    
    @Override
    public void secondMethod(){
        System.out.println("SecondConcreteState.secondMethod");
    }
}
```

The `Context` class will then be:

```
class Context {
    private State state;
    
    public Context() {
        this.state = new FirstConcreteState();
    } 

    void setState(State state){
        this.state = state;
    } 

    void getState(){
        return state;
    }
}
```

Therefore the client code will be:

```
public class Demo {
    public static void main(String[] args) {
        Context context = new Context();
        context.getState().firstMethod(); // prints FirstConcreteState.firstMethod
        context.getState().secondMethod(); // prints FirstConcreteState.secondMethod
    
        context.setState(new SecondConcreteState());
        context.getState().firstMethod(); // prints SecondConcreteState.firstMethod
        context.getState().secondMethod(); // prints SecondConcreteState.secondMethod
    }
}
```

As you noticed after changing `Context` state the execution of the same two lines in which we call `firstMethod`
and `secondMethod` produce different results. No need then to implement any state check in the `Context` class: every
time a new behaviour has to be added to it a new `ConcreteState` class can be implemented following the `State`
interface.

`Context` state management happens then on the client code side, which provides new states and requires then the
associated behaviours.

</details>

## Strategy

The Strategy design pattern allows algorithm exchange encapsulating each one of those in a class and using those
interchangeably.

<details>
  <summary>Click to know more about the Strategy</summary>

Imagine you want to implement a class, the `Context`, that can execute a task in different ways. Each time a new way has
to be added to the code of the original class probabilities of errors and tight coupling increase. It is way better to
define `Strategies`, which can be swapped onr with the other. In such a way the `Context` will execute it in the way
defined by the `Strategy` that had been set.

Let's define the `Strategy` interface:

```
interface Strategy {
    void someMethod();
}
```

Let's imagine two different `CocreteStrategies` implementing such interface:

```
class FirstCocreteStrategy implements Strategy {
    @Override
    public void someMethod(){
        System.out.println("FirstCocreteStrategy.someMethod");
    }
}

class SecondCocreteStrategy implements Strategy {
    @Override
    public void someMethod(){
        System.out.println("SecondCocreteStrategy.someMethod");
    }
}
```

The chosen `Strategy` will be indirectly used by the `Context` class:

```
class Context {
    private Strategy strategy;
    
    public Context(Strategy strategy) {
        this.strategy = strategy;
    }
    
    public void executeTask() {
        this.strategy.someMethod();
    }
    
    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }
}
```

Therefore the client code will only take care of setting the right `Strategy` in the `Context` and use the latter:

```
public class Demo {
    public static void main(String[] args) {
        Strategy firstStrategy = new FirstCocreteStrategy();
        Strategy secondStrategy = new SecondCocreteStrategy();
        Context context = new Context(firstStrategy);
        context.executeTask(); // prints FirstCocreteStrategy.someMethod
        context.setStrategy(secondStrategy);
        context.executeTask(); // prints SecondCocreteStrategy.someMethod
    }
}
```

This pattern allows client code to add new behaviours to the `Context` creating new `ConcreteStrategy` classes
implementing the desired behaviour respecting the `Strategy` interface.
</details>

## Template Method

The Template Method design pattern allows algorithm structure definition and step implementation overriding without
structural modifications.

<details>
  <summary>Click to know more about the Template Method</summary>

Imagine you need to implement an `Algorithm` to accomplish a specific task in different conditions. According to such
conditions the internal implementation of some steps of the `Algorithm` should change. If the implementation of
the `Algorithm` stays only in one class it is easy to end up with lots of conditions on state that will change the
internal behaviour itself. Moreover, each time the algorithm needs to be used in another specific condition the single
algorithm class needs to be modified and updated with new conditionals.

It is better to break down the algorithm in several steps, each one implemented by a specific method:

```
abstract class Algorithm {
    
    public void mainMethod() {
        firstStep();
        secondStep();
    }
    
    protected abstract void firstStep();
    protected abstract void secondStep();
}
```

Let's imagine now two `ConcreteAlgorithm` class extending the case `Algorithm`:

```
class FirstConcreteAlgorithm extends Algorithm {    
    
    protected void firstStep() {
        System.out.println("FirstConcreteAlgorithm.firstStep");
    }
    
    protected void secondStep() {
        System.out.println("FirstConcreteAlgorithm.secondStep");    
    }
}

class SecondConcreteAlgorithm extends Algorithm {    
    
    protected void firstStep() {
        System.out.println("SecondConcreteAlgorithm.firstStep");
    }
    
    protected void secondStep() {
        System.out.println("SecondConcreteAlgorithm.secondStep");    
    }
}
```

Both `FirstConcreteAlgorithm` and `SecondConcreteAlgorithm` can execute the `mainMethod` with their own steps
implementations,seamlessly to the client code:

```
public class Demo {
    public static void main(String[] args) {
        Algorithm algorithm = new FirstConcreteAlgorithm();
        algorithm.mainMethod(); // prints FirstConcreteAlgorithm.firstStep - FirstConcreteAlgorithm.secondStep
        algorithm = new SecondConcreteAlgorithm();
        algorithm.mainMethod(); // prints SecondConcreteAlgorithm.firstStep - SecondConcreteAlgorithm.secondStep
    }
}
```

</details>

## Visitor

The Visitor design pattern allows to separate alogirithms from objects in which they operate.

<details>
  <summary>Click to know more about the Visitor</summary>
  Imagine you need to introduce some behavior into a class hierarchy on all classes. The required behavior unfortunately needs to adapt to the specific class type itself. Introducing methods in all classes can be a long and possibily dangerous process in which the existing codebase can acquire bugs. Instead, injecting such specific behavior is the best way to go, decoupling it from the objects in which it will be executed, and allowing similar-behavior groups to be provided to the class hierarchy all at the same time.

Let's define our `Visitor` interface, having declared all the methods that can use all the objects of the class hierarchy as input:

```
interface Visitor {
    String visitFirstClass(FirstClass firstClass);
    String visitSecondClass(SecondClass secondClass);
}
```

Each of such methods will be used in the class hierarchy. Let's define then our class hierarchy, that relies on the following interface:

```
interface MainInterface {
    void someMethod();
    String accept(Visitor visitor);
}
```

Let's imagine two different classes implementing such interface:

```
class FirstClass implements MainInterface {
    @Override
    public void someMethod(){
        System.out.println("FirstClass.someMethod");
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitFirstClass(this);
    }
}

class SecondClass implements MainInterface {
    @Override
    public void someMethod(){
        System.out.println("SecondClass.someMethod");
    }

    @Override
    public String accept(Visitor visitor) {
        return visitor.visitSecondClass(this);
    }
}
```

The visitors have now to implement the `Visitor` interface, providing adaptive behaviors for each object class:

```
class FirstVisitor implements Visitor {

    @Override
    public String visitFirstClass(FirstClass firstClass){
        System.out.println("FirstClass visited by FirstVisitor");
    }

    @Override
    public String visitSecondClass(SecondClass secondClass){
        System.out.println("SecondClass visited by FirstVisitor");
    }
}

class SecondVisitor implements Visitor {

    @Override
    public String visitFirstClass(FirstClass firstClass){
        System.out.println("FirstClass visited by SecondVisitor");
    }

    @Override
    public String visitSecondClass(SecondClass secondClass){
        System.out.println("SecondClass visited by SecondVisitor");
    }
}
```

The client code will therefore be:

```
public class Demo {
    public static void main(String[] args) {
        FirstClass firstClass = new FirstClass();
        SecondClass secondClass = new SecondClass();

        FirstVisitor firstVisitor = new FirstVisitor();
        firstClass.accept(firstVisitor); // prints "FirstClass visited by FirstVisitor"
        secondClass.accept(firstVisitor); // prints "SecondClass visited by FirstVisitor"

        SecondVisitor secondVisitor = new SecondVisitor();
        firstClass.accept(secondVisitor); // prints "FirstClass visited by SecondVisitor"
        secondClass.accept(secondVisitor); // prints "SecondClass visited by SecondVisitor"
    }
}
```

As you can see the added behavior introduced with first and second visitor are not hardocded into the class hierarchy, grouped under the same visitor and easily switchable. All comes with little cost in terms of the class hierarchy codebase modification, adding only the `accept` method that simply redirects the new behavior request to the used visitor.

</details>
  
Credits to [Refactoring Guru's design patterns](https://refactoring.guru/design-patterns)
