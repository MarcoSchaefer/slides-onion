# Onion Architecture


## TOC

2.1 Overview

2.2 Layers

2.3 Golden rule

2.4 Dependency injection


## 2.1 Overview

Note:
Onion Architecture, like Clean Architecture, Hexagonal Architecture, Ports and Adapters, is an architectural pattern that helps you to organize your code per concern/responsibility.
Onion defines three different layers with different responsibilities, and your code will be distributed across them.


## 2.2 Layers


<!-- .slide: data-background-color="white"  --> 
<img style="background-image: white;" src="slides/assets/onion.png" />

Note:
Onion defines three layers: Domain, Application, and Infrastructure.
Domain should contain your business rules.
Infrastructure should contain your code that does IO.
Application should contain code that receives data from the Infra layer, and calls methods from your Domain.


## 2.3 Golden Rule

*Nothing in an inner circle can know anything at all about something in an outer circle. That includes functions, classes, variables, or any other named software entity.*

Robert C. Martin

Note:
This rule basically reflects what we already know as Dependency Injection.


## 2.4 Dependency injection


<!-- .slide: data-background-color="white"  --> 
<img style="background-image: white;" src="slides/assets/di.png" />

Note:
Dependency injection is a pattern that ensures that a unit of code (can be class, function, module, package, etc) is never responsible for creating their own dependencies.
This is used to loose coupling between different parts of your code.
By using this pattern, you ensure that your code depends on a behavior, instead of a particular implementation.


### Example

Let's say you want to create a service for sending an email confirming an user's purchase order.


```ts
const notifyPurchase = async (account: Account, order: Order) => {
  const emailAPI = new SendGridAPI();

  if(account.muteNotifications) {
    return
  }

  const emails = [ account.email ];

  if(account.hasParentalNotifications) {
    emails.push(account.parent_email);
  }

  return emailAPI.NotifyPurchase(emails, order);
}
```

Note:
So, let's say you have this function for notifying some emails when a purchase happens.
This function instantiates it's own dependencies, and then, sends some emails depending on the user's settings.
Notice that this function does not use Dependency injection.
So, how can I test it? Since it does not receive it's dependencies, I can't provide it a mock. Every time it will attempt to create a real email client. I'll need to somehow patch the underlying calls.
So let's make a few changes:


```ts
const notifyPurchase = async (
  emailAPI: SendGridAPI, // concrete type
  account: Account,
  order: Order
) => {
  if(account.muteNotifications) {
    return
  }

  const emails = [ account.email ];

  if(account.hasParentalNotifications) {
    emails.push(account.parent_email);
  }

  return emailAPI.notifyPurchase(emails, order);
}
```

Note:
Now, instead of instantiating it's own dependencies, the function now receives a concrete type on it's parameters.
We removed the responsibility of creating dependencies from that function, but, since it's still expecting a concrete type, it's gonna be hard to provide a mock for it.
If SendGridAPI type has a hundred of methods and a lot of properties, our mock would need to have all of those!
Actually, the bigger problem is: This function should depend on a behavior, but instead it's depending on an implementation!
The behavior we should depend on is: We would like to receive an EmailAPI that has a NotifyPurchase method, which receives some emails and an order, and that's it!
We don't care if this emailAPI has a lot of methods and properties, only this NotifyPurchase method is used.
Now, let's change it to receive an interface instead:


```
interface EmailAPI {
  notifyPurchase: (emails: string[], order: Order) => Promise<void>;
}
```


```ts
const notifyPurchase = async (
  emailAPI: EmailAPI, // interface
  account: Account,
  order: Order
) => {
  if(account.muteNotifications) {
    return
  }

  const emails = [ account.email ];

  if(account.hasParentalNotifications) {
    emails.push(account.parent_email);
  }

  return emailAPI.notifyPurchase(emails, order);
}
```

Note: Now this function expects an interface instead of a concrete type.
It will make our lives way easier when we need to test it.
We can easily create a mock for it:


```ts
const mockEmailAPI = (emailsCalled: string[]) => ({
  notifyPurchase: async (emails: string[], order: Order) => {
    emailsCalled.push(...emails);
    return Promise.resolve();
  },
  calls: () => emailsCalled,
});
```
