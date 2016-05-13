# Swift 2: Create an Instance of a Class from a String
## (by calling a custom initializer??)

(This repo contains a Xcode 7.3.1 Swift playground with the below text)

First of all, Swift is just awesome.
It is my favorite programming language by far.
Nevertheless, it is still evolving, and although Swift features such as type safety can help us write rock-solid code, Swift is occassionally challenged by those features to provide the same crazy contortions afforded by good ol' Objective-c.

For example... try to create a class instance from a string.
If you haven't had to do this, its probably a good thing.
Consider yourself lucky.
But if you're porting an old, crusty Objective-c application to the ultra-sweet, modern Swift language (as I'm currently doing) you may need a trick or two like this in your tool belt.

## The Problem

Suppose you have the name of a class as a string.
Then you get the crazy idea you'd like to programmatically create an instance of that class from that string.
What?? Slow down there, Chief!

In Objective-c, this was fairly simple.
However, in Swift, such functionality is still a little restricted.
To make matters worse, you then go completely off the rails and decide you really need to call a custom initializer for that class!
What?? Have you completely lost your mind?!

However, if you know the superclass type, and that superclass has a *required* initializer, you can create that instance without knowing the specific derived class type.

## Code

In this example, we will use a simple class inheritance hierarchy to show how to instantiate a specific derived type instance with only the knowledge of the base type.

### A Superclass

For this example we need a simple superclass. Consider the following `Car` class:
```
import UIKit

class Car {
    let make: String
    
    required init(make: String) {
        self.make = make
    }
    
    var description: String {
        return "\(make) is a \(self.dynamicType)."
    }
}
```
The `Car` class has a `make:String` property which gets set via a custom initializer.
There is no basic `init()` here, so in order to instantiate `Car` we must call the custom initializer.

> The initializer is marked with the `required` modifier.
This guarantees that all classes derived from `Car` will have this same initializer.
It also provides the critical clue Swift will need in order to know how to create an instance from a `String`.

#### A Derived Class
```
class Supercar : Car {}
```
Next, we need a derived class.
Nothing fancy needed here.
Just basic inheritance.

### A String
```
let classString = NSStringFromClass(Supercar)
```
Now we need a string that is the name of the class that we will use to instantiate the object.
In this case, we want to create the string from the `Supercar` class.
This will prove that this technique does indeed create the correct class type.

> We use `NSStringFromClass()` to save a "mangled" class string.
It is important to create your class string this way in order to correctly find and reference the class type later.

### Create The Instance
```
if let carType = NSClassFromString(classString) as? Car.Type {
    let car = carType.init(make: "Lamborghini")
    car.description   // output: "Lamborghini is a Supercar."
}
```
To make this possible, we use `NSClassFromString` to turn the string into a optional `AnyClass` object.
`AnyClass` is a just a typealias for `AnyObject.Type`.
For this return type to be really useful to us we must try to cast it as the type of our `Car` superclass: `Car.Type`.

Once we have `carType`, something very swifty happens.
Swift allows us to access the custom initializer that we marked with the `required` modifier earlier.
And presto!
We can create an instance of the specific `Supercar` derived class.

And there you have it! Instantiation of a class using a custom initializer from only a string, and in pure Swift!
Now go port some code.
Ride 'em, Cowboy!
