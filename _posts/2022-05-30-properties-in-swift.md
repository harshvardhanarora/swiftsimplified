---
title: Properties in Swift
description: Practical overview of Swift properties - stored, computed, lazy, property observers, and access control with concise examples.
date: 2022-05-30 00:00:00
categories: [Swift Basics]
tags: [swift, properties]
---

Properties are simply variables or constants inside a struct, class or enum. We can have **stored propertie**s which can hold constants and variables for us. We also have **computed properties** that do not hold any data but it gets computed when we ask for it. We will also look `type` and `instance` properties along with **property observers** in this article.

---

### Stored Properties

We looked at examples of classes and structures in the [previous](../structures-and-classes-in-swift) article. Stored properties are variables and constants that store data while being a part of to a struct, class or enum.

```swift
struct Vacation {
    let location: String
    let numberOfDays: Int
    var pictures: [String]
}
```

We create a `struct` with 3 stored properties out of which 2 are constant and 1 is a variable. Now you might wonder why to use constants at all? Truth is even though it sounds counterintuitive to use constants, it forces you to be much more explicit about the intent of your code.

Consider the above example — When you create an instance of your struct `Vacation`, you know the name of the location and number of days should not change, but the pictures might change since all our friends take their sweet time to share vacation pictures.

Making properties constant forces you to not be able to change it by mistake at any point in your code thus avoiding any unexpected bugs.

---

#### How to assign value to stored properties?

When you create an instance of your struct or class, you set its stored properties to the desired value inside the initialiser. We looked at the basics of initialisation in the [previous](../structures-and-classes-in-swift) article, and there will be a separate detailed article for it soon as well, so don’t worry if it is not fully clear to you.

```swift
let summerVacation = Vacation(location: "Bali", numberOfDays: 10, pictures: [])
print(summerVacation.location)
// Output - Bali
```

The free initialiser for Vacation struct sets the properties to the values we specify inside the initialiser arguments.

---

#### Constant Structure Instances

We created the `pictures` property as a variable so we could modify it later. Let’s try doing that.

`summerVacation.pictures.append("beach.png")`

You get an error —

`Cannot use mutating member on immutable value: 'summerVacation' is a 'let' constant`

Notice how the instance summerVacation was created as a constant? If you create the instance of a structure as a constant, you cannot modify its contents even though the properties inside it might be variables.

Let’s try a new vacation instant which will be a variable this time —

```swift
var springVacation = Vacation(location: "Paris", numberOfDays: 5, pictures: ["louvre.jpg"])
print(springVacation.pictures)
// Output - ["louvre.jpg"]
springVacation.pictures.append(contentsOf: ["eiffeltower.jpg", "sienne.jpg"])
print(springVacation.pictures)
// Output - ["louvre.jpg", "eiffeltower.jpg", "sienne.jpg"]
```

That worked perfectly! Till this day I make mistakes of creating constant structs when I intended on modifying them later in the code, but Swift is powerful enough to let me know each time I’m making that mistake.

> This behaviour of structures is because they are value types, but it does not hold for classes which are reference types. We will look at this pretty soon!

---

#### Lazy Stored Properties

Lazy people do not get up to do a task until they **ABSOLUTELY HAVE TO**! That’s exactly how `lazy` properties in Swift work. They will not be initialised until the point someone asks for them in your code.

> Constant properties in Swift always must have a value before the initialisation of the instance they belong to completes. So lazy properties must always be a `var`.

Let’s look at an example —

```swift
class Lazy {
    var normalProperty: String = {
        let message = "This is a normal property"
        print(message)
        return message
    }()
    lazy var lazyProperty: String = {
        let message = "This is a lazy property"
        print(message)
        return message
    }()
}
```

We define a class with a lazy and a normal property. It is important to note that we create and execute closures to initialise both properties.
Now let’s see what happens when we try to create an instance of our class —

```swift
let lazyClass = Lazy()
// Output - This is a normal property
```

Only the first property was initialised and the lazy property did not get initialised. Let’s try to access the lazy property and see what happens —

```swift
print(lazyClass.lazyProperty)
/* Output -
This is a lazy property
This is a lazy property
*/
```

The message gets printed twice! One for the initialisation and then in line 49 when we print it. And that’s how lazy properties work!

---

### Computed Properties

Till this point we looked at different ways to create stored properties. What that means is the value of that property is stored somewhere in memory which can be retired at any point of time.

Computed properties, as the name suggest, compute the value of the required property when it is required. It has a `getter` method which is accessed when anyone tried to call that property. The result of the getter method is returned as the property value.

```swift
struct Person {
    var firstName: String
    var lastName: String
    
    var fullName: String {
        firstName + " " + lastName
    }
}
```

In the above example, fullName is a computed property. It is important to explicitly mention the type of the property if it is computed. Let’s see an example of how to use it —

```swift
var mark = Person(firstName: "Mark", lastName: "Dawson")
print(mark.fullName)
// Output - Mark Dawson

```

Now you might think this works in a similar way to lazy properties, but let’s see the difference —

```swift
mark.lastName = "Turner"
print(mark.fullName)
// Output - Mark Turner

```

I now changed the `lastName` property and printed the `fullName` property. If this was a lazy property (which is a stored property), the value of `fullName` would have not changed. But since computed values always generate value when they are called for, it picked up the latest value of `firstName` and lastName while computing the `fullName`.

---

#### Getter and Setter

The above example you saw are **read-only** computed properties — as the name suggests you can only ask from values from it and not set anything.

