# Creational Design Patterns

## Abstract Factory
The Abstract Factory design pattern provides and easy way to create families of related objects without specifying their concrete classes.

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

## Builder
The Builder design pattern provides a simple step-by-step process to construct complex objects.
Identify the class which requires numerous step-by-step initialization of fields and nested objects and the relative long constructor:

```
class NumerousFieldsClass{
    private int firstField;
    private int secondField;
    private int thirdField;
    ...
}
```



## Factory Method
The Factory method design pattern provides an easy introduction for new classes of objects that offer similar functionality with respect to the previous ones. 
## Prototype
## Singleton
## Abstract Factory

# Structural Design Patterns

## Adapter
## Bridge

Credits to [Refactoring Guru's design patterns](https://refactoring.guru/design-patterns)
