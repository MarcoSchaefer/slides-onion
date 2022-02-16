# Domain Layer


## TOC

3.1 DDD

3.2 Domain Model

3.3 Value Objects


## 3.1 DDD


<!-- .slide: data-background-color="white"  --> 
<img style="background-image: white;" src="slides/assets/ddd.jpeg" />

Note:
Before diving into the Domain Layer, we need to define what we understand for "Domain".
Domain is: Knowledge about a particular industry or business. In our case, our domain knowledge regards investments and the financial industry.
Domain-driven design (DDD) is a software design approach that aims to model software in a way that matches a domain, according to it's experts.


### Ubiquitous language

Note:
One of the most important DDD guidelines is that we should have an ubiquitous language, i.e., domain experts and developers should use the same terms and entities.
Domain aware names like Position, Operation, Security, and so on, they must mean the exactly same thing in the domain rules and in the software code.


*Domain experts should object to terms or structures that are awkward or inadequate to convey domain understanding; developers should watch for ambiguity or inconsistency that will trip up design.*

Eric Evans


<img style="background-image: white;" src="slides/assets/ul-1.png" />


<img style="background-image: white;" src="slides/assets/ul-2.png" />

Note:
Both developers and domain experts should be able to use and understand the exact same terms regarding domain knowledge and implementation.


<img style="background-image: white;" src="slides/assets/ul-3.png" />


<img style="background-image: white;" src="slides/assets/ul-4.png" />


<img style="background-image: white;" src="slides/assets/ul-5.png" />


<img style="background-image: white;" src="slides/assets/ul-6.png" />

Note:
That's the greatest benefit of using a ubiquitous language.
It's easier for developers to model domain into code, and it's easier for domain experts do validate the modelling.


<img style="background-image: white;" src="slides/assets/ul-7.png" />
Note:
Also notice that your domain code should be used only to express your domain, so it should not declare things that are not from your business' domain.


## 3.2 Domain Model

Note:
One of the core concepts in DDD is the Domain Model.
A Domain Model is an unit of code that incorporates behavior and data from some business model.
In an OOP language for example, it could be a class with methods which describe the model’s behavior according to the business rules, and also it's properties describe the data contained in the model.


```ts
type Position = {
  walletId: string;
  securityId: string;
  date: Date;
  quantity: number;
  marketValue: number;
};

const recalculate = (position: Position, price: number): Position => (
  { ...position, marketValue: price * position.quantity }
);
```


```ts
class Position {

  walletId: string;
  securityId: string;
  date: Date;
  quantity: number;
  marketValue: number;

  // constructor...

  recalculate = (position: Position, price: number) => {
    this.marketValue = price * position.quantity;
  };
}
```

Note:
The purpose of the Domain Layer is to have your domain entities and rules.


```ts
type Custodian = {
  walletId: string;
  securityId: string;
  date: Date;
  quantity: number;
  averagePrice: AveragePrice;
};

type AveragePrice = {
  value: number;
}
```

Note:
You can also have a domain aggregate. An aggregate is a cluster of entities. In this case, the aggregate root is Custodian.


## 3.3 Value Objects

Note:
A Value Object is an entity that has no identity, and is immutable.
These objects have no behavior, being just bags of data used alongside your models.


```ts
type Point = {
  x: number;
  y: number;
}
```
