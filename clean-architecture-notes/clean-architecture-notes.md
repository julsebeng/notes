*It's more important for software to be easy to change than for the software to simply work.*

# SOLID Principles
## Single Responsibility Principle
#### *Each software module has one, and only one, reason to change.*

Another way to put this principle is, *A module should be responsible to one, and only one, actor.*
* In this case, an actor is some group of users/stakeholders that want a part of the system changed.
* For any agent acting upon some part of the system, code that isn't relevant to the tasks they are
  there to perform should be moved into a different location.
  * Actors can be coupled togeather, just like code!

It can be annoying to factor out code relevant to each actor into individual classes - now there are
multiple objects that need to be instantiated and managed.
* The *facade* pattern is a stand-in class whose only purpose is to instantiate these individual 
  objects, and call their functions when asked. This way, the *logic* for each actor is separated,
  without having to juggle multiple class instances throughout the code.


---
## Open-Closed Principle
#### *The behavior of a system should be changed by adding new code, not changing existing code.*

In other words, software should be extensible without having to be modified. Code in one module that
should be protected from changes in another via a *using* relationship.
* This creates a hierarchy of levels inside of a system - the higher the level of the module, the 
  more resistant to change it is.
  * Anything at the top level of this hierarchy ought to be protected from changes in just about
    anything - this should be the code that deals with the **main concern** that the module exists
    to address.
    * Everything else ought to be a helper in some form or another to help accomplish this main 
      concern.


---
## Liskov Substitution Principle
#### *To build software systems from interchangeable parts, those parts must adhere to a contract that allows those parts to be substituted for one another.*


---
## Interface Segregation Principle
#### *Software shouldn't depend on what it doesn't use.*


---
## Dependency Inversion Principle
#### *Code that implements high-level policy should not depend on the code that implements low-level details - rather, details should depend on policies.*