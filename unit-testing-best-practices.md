# Notes on Unit Testing

## What is a unit test

A unit test is an automated test that
- Verifies a small piece of code (also known as a unit)
- Does it quickly,
- And does it in an isolated manner.

## What is the goal of unit testing

By definition, UTs can only verify a small subsection of an overall logical path, so only write them for the most important parts of your code base where one small change in condition can lead to a regression.

## What are the most important parts of your code base?

The heuristic here is to only write UTs for most important / complicated parts of the code base.

### The four different categories of your code base
Two axes: complexity & number of collaborators

Four quadrants:
- domain model & algorithms: high complexity and low number of collaborators (this includes utils too)
- Over-complex code: high complexity and high number of collaborators
- trivial code: low complexity, Low number of collaborators
- controllers: Low complexity, high number of collaborators

### Which categories to test?

Unit testing domain model and algorithms gives you the best return for your efforts. The resulting unit tests are highly valuable and cheap. 
- Valuable because the underlying code carries out complex or important logic, thus increasing tests’ protection against regressions.
- Cheap because the code has few collaborators (ideally, none), thus decreasing tests’ maintenance costs.

Trivial code shouldn’t be tested at all; such tests have a close-to-zero value, and might be a net negative since it means more code to maintain

As for controllers, you should test them briefly as part of a much smaller set of the overarching integration tests

Overcomplicated code will be hard to UT but still necessary to do so since they contain complicated logic, so ideally you should be refactoring them, or prevent them from being written in the first place.

## How to provides maximum value with minimum maintenance costs

Be able to:
- Recognize a valuable test (and, by extension, a test of low value) -> see "Four pillars of a good test"
- Write a valuable test, which is the focus of subsequent sections

## Anatomy of a good Unit Test

- ONE Arrange, Act, Assert section
- Having multiple Arrange, Act, Asserts is a code smell - it suggests your UT is doing too many things, or there is a problem with the class signature such that multiple public APIs need to be called just to see an observable result (bad abstraction)

## Pre-requisite information about 'mocks'

'mocks' in quotation marks because what we define as mocks in practice may not actually be the textbook definition.

### Mocks vs Test Doubles

- Test double is an overarching term that describes all kinds of non-production- ready, fake dependencies in a test.
- Mock is just one kind of such dependencies.

### Mocks vs Stubs

Test doubles can be any of this:
- mocks, spies
- Stubs, dummies, fakes

Mocks help to emulate and examine outcoming interactions. These interactions are calls the SUT makes to its dependencies to change their state.

Stubs help to emulate incoming interactions. These interactions are calls the SUT makes to its dependencies to get input data

In practice, Mockito.mock creates a mock that can perform both functionalities

### Why Use Test Doubles (or 'mocks' in practice)

Ensure that if a test fails, it is because of your code, not because of a dependency.

Also when your dependency is either shared or volatile - don’t want your test to fail due to interference

## Pre-requisite information about Shared Dependencies vs Out of Process Dependencies

- Shared dependencies are those which can have their state updated from another service
 - e.g. a DB table that is writable from different services
    - or even just a Singleton Class whose state can be changed by multiple other classes
- Out of process dependencies are those which do not reside within your service
    - e.g. DB, or a shared memory space like Redis, or even a Kafka broker

These are not mutually exclusive, e.g. in the DB case

## How to make tests less fragile

### Do not assert against interactions with stubs

Why: a call from the SUT to a stub is not part of the end result the SUT produces.

So: only use stubs to generate mock return values

### Only make test doubles for interactions with external systems.

Follow-up: if possible, only mock external systems that other applications can interact with

Why good to mock external systems: 
- So no need to do extensive setups to run your tests
- Prevent your tests from being affected by other tests

Why good to NOT mock internal communications between classes inside your system:
- if you are verifying communications between classes inside your system, you are asserting on an implementation detail
- Therefore, your test suite will fall short of the resistance-to-refactoring metric.

Fundamentally, mocking leads to some unavoidable coupling between implementation detail and observable behaviour, because when you mock, you are implicitly making assumptions on what interactions are involved in the system when generating the observable output that you want, and this can make the test suite less resistant to refactor.
But, it is necessary for
- making tests non-flaky, and
- Ensuring runtime setup time for running tests are minimal

### Only assert on observable behaviour

So if the code is refactored, it will not break easily since the output remained the same (as it should when refactor happens)

#### What is an observable behaviour (and not an implementation detail)?

For a piece of code to be part of the system’s observable behavior, it has to do one of the following things:
- Expose an operation that helps the client achieve one of its goals. An operation is a method that performs a calculation or incurs a side effect or both.
- Expose a state that helps the client achieve one of its goals. State is the current condition of the system.

Any code that does neither of these two things is an implementation detail.

Ideally, the system’s public API surface should coincide with its observable behavior, and all its implementation details should be hidden from the eyes of the clients. Such a system has a well-designed API.

## How big should each UT be?
- Size of a UT must be consistent
 - The arrange section is usually the largest of the three. It can be as large as the act and assert sections combined. If it is much larger than that, extract the arrangements into private methods within the same test class and do composition
 - The act section is normally just a single line of code. If the act consists of two or more lines, it could indicate a problem with the SUT’s public API, because client must remember to make the second method call to finish the purchase and thus lacks encapsulation.
 - Contrary to popular belief, you can have multiple Assert statements in a test. A unit in unit testing is a unit of behavior, not a unit of code. A single unit of behavior can exhibit multiple outcomes, and it’s fine to evaluate them all in one test. 
 - Having that said, you need to watch out for assertion sections that grow too large: it could be a sign of a missing abstraction in the production code. For example, instead of asserting all properties inside an object returned by the SUT, it may be better to define proper equality members in the object’s class. You can then compare the object to an expected value using a single assertion.

## Dealing with overcomplicated code

### Making implicit dependencies explicit

- Add interfaces for implicit dependencies, inject those interfaces to the unit under test, then mock those dependencies.

### Decompose overcomplicated code into a domain layer & a controller layer

- Shift responsibility of communicating with external systems to the Controller
- Ensure domain classes only depend on in-process dependencies
- Then, if you only write UTs for domain classes, you'll ideally only have to test in-process dependencies

### Ensure the controller layer really only communicates with external systems

- E.g. object instantiation should not be done at controller layer - extract that logic from it, by using an ORM or a Factory class for example.
    - For simpler cases, a static method in the existing domain classes will suffice as a factory.

## How to increase UT reusability
- Tests with similar facts can be paramterized into a single test. E.g. using TestNG’s data provider
    - caveat of this - leads to coupling between different tests, so be more liberal about having to create new cases, rather than reusing old cases

## Miscellaneous antipatterns of UTs

### Avoid if statements in a unit test
- indicates that the test is verifying too many things at once
- And makes your test harder to maintain due to increased complexity

### Don’t follow a rigid naming policy (e.g. method_situation_expectation)
- You simply can’t fit a high-level description of a complex behavior into the narrow box of such a policy.
    - Instead, name the test as if you were describing the scenario to a non-programmer who is familiar with the problem domain.