Computed properties have **getter** and **setter** methods that allow you to define what happens when someone asks for the value of the property or tried to set the property to some value.

Although computed values do not store anything and thus cannot be set to anything, they allow for us to change other properties when the **setter** method of a computed property is called.

Let’s look at an example to understand this —

```swift
struct Student {
    var firstName: String
    var lastName: String
    var age: Int
    
    var fullName: String {
        get {
            return firstName + " " + lastName
        }
        set(newName) {
            let split = newName.split(separator: " ")
            firstName = String(split[0])
            lastName = String(split[1])
        }
    }
    
    var description: String {
        fullName + " is a student of age \(age)"
    }
}
```

In the above struct, we have 2 stored properties in `firstName` and `lastName`. We also have a **read-only computed** property in `description`. Now `fullName` is where we have a computed property with a getter and setter. The getter works exactly like the read-only computed property —

```swift
var student = Student(firstName: "Harry", lastName: "Dawson", age: 15)
print(student.fullName)
// Output - Harry Dawson
```

But the `setter` is where things change.

Now when you are trying to set a computed property, you actually do not get to change that property itself, but it gives you an opportunity to change other **stored properties** that are available in the context. As you can see from the above struct, the we use the new name supplied to change the `firstName` and `lastName` of that instance of the struct. Let’s look at an example —

```swift
student.fullName = "Harry Potter"
print(student.lastName)
// Output - Potter
```

The **setter** method of the computed property code invoked and all the lines inside it got executed. Important thing to note here is if you supplied a string with no white spaces in it, the program would crash with an **index out of range** error. Try to figure out why!

---

### Property Observers

As the name suggests, property observers help us observe change in our properties. We can observe both before and after the change was made to that property. We can have property observers for the following —

#### Stored Properties defined by you

```swift
struct Employee {
    var name: String {
        willSet {
            print("New name will be \(newValue)")
        }
        didSet {
            print("Previous name was \(oldValue)")
        }
    }
}
```

As you can see, you can access the value the property will be set in `willSet` using the `newValue` property. Similarly, after the property has been updated, you can access the old value using the `oldValue` property inside `didSet`.

Let’s see an example —

`var paige = Employee(name: "Paige Hudson")`


This does not print anything. Why? Property observers do not do anything before initialisation. Try to change the **name** property —

```swift
paige.name = "Paige Holmes"
/* Output -
 	New name will be Paige Holmes
 	Previous name was Paige Hudson
*/
```

#### Inherited Stored and Computed Properties

Now this part is not that simple to understand, but bear with me. If you do not understand inheritance and initialisation at the moment, do not worry about it. I will be putting out articles on both of them later on which you can refer to and come back to this section again.

Okay let’s begin. Let’s define our parent class first —

```swift
class ParentClass {
    var firstName: String
    var lastName: String
    var fullName: String {
        get {
            firstName + " " + lastName
        }
        set {
            let split = newValue.split(separator: " ")
            firstName = String(split[0])
            lastName = String(split[1])
        }
    }
    
    init(firstName: String, lastName: String) {
        self.firstName = firstName
        self.lastName = lastName
    }
}
```

This is similar to the `Student` struct we defined before. But this will be a class because **inheritance** only works with **classes**. Now we will create a class that inherits from this class and tries to set property observers to its inherited properties —

```swift
class SubClass: ParentClass {
    override var lastName: String {
        willSet {
            print("Last name will be changed to \(newValue)")
        }
    }
    
    override var fullName: String {
        willSet {
            print("Full name will be changed to \(newValue)")
        }
    }
}
```

The only thing we have done in the subclass is to override a stored and a computed property and add a `willSet` property observer to both of them.

Let’s see what this does in action —

```swift
let subClass = SubClass(firstName: "Lucy", lastName: "Banks")
subClass.fullName = "Sherlock Holmes"
/* Output -
 	Full name will be changed to Sherlock Holmes
 	Last name will be changed to Holmes
*/
```

Once you change the `fullName` property of the subclass, it triggers the `willSet` observer. After that, the `set` method for `fullName` gets triggered in its parent class. Which in turn triggers the `willSet` property observer for `lastName`. Take your time to understand this, write the code in your playground and try to map this sequence. After a few times, it will make sense to you.

---

### Type Properties

Alright, that was a lot of information about properties to take in. Take a breather, go through everything again before proceeding to the last section for this article.

Let’s go! Up until this point, every property we have discussed are **instance properties**. That means, they are associated to an instance of your struct, class or enum. Type properties, as the word suggests, are associated to the type itself. You cannot access type properties from an instance of an object. Let’s see all this in an example —

```swift
struct Pet {
    static var description = "This is a Pet"
    
    let name: String
}
```

In the above struct `Pet`, we have a type property and an instance property. `Type` properties are declared using the keyword `static` as you can see from the above example.

```swift
let cat = Pet(name: "cat")
let dog = Pet(name: "dog")
```

You can create instances of the struct which will have **unique** values for its **instance** properties. You cannot access the **type** properties from instances of your struct. You can only access them on the type itself —

```swift
print(cat.name)
print(dog.name)
print(Pet.description)
/*
 Output -
 	cat
 	dog
 	This is a Pet
*/
```

You can change the type properties similar to instance properties, but the change needs to be made on the type itself —

```swift
Pet.description = "This is somebody's pet"
print(Pet.description)
// Output - This is somebody's pet
```

---

That’s it! We covered a lot of ground here. Give yourself time to absorb all of it, try to write out the code yourself. Feel free to reach out to me on LinkedIn in case you have any questions.

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.