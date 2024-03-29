---
layout: post
title: Delegation Pattern
tags: iosDesignPatterns, behavioralPatterns, delegationPattern
math: false
toc: true
date: 2022-10-02 20:22 +0800
---

The delegation pattern allows an <mark> object to use a helper object to perform a task</mark>, instead of doing the task itself.

This allows for code reuse through object composition [^first_footnote], instead of inheritance. Communicating back to its owner in a decoupled way.

1:1 communication pattern - Delegate and Protocol

### Design overview

<code>[Object in need of a delegate] -> [Protocol] <--[Object acting as a Delegate]<code>

1. **Delegating object.** Obj that has a delegate. <ins>Delegate property is hold as 'weak' to avoid a retain cycle.</ins>
2. **Delegate Protocol.** <ins>Defines methods a delegate should implement.</ins> Appears as a property within the Delegating Object.
3. **Delegate.** Helper object. The delegate. Comforms and implements Delegate protocol. As also <ins>assigns self to delegate variable reference. </ins>
   It is standard to send self of delegate object as an input param.

Delegates should almost always be a _weak_.

Datasources and delegates both use this pattern:

- Datasources <ins>provide data.</ins>
- Delegates <ins>receive data.</ins>

### When should you use this pattern?

- Breakup large classes
- Create reusable components
- Navigation to different views depending on action taken.

### Use Cases

Delegation is used for everything from handling table view events using UITableViewDelegate, to modifying cache behavior using NSCacheDelegate.

Instead of naming all use cases, I decided to write down in words an example:

In a ToDo list, a user can check a cell item marking it as complete. \
Then from the cell clicked on, it invokes a <ins>delegate method</ins> which is the response behavior expected to happen. \
This is communicated to the ViewController who has the responsability to refresh the view based on latest changes..

**ToDoCellDelegate** - protocol \
with a method toDoCellDidUpdateSubtask

**ToDoCell** - Delegating object \
Has a weak var delegate ToDoCellDelegate \
Through this delegate variable calls out method when needed.

**ViewController** - Delegate Object - inherits and implements the protocol \
(1) Inherits the ToDoCellDelegate. By doing this it <ins>implements the method</ins>, defining what will happen whenever it is called to action.
In this case update the collection view and update UIView position. \
(2) While dequeue reusable cell ToDoCell, <ins>It assigns self to the delegate variable of the cell.</ins> \
<code> cell.todoDelegate = self <code>

### Be Careful with

Be careful about creating too many delegates for an object. It can be overused.

Delegation is usually a good choice when a type needs to be usable in many different contexts, and when it has a clear owner in all of those contexts - just like how a UITableView is often owned by either a parent container view or its view controller.

If an object is doing too much with many delegates, then maybe it has to be broken down to different objects.

### Code Example

I'm skipping overload definition of methods and just defining the most important pieces to show case this pattern.

Look for the uppercase comments\*
{% highlight js linenos %}
import UIKit

// Delegate Protocol - defines methods to be implemented
public protocol ToDoCellDelegate: class {

    func toDoCellDidUpdateSubtask(_ cell: ToDoCell)

}

// Object in need of a Delegate
public class ToDoCell: UICollectionViewCell {

    // MARK: - IBOutlets
    @IBOutlet var label: UILabel!
    @IBOutlet var checkBoxView: UIView!
    @IBOutlet var subtaskCollectionView: UICollectionView!

    // MARK: - Properties
    weak var todoDelegate: ToDoCellDelegate? // DELEGATE VARIABLE
    var subtasks: [ToDo]?
    .
    .
    .

}

// MARK: - UICollectionViewDelegate
extension ToDoCell: UICollectionViewDelegate {

    public func collectionView(_ collectionView: UICollectionView,
    didSelectItemAt indexPath: IndexPath) {
        let toDo = subtasks![indexPath.row]
        toDo.isComplete = !toDo.isComplete
        collectionView.reloadData()
        todoDelegate?.toDoCellDidUpdateSubtask(self) // CALL ON DELEGATE
    }

}

// MARK: - UICollectionViewDataSource
extension ViewController: UICollectionViewDataSource {

    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return toDos.count
    }

    func collectionView(_ collectionView: UICollectionView,
        cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
            let currentToDo = toDos[indexPath.row]
            let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "cell", for: indexPath) as! ToDoCell
            cell.todoDelegate = self // KEY PART - ASSIGN SELF TO DELEGATE PROPERTY
            cell.label.text = currentToDo.name
            cell.checkBoxView.backgroundColor = currentToDo.isComplete ?
            UIColor(red: 0.24, green: 0.56, blue: 0.30, alpha: 1.0) : .white
            cell.subtasks = currentToDo.subtasks
            cell.layoutSubviews()
            return cell
    }

}

// MARK: - ToDoCellDelegate
// Extension of existing class, that conforms to. Object acting as delegate.
extension ViewController: ToDoCellDelegate {

    func toDoCellDidUpdateSubtask(_ cell: ToDoCell) {
        collectionView.reloadData()
        updateWarriorPosition()
    }

}
{% endhighlight %}

[^first_footnote]: Object composition, in programming, is just classes containing instances of other classes as attributes to implement the desired functionality instead of inheriting classes.
