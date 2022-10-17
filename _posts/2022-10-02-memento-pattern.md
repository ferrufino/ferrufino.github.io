---
layout: post
title: Memento Pattern
tags: iosDesignPatterns, behavioralPatterns, mementoPattern
math: false
toc: true
date: 2022-10-03 20:22 +0800
---

Memento Pattern captures the current state of an object and store it in such a way that you can retrieve it at a later time when you would like to return to the previous state.

### Design overview

1. **Originator.** Allows object to be saved and restored. _Object to be saved._
2. **Memento.** represents a stored state. _Saved state_
3. **Care taker.** requests a save from originator and receives a memento in response. Responsible for persistent the memento and retrieving the memento for the originator.

iOS apps typically use an **Encoder** to encode an originator’s state into a memento, and a **Decoder** to decode a memento back to an originator. This allows encoding and decoding logic to be reused across originators.

Use **Codable**, it is a typealias that combines Encoder and Decoder.

### When should you use this pattern?

Save the state of an object and store. Allowing it to be retrieved at a later time and restore the previous state.

### Use Cases

- Save game system (level, health, number of lives)
- Profile accounts

### Be Careful with

Adding or removing codable properties, they can both throw an error. Mitigate this by expecting it to fail while doing a 'try'.

### Code Example - [Coming soon]

{% highlight js linenos %}

{% endhighlight %}
