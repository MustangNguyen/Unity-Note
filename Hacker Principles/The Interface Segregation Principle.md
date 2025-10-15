#programming #programming-philosophy #hacker-principles


[The Interface Segregation Principle on Wikipedia](https://en.wikipedia.org/wiki/Interface_segregation_principle)

> No client should be forced to depend on methods it does not use.

The fourth of the '[[../Hacker Principles/SOLID|SOLID]]' principles. This principle states that consumers of a component should not depend on functions of that component which it doesn't actually use.

As an example, imagine we have a method which reads an XML document from a structure which represents a file. It only needs to read bytes, move forwards or move backwards in the file. If this method needs to be updated because an unrelated feature of the file structure changes (such as an update to the permissions model used to represent file security), then the principle has been invalidated. It would be better for the file to implement a 'seekable-stream' interface, and for the XML reader to use that.

This principle has particular relevance for object-oriented programming, where interfaces, hierarchies and abstract types are used to [minimise the coupling](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#todo) between different components. [Duck typing](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#todo) is a methodology which enforces this principle by eliminating explicit interfaces.

See also:

- [Object-Oriented Programming](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#todo)
- [[../Hacker Principles/SOLID|SOLID]]
- [Duck Typing](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#todo)
- [Decoupling](https://github.com/dwmkerr/hacker-laws/tree/main?tab=readme-ov-file#todo)