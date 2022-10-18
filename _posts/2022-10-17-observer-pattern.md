---
layout: post
title: Observer Pattern
tags: iosDesignPatterns, behavioralPatterns, observerPattern
math: false
toc: true
date: 2022-10-17 20:22 +0800
---

The observer pattern let's an object <mark> observe the changes notified by another object. </mark>

- Loosely coupled design between interacting objects.
- 1:N communication pattern - Notification/Observer pattern
- New observers can be added without impacting other observer or value/subject sent.

  Part of Combine\* - Swift 5.1 makes it easy to implement the observer pattern using @Published properties.

### Design overview

Consists of three parts:

1. **Subscriber:** <ins>Observer object</ins> that subscribes and receives updates.
2. **Publisher:** <ins>Observable object.</ins> Sends updated/notifications.
3. **Value:** Underlying object that has changed.

### When should you use this pattern?

Use this pattern when an object wants to be notified of changes made on another object.

### Use Cases

Often used with MVC:\
Controller observes model changes.\
Model is able to communicate changes back to controller, without needing to know anything about the controller.

### Be Careful with

- For simple model/properties that never change, this pattern is an overkill.
- Do not use it with a singleton and multi cases.
- Hard to traced and debug issue.
- Costly on thread used as when a notification is done, all observer handlers are called on the same thread. Watchout for calling handlers on the background thread when they are used for updating the UI.
- Before using this pattern, define what you expect to change and when (under what conditions).
- Cannot use @Published on let properties as it cannot be changed.
- Cannot be used on structs - Only on classes.
- Only if a package already has it implemented, then use it. Else <mark> it is strongly advised not to use this pattern. </mark>

### Code Example

{% highlight js linenos %}
import Combine

public class User {
@Published var name: String

    public init(name:String) {
        self.name = name
    }

}

let user = User(name: "Gustavo")
//This returns an object of type published, string.publisher. This object is what can be listened to for updates.
// Accessed the publisher for broadcasting changes to the user's name at user.$name
let publisher = user.$name

var subscriber: AnyCancellable? = publisher.sink() { // by calling sink, you create a subscriber on publisher.
print("User's name is \($0)") // closure gets called, when initial value changes.
}
{% endhighlight %}

{% highlight js linenos %}
user.name = "Fernando"
user.name = "Vicky"
subscriber = nil
user.name = "Gustavo has left the building"

// prints
// User's name is Gustavo
// User's name is Fernando
// User's name is Vicky
{% endhighlight %}
