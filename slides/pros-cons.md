# Pros & Cons


## TOC

5.1 Advantages

5.2 Disadvantages

5.3 When should it be considered?


## 5.1 Advantages


- Easy to maintain

- Language and framework independent

- Easy to test

Note:
It’s easier to maintain an application that has a good separation of concerns. You can change things in the Infrastructure Layer without having to worry about breaking a business rule.
It’s easy to find where are the business rules, the use cases, the code that deals with the database, the code that exposes an API, and so on.

The Onion Architecture does not depend on any specific language or framework. You can implement it in basically any language that supports dependency injection.

By doing dependency injection in all the code, everything becomes easier to test.
Instead of each module being responsible of instantiating it’s own dependencies, it has its dependencies injected during it’s initialization. This way, when you want to test it, you can just inject a mock that implements the interface your code is expecting to.


## 5.2 Disadvantages


- Overkill when you don't have many business rules

- Learning curve

<img style="background-image: white;" src="slides/assets/yagni.png" />

Note:
When you are creating a software that does not deal with business rules, this architecture won’t fit well. It would be really cumbersome to implement, for example, a simple gateway using Onion Architecture.
It can be hard to implement a service using Onion Architecture when you have a database-centric background.


## 5.3 When should it be considered?

Note:
So, considering the pros and cons mentioned here, Onion Architecture can be a good choice if your service is going to be domain logic in it.
However, if your service won't have any business rules on it, probably it's not worthy the time.
