---
layout: post
title: Intro Behavioral Patterns - Delegation
tags: iosDesignPatterns, behavioralPatterns, delegationPattern
math: false
date: 2022-10-02 20:22 +0800
---

Within iOS there are different patterns to learn. They fall into the following pattern categories that describe a purpose:

- **Behavioral patterns** describe how objects communicate with each other.
- **Structural patterns** describe how objects are composed to form larger subsystems.
- **Creational patterns** instantiate or "create" objects for you.

First we will look at the different type of **Behavioral Patterns**:

# Behavioral Patterns

Behavioral Design Pattern deals with how objects interact.
It describes how objects communicates with each other and how the steps of a task is broken among different objects so as to provide more flexibility and make the code more efficient.

## Delegation Pattern

Mainly consists on 1:1 communication pattern through delegation. This is represented by Delegate and Protocol.
To comprehend better, let's exemplify this pattern.

Imagine there is a Boss and an Intern. See it as a Boss view controller **delegating responsibilities defined on a protocol**, those are the command lists to an Intern view controller.

Boss knows all the information. What image to use, what data to pass, etc.
Intern gets that information, and shows it.
It just follows orders represented by the Boss Protocol function.

Then the intern view controller implements methods based on information received from Boss.

When you assign a delegation to self. It is like an intern stating it will be the Intern of the Boss.

### Design overview

`[Object in need of a delegate] -> [Protocol] <--[Object acting as a Delegate]`

1. **Delegating object.** Obj that has a delegate. Delegate is hold as a weak property to avoid a retain cycle.
2. **Delegate Protocol.** Defines methods a delegate should implement. Appears as a property within the Delegating Object.
3. **Delegate.** Helper object. The delegate. Implements Delegate protocol.

Delegates should almost always be a _weak_.

Datasources and delegates both use this pattern:

- Datasources provide data.
- Delegates receive data.

### _Use Cases - Why should you use this pattern?_

- Breakup large classes
- Create reusable components
- Navigation to different views depending on action taken.

### Example

{% highlight js linenos %}
import UIKit

public protocol CreateQuestionGroupViewControllerDelegate {

    func createQuestionGroupViewControllerDidCancel(\_ viewController: CreateQuestionGroupViewController)

    func createQuestionGroupViewController(\_ viewController: CreateQuestionGroupViewController,
    created questionGroup: QuestionGroup)

}

public class CreateQuestionGroupViewController: UITableViewController {

    // MARK: - Properties
    public var delegate: CreateQuestionGroupViewControllerDelegate?

    @IBAction func cancelPressed(\_ sender: Any) {
        delegate?.createQuestionGroupViewControllerDidCancel(self)
    }

}

extension SelectQuestionGroupViewController: CreateQuestionGroupViewControllerDelegate {

    public override func prepare(for segue: UIStoryboardSegue, sender: Any?) {

            if let navController = segue.destination as? UINavigationController,
                let viewController = navController.topViewController as? CreateQuestionGroupViewController {
                viewController.delegate = self // This is the key line of the method
            }
            //skip anything else
        }
    }

    public func createQuestionGroupViewControllerDidCancel(\_ viewController: CreateQuestionGroupViewController) {
        dismiss(animated: true, completion: nil)
    }

}

{% endhighlight %}
