---
layout: post
title: Swift Memory Management
tags: ios, memory, swift
math: false
toc: true
date: 2022-10-17 21:26 +0800
---

When an object references another object in memory a reference counting is initiated for memory management. This is handled for you by ARC, automatic reference counting. Increasing and decreasing on the count of references when one object is referred or the relation is nil and this makes ARC release it from memory (deinit). By doing this references, there are known cases where a reference cycle might occur never calling deinit on both objects, therefore never being released from memory.

## Reference Cycles for Classes

(other term is retain cycles.)
Two class instances that hold a strong reference to each other create a strong reference cycle that leads to a memory leak. That’s because <ins>each instance keeps the other one alive, so their reference counts never reach zero.</ins>

Strong references increment the reference count when the property is initialized as part of an object.
Also when an instance of a new object B uses an initialized object A as param, the properties in object A increase there reference count.

When an object gets reassigned or assigned to nil, it decrements reference count. Releasing previous strong references.

## Weak References

A weak reference <ins>does not increment or decrement the reference count </ins> of an object. Since weak references do not increment the reference count of an object, a <mark>weak reference can be nil</mark>. This is because the object could be deallocated while the weak reference is pointing to it.

Weak references are references that don't play a role in the ownership of an object.\
They automatically detect when the underlying object has gone away.\
This automatic detection is why you always declare them with an optional type as they can be nil.

- It is not enough to make the var optional, it has to maintain a weak reference.
- weak references must be vars.

## Unowned references

Unowned references behave <ins>much like weak ones in that they don’t change the object’s reference count.</ins>
<mark> They always expect to have a value — you can’t declare them as optionals. </mark> As it guarantees to the Swift compiler that an unowned reference will not be nil when it is accessed. <ins> Otherwise it crashed/ program error.</ins>

for example:
A tutorial cannot exist without an author.
make var author in tutorial non-optional.
At the same time, a tutorial does not “own” the author, so the reference should be unowned.

## How to solve a reference cycle?

Imagine this case:
There is a reference cycle between a Contact and Number.
<ins>A Contact can exist without a Number, but a Number should not exist without a Contact.</ins>

What is the best way to remove this reference cycle?

<mark>Apple Documentation recommends that a parent object should have a strong hold on a child obejct by convention.</mark>

Then, this means that giving Contact a strong hold on a Number. And Number an "unowned" reference to a Contact.

## Reference Cycles for Closures

Closures capture values from the enclosing scope. (via captured value of self)\
Because Swift is a safe language, closures extend the lifetime of any object they use to guarantee those objects are alive and valid.
This automatic safety is convenient, but the downside of this is you can inadvertently create a reference cycle if you extend the lifetime of an object that itself captures the closure.\
Closures, you see, are reference types themselves.

For example:

{% highlight js linenos %}
lazy var description: () -> String =
{ "\(self.title) by \(self.author.name)" }
{% endhighlight %}

Remember that a lazy property isn’t assigned until its first use and that self is only available after initialization.

You created another strong reference cycle between the tutorial object and the closure by capturing self, so only the author’s deinit method runs.

### @escaping

Note: Swift requires self inside of closures of reference types, and it’s a good reminder that you are capturing a reference to the current object.
The other place where you can omit self is when the closure is non-escaping.

Closure parameters are by default non-escaping because they are assumed to not be used after the function returns. This is the case for map, filter, reduce, sort and more.

<mark> If the closure is going to be used at a later time, it needs to let the caller know. </mark> You do this by marking the closure parameter with the @escaping attribute.

{% highlight js linenos %}
final class FunctionKeeper {

    private let function: () -> Void // 1
    init(function: @escaping () -> Void) { // 2
      self.function = function
    }

    func run() { // 3
        function()
    }

}
{% endhighlight %}

Here is what FunctionKeeper does:

1. The stored property function keeps a reference to a closure.
2. You pass a closure on initialization. Because it is going to put it into a stored property and keep using it after init(function:) returns, it must be marked as @escaping.
3. The run() function executes the function.
   {% highlight js linenos %}
   let name = "Gustavo"
   let f = FunctionKeeper {
   print("Hello, \(name)")
   }
   f.run()
   {% endhighlight %}

## Capture Lists

Elegant way to break strong reference cycles in closures.
Capture lists are a language feature to help you control exactly how a closure extends the lifetime of instances it references. Capture lists are lists of variables captured by a closure and appear at the beginning of the closure before any arguments.

Now let's explain this through an example:
{% highlight js linenos %}
var counter = 0
var g = {print(counter)}
counter = 1
g()
{% endhighlight %}
The above will print > 1
This is because there is a reference to counter and it got updated to 1. This is picked up by closure g.

Now we add a Capture List:
{% highlight js linenos %}
counter = 0
g = {[c = counter] in print(c)}
counter = 1
g()
{% endhighlight %}
Note: It can be "[c = counter]" or just "[counter]". From latter a local shadow copy is created.

The above will print > 0.
This is because closure g() created a showed copy of counter when it was initialized.

When dealing with objects, remember that “constant” has a different meaning for reference types. A capture list will cause the closure to capture and store the current reference stored inside the captured variable with reference types. Changes made to the object through this reference will still be visible outside of the closure.

note:
if it is in the closure capture list, it is captured by value.
if it is not in the capture list, but defined outside of closure, it is captures by reference.

### Unowned Self

The closure that determines the tutorial’s description captures a strong reference of self and creates a reference cycle.

Since the closure doesn’t exist after releasing the tutorial object from memory, self will never be nil, so you can change the strong reference to an unowned one using a capture list.

### Weak Self

If self reference in closure might be nil, use a weak self.

[weak self] means that the closure will not extend the lifetime of self. If the underlying object representing self goes away, it gets set to nil. The code doesn’t crash anymore like using [unowned self]) but does generate a warning which you can fix.

## Weak-Strong pattern

<mark> Does not extend the lifetime of self but converts the weak reference to a strong one after it enters the closure: </mark>

{% highlight js linenos %}
lazy var description: () -> String = { [weak self] in

    guard let self = self else {
      return "The tutorial is no longer available."
    }

    return "\(self.title) by \(self.author.name)"

}

{% endhighlight %}

guard makes self strong if it isn’t nil, so it’s guaranteed to live until the end of the closure. You print a suitable message if self is nil this time, and the previous warning is gone.
