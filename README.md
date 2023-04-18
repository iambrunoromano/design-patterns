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
