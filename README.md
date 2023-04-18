# Creational Design Patterns

## Abstract Factory
The Abstract Factory design pattern provides and easy way to create families of related objects without specifying their concrete classes.

The case is you have several classes that behave in a similar way but differ for what they do represent:
```
class SomeClassFirstVersion{
    void doSomething(){}
}

class SomeClassSecondVersion{
    void doSomething(){}
}

class SomeClassThirdVersion{
    void doSomething(){}
}
```

Define then an interface with the common behaviours for the mentioned classes:
```
interface DoSomethingInterface{
    void doSomething();
}
```

Let then all the classes implement this interface:
```
class SomeClassFirstVersion implements DoSomethingInterface{
    @Override
    void doSomething(){
        System.out.println("New [First Version] doSomething method!");
    }
}

class SomeClassSecondVersion implements DoSomethingInterface{
    @Override
    void doSomething(){
        System.out.println("New [Second Version] doSomething method!");
    }
}

class SomeClassThirdVersion implements DoSomethingInterface{
    @Override
    void doSomething(){
        System.out.println("New [Third Version] doSomething method!");
    }
}
```


Define then another interface for the coming factory classes, namely the `AbstractFactory`:
```
interface FactoryInterface{
    DoSomethingInterface createDoSomething();
}
```

## Builder
## Factory Method
The Factory method design pattern provides an easy introduction for new classes of objects that offer similar functionality with respect to the previous ones. 
## Prototype
## Singleton
## Abstract Factory

# Structural Design Patterns

## Adapter
## Bridge

Credits to [Refactoring Guru's design patterns](https://refactoring.guru/design-patterns)
