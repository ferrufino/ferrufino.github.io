---
layout: post
title: Strategy Pattern
tags: iosDesignPatterns, behavioralPatterns, strategyPattern
math: false
toc: true
date: 2022-10-02 20:22 +0800
---

Strategy Pattern consists of defining <Mark>different objects that conform to a strategy protocol and can be set or be interchangeable at runtime </Mark> while being used by an object.

This creates a level of abstraction that decouples a ViewController from another concrete strategy object (For example a service). Bringing the opportunity to interchange more than one relatable "concrete strategy" by conforming to a common protocol.

### Design overview

1. Object using a Strategy - most likely a view controller, that has a Strategy protocol property.
2. Strategy Protocol - defines methods every strategy must implement.
3. Family of Strategy objects - Conforms to protocol and implements methods.

Intended to be easily changeable at runtime. (The family of strategy objects)
If a behavior does change, do not use this!

### When to use it?

- Two or more interchangeable behaviors.
- Family of objects, instead of one.
- Need of a easy changeable form for objects at runtime.

### Use cases:

- handling different services for the same topic. For example, "movie rating services", could have Rotten tomatoes, IMDb, Metacritic, etc.

### Be careful with

If the behavior really doesn't change between concrete strategies. Then it's better to just call this in the calling View Controller.

### Example

We define a protocol called Strategy and it has a method named "convert" the number into a format decided by the user at runtime.
We implement two algorithms: BinaryStrategy to convert number to binary and HexStrategy to convert number to hex.

{% highlight js linenos %}
protocol Strategy {

    func convert(number: Int)

}

class Convert {

    var strategy: Strategy
    var number: Int

    init(number: Int, strategy: Strategy) {
        self.number = number
        self.strategy = strategy
    }

    func update() {
        self.strategy.convert(number: number)
    }

}

class BinaryStrategy: Strategy {

    func convert(number: Int) {

        let binary = String(number, radix: 2)
        print("Binary is \(binary)")

    }

}

class HexStrategy: Strategy {

    func convert(number: Int) {

        let hex = String(number, radix: 16)
        print("Hex is \(hex)")

    }

}
{% endhighlight %}

So if we use the above:

{% highlight js linenos %}

let binaryConvert = Convert(number: 2, strategy: BinaryStrategy())
binaryConvert.update()
let hexConvert = Convert(number: 123, strategy: HexStrategy())
hexConvert.update()
Output:
Binary is 10
Hex is 7b

{% endhighlight %}
