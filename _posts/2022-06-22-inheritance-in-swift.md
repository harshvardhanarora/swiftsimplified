---
title: Inheritance in Swift
description: Learn class inheritance in Swift - creating base and derived classes, method/property overriding, initialiser rules, and using final to prevent overrides.
date: 2022-06-22 00:00:00
categories: [Swift Basics]
tags: [swift, inheritance]
---
Inheritance allows you to create classes that build upon from other classes. You can write different implementations while maintaining the same behaviours by reusing code you have already written. Today’s article will focus on how to creating a base class and subclassing it, overriding properties and methods and also overriding property observers.

---

#### Base Class
A `base class` is the one which does not inherit from anything. Let’s look at an example —

```swift
class Pet {
    var name = "Margo"
    var description: String {
        return "My name is \(name)"
    }
    
    func speak() {
        // Subclasses will add their code
    }
}
Now we will try to create an instance of this class —

```swift
let cat = Pet()
print(cat.description)
// Output - My name is Margo
```

Notice we did not need to create an **initialiser** method for our class because there were no properties that needed initialisation.

This class does not inherit from any classes, but other classes can inherit from it and override the properties and methods that we have here. Let’s see how!

#### SubClass

A **subclass** is a class that inherits from a **base class** like the one we defined above. It basically means we build our new class on top of another class without affecting that class itself. Let’s look at an example —

```swift
class Dog: Pet {
    var numberOfLegs = 4
}
```

This is syntax to declare that our class `Dog` wants to inherit from our class `Pet`. Now what that does is make available all the public properties of `Pet` to `Dog`. We also create an additional property in `Dog` class which will not be accessible to `Pet`. Let’s see it in an example —

```swift
let dog = Dog()
print(dog.numberOfLegs)
// Output - 4
print(dog.description)
// Output - My name is Margo
```

As you can see, an instance of type Dog has access to property belonging to Cat class. You can also subclass a subclass itself. Now let’s look at how we can change the properties of a base class inside a subclass!

---

### Overriding

A subclass can have its own implementations for the properties or methods that it inherits from a base class. We call it **overriding**. Even if you chose to override a method, property or subscript, you can still use the existing implementation from the base class. Let’s see how!

#### Overriding Methods

Let’s create another subclass from Pet class and try to override the speak() method.

```swift
class Cat: Pet {
    override func speak() {
        print("Meow")
    }
}
```

Notice the `override` keyword that let’s Swift know you are trying to write a custom implementation for a method from your superclass. Let’s see it in action —

```swift
let snowy = Cat()
snowy.speak()
// Output - "Meow"
```

It’s as simple at that! Now, even though our initial implementation of method `speak()` was empty, but let’s see how we could call it. We use the super keyword to access the superclass —

```swift
class Cat: Pet {
    override func speak() {
        super.speak()
        print("Meow")
    }
}
```

#### Overriding Properties

You cannot override a stored property to set it to a new value. On the other hand, you can override **getter** and **setter** methods for your inherited property. You can also add **property observers** to respond to changing underlying property.

##### Overriding Getter and Setter

Before we look at an example to override getter and setter methods, you need to understand one simple rule. You can provide getter-setter overrides for a read only property, but you can’t provide just a getter override for read-write property. Let’s now jump off to an example to understand how it works —

```swift
class NamedPet: Pet {
    var petName: String?
    
    override var name: String {
        get {
            return petName != nil ? petName! : super.name
        } set {
            petName = newValue
        }
    }
}
```

If you go back, you will remember that our class `Pet` had a property named `name` which was a `var`. Let’s see what the above code means line by line —
- `Line 1` — Define subclass `NamedPet` which inherits from `Pet`.
- `Line 2` — Create an optional property `petName`.
- `Line 4` — Override `name` property while providing getter and setter methods
- `Line 6` — Returning `petName` if it is not empty otherwise return `name` property from base class using the keyword `super`.


Hopefully you understand what this class does before we see it working with an example. Also, try to remove the **setter** for the `name` property in line 7, you will receive an error saying you cannot override mutable property with **get-only** property. Hopefully you understand the rule we discussed above as well now. Let’s see an example of this class working —

```swift
let max = NamedPet()
print(max.name)
// Output - Margo
max.name = "Max"
print(max.petName!)
// Ouptut - Max
print(max.name)
// Output - Max
```

We create an object of class `NamedPet` named `max`. Initially, it is set to the default value of Margo which is what we see in the output. But after changing the property name to `max`, we see that now both the property `petName` and `name` have been set to `“Max”`.

#### Overriding Property Observers

Just like with overriding property setter and getter, there are certain things you need to understand before overriding property observers. You can’t add property observers to inherited properties that are constant stored properties or read-only computed properties. Reason — No point in observing changes to something that cannot be changed.

You must think at this point, that we could still override a setter method in the subclass. You’re correct to think that, but here’s another rule — You **CANNOT** provide an overriding setter and overriding property observer to the same property. You can simply handle the observing inside the setter method itself. With that out of the way, let’s look at an example —

```swift
class SomePet: Pet {
    override var name: String {
        didSet {
            print("Name was changed to \(name)")
        }
    }
}
```

In this example, we override the name property to add a didSet property observer. Whenever the name is changed, the closure inside the didSet will be executed. Let’s see an example —

```swift
var lex = SomePet()
lex.name = "Lex"
// Output - "Name was changed to Lex"
```

#### Preventing Overrides

You can prevent overrides of any property, method or subscript by using the keyword `final` before it.

```swift
class Planet {
    final var description: String {
        return "This is a planet"
    }
}
```
If you try to override this property in a subclass, you will receive an error.

You can also use `final` before a class, which would not allow any other class to subclass your class.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.