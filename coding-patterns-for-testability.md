# Coding Patterns for Code Testability

Suggested patterns / best practices for writing testable code

## Fail-fast principle

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 8

The Fail Fast principle stands for stopping the current operation as soon as any unexpected error occurs. This principle makes your application more stable by
- Shortening the feedback loop — The sooner you detect a bug, the easier it is to fix. A bug that is already in production is orders of magnitude more expensive to fix compared to a bug found during development.
- Protecting the persistence state — Bugs lead to corruption of the application’s state. Once that state penetrates into the database, it becomes much harder to fix. Failing fast helps you prevent the corruption from spreading.

## Dependency Injection

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 8

Always inject all dependencies explicitly (including loggers), either via the constructor or as a method argument. (Page 215, Unit Testing)

## How to prevent DB coupling

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 8


### Usual anti-pattern

A system begins with its own dedicated database. After a while, another system begins to require data from the same database. And so the team decides to share access to a limited number of tables just for ease of integration with that other system. As a result, the database becomes a dependency that is both managed and unmanaged.

The use of a database is a poor way to implement integration between systems because it couples these systems to each other and complicates their further development

### Solution

Only resort to this approach when all other options are exhausted. 
A better way to do the integration is via an API (for synchronous communications) or a message bus (for asynchronous communications).

## How much data to log?

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 8


- Ideally, only use diagnostic logging for unhandled exceptions
- Otherwise, only add diagnostic logs temporarily when you need to debug something.

## Where to log?

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 8

- In most cases, you can safely move diagnostic logging from domain classes to controllers

## One Act per Task

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 3

If you need to call two public APIs to do one thing, it might be a sign of missing encapsulation and it can also lead to invariant violations.

## Adhere to command query separation principle

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 4

If a method produces a side effect, that method’s return type should ideally be void. 
And if the method returns a value, it must stay side-effect free.

## Prefer functional programming

Source: Taken from 
[Unit Testing: Principles, Practices and Patterns, 2020 Edition](https://www.amazon.sg/Unit-Testing-Principles-Automation-Integration/dp/1617296279/ref=asc_df_1617296279/?tag=googleshoppin-22&linkCode=df0&hvadid=404807948861&hvpos=&hvnetw=g&hvrand=14512927362377798526&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062507&hvtargid=pla-869957460222&psc=1), Chapter 6

Assertions in a test based on output is easier to test for than assertions based on state and communication.

To achieve this, the code should ideally be written in a Functional paradigm - i.e. all inputs and outputs of a function must be **explicitly** expressed in its *method signature*.
