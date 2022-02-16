# Introduction


## TOC

1.1 Software lifecycle

1.2 What is "maintainable software"?


## 1.1 Software lifecycle


<img style="background-image: white;" src="slides/assets/lifecycle.jpg" />

Note:
When doing software development, one of the most important things to have in mind is that good software should always be evolving. Softwares that are not evolving anymore are either legacy or useless. Throughout the sprints on a Scrum based framework, during each sprint it will always be maintained, evolved, receiving new features, improvements, and bug fixes.


<img style="background-image: white;" src="slides/assets/coupling.png" />

Note:
So, with that in mind, we would like to be able to build a software that can be maintained by current, and future developers. Throughout the years, developers will join and leave the company, so we would like to adopt an well known architectural pattern, so also newcomers will have a better time when being onboarded.
So, our main goal is to build maintainable software.


## 1.2 What is "maintainable software"?

Note:
It’s a software that a developer should be able to do improvements and fixes without worrying about breaking something under the hood. Any developer, familiar with the domain, should be able to understand the code, and easily know where to change things.


### Separation of concerns

Note:
Modifying infrastructure code should not break any domain logic. Modifying the database modeling should not affect the software’s business rules. Modifying the way you receive parameters on your API should not affect the payload you send to the database. You should be able to easily test your domain logic.
Then, we should start thinking about separating different concerns into different units of code.
