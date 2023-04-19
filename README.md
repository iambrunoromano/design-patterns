# Creational Design Patterns

## Abstract Factory
The Abstract Factory design pattern provides and easy way to create families of related objects without specifying their concrete classes.

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

Now that all the classes that behave in the same way are grouped around common interfaces we can define a new interface for the coming factory classes.

Namely the `AbstractFactory`:
```
interface FactoryInterface{
    DoSomethingInterface createDoSomething();
    DoSomethingElseInterface createDoSomethingElse();
}
```

We can now create several `Factories` that will take care of creating objects implementing `DoSomethingInterface` or `DoSomethingElseInterface`:
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

Since the two factories `FirstVersionFactory` and `SecondVersionFactory` implement the same interaface, they provide an interchangeable and loose coupled way of creating objects implementing `DoSomethingInterface` or `DoSomethingElseInterface`.
</details>
    
## Builder
The Builder design pattern provides a simple step-by-step process to construct complex related objects hiding the construction process to the client code.
Identify the classes which requires numerous step-by-step initialization of fields and nested objects and the relative long constructor:

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

Create a new `Builder` interface that declares all the methods that all the single builder classes will need to provide their own implementation of:

```
interface Builder {
    void reset();
    void firstField(FirstField firstField);
    void secondField(SecondField secondField);
    void thirdField(ThirdField thirdField);
    ...
}
```

As you may notice the `Builder` interface provides methods useful during both construction of `FirstClass` and `SecondClass` objects.

Create the new builder classes that implement the building steps for the single chosen (product - `FirstClass` and `SecondClass`) class:

```
class FirstBuilder {
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

class SecondBuilder {
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

Create then a new `Director` class that, given a `Builder`-implementing class, delegates the construction according the chosen recipe:

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

Now we can finally decouple the client code from the recipe used to create an object of class `FirstClass` and `SecondClass`:

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

If you want now to use another recipe you can just change one line of code of what you just read, completely decoupling the recipe needed to build something from the actual builder:

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

If you want instead to build another type of object but maintaing the same `firstRecipe` you can change only the used builder:

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

In this way the steps are completely decoupled from the managing of them during the construction phase, therefore a change in the steps' body or recipe's body is hidden with respect to the client code.


## Factory Method
The Factory method design pattern provides an easy introduction for new classes of objects that offer similar functionality with respect to the previous ones. 
## Prototype
## Singleton
## Abstract Factory

# Structural Design Patterns

## Adapter
## Bridge

Credits to [Refactoring Guru's design patterns](https://refactoring.guru/design-patterns)
