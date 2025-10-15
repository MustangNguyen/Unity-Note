#programming #programming-philosophy #hacker-laws


[The Law of Demeter on Wikipedia](https://en.wikipedia.org/wiki/Law_of_Demeter)

> Don't talk to strangers.

The Law of Demeter, also known as "The Principle of Least Knowledge" is a principle for software design, particularly relevant in object orientated languages.

It states that a unit of software should talk only to its immediate collaborators. An object `A` with a reference to object `B` can call its methods, but if `B` has a reference to object `C`, `A` should not call `C`s methods. So, if `C` has a `doThing()` method, `A` should not invoke it directly; `B.getC().doThis()`.

Following this principal limits the scope of changes, making them easier and safer in future.