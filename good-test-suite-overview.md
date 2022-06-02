# Notes on Test Suites

## What is the goal of testing

Enable sustainable growth of the software project - it’s much harder to push new features to a large codebase and this gets worse without tests because you don't know if something will break

Your initial rate of pushing out code will be slower, but in theory it is supposed to pay off in the long run. With bad tests, the net benefit can be zero or even negative though, because maintaining the test suites will also become a problem.

So, to enable sustainable project growth, you have to exclusively focus on high-quality tests — those are the only type of tests that are worth keeping in the test suite.

## Properties of a successful test suite

- It’s integrated into the development cycle.
- It provides maximum value with minimum maintenance costs.

## How to provides maximum value with minimum maintenance costs - Four pillars of a good test

A good test has the following four attributes:
- Protection against regressions
- Resistance to refactoring
- Fast feedback
- Maintainability

These four attributes, when multiplied together, determine the value of a test -> none of them can be too lousy

### Protection against regressions

To evaluate how well a test scores on the metric of protecting against regressions, you need to take into account the following:
- The amount of code that is executed during the test
- The complexity of that code
- The code’s domain significance

Generally, the larger the amount of code that gets executed, the higher the chance that the test will reveal a regression, assuming good assertions

### Resistance to refactoring

Refactoring means changing existing code without modifying its observable behavior.

To evaluate how well a test scores on the metric of resisting to refactoring, you need to look at how many false positives the test generates (when you do a refactor of the code). The fewer, the better. Why this is the case:
- Developers start ignoring test case failures
- Trust in the test suite gets lost, and developers stop trying to refactor the code being tested

The more the test is coupled to the implementation details of the system under test (SUT), the more false alarms it generates. So, only verify end result the SUT delivers: its observable behavior, not the steps it takes to do that. Tests should approach SUT verification from the end user’s point of view and check only the outcome meaningful to that end user. Everything else must be disregarded (more on this topic in chapter 5).

The best way to structure a test is to make it tell a story about the problem domain. Should such a test fail, that failure would mean there’s a disconnect between the story and the actual application behavior. It’s the only type of test failure that benefits you — such failures are always on point and help you quickly understand what went wrong. All other failures are just noise that steer your attention away from things that matter.

Note: False positives don’t have as much of a negative effect in the beginning of the project, but they become increasingly important as the project grows: as important as false negatives.


### Fast feedback and Maintainability

Tests that run quickly drastically shortens the feedback loop, reducing the cost of fixing a bug almost to zero. 

Maintainability consists of two major components:
- How hard it is to understand the test — This component is related to the size of the test. The fewer lines of code in the test (if not artificially compressed), the more readable the test is. It’s also easier to change a small test when needed. Don’t cut corners when writing tests; treat the test code as a first-class citizen.
- How hard it is to run the test — If the test works with out-of-process dependencies, you have to spend time keeping those dependencies operational: reboot the database server, resolve network connectivity issues, and so on.

## Is it possible to create an ideal test?

Answer: no, each individual test case has tradeoffs on these four traits, but a good balance can be struck with an appropriately sized test suite, e.g. by following the Test Pyramid pattern.

Tradeoffs per test type:
- E2E: good for resistance against refactoring and testing regressions, bad for execution time
- Trivial tests: fast feedback, good resistance to refactoring, unlikely to reveal any regressions.
- Brittle tests: fast feedback and good for testing regressions, bad for resistance against refactor

## Suggested proportion of unit testing and functional testing

The ratio between unit and integration tests can differ depending on the project’s specifics, but the general rule of thumb is the following: check as many of the business scenario’s edge cases as possible with unit tests; use integration tests to cover one happy path, as well as any edge cases that can’t be covered by unit tests.

But, the Test Pyramid can take different shapes depending on the project’s complexity. Simple applications have little (if any) code in the domain model and algorithms quadrant. As a result, tests form a rectangle instead of a pyramid, with an equal number of unit and integration tests. In the most trivial cases, you might have no unit tests whatsoever.

## Purpose of coverage metrics

Coverage metrics can only signal the amount of code tested, not the quality of the tests.

Furthermore, they can never be used to track code paths through external libraries. Not that they should - the point is, coverage metrics can’t possibly tell whether your tests are exhaustive; nor can they say if you have enough tests.

So, the best way to view a coverage metric is as a weak indicator for how well tested your code is, but not as a goal for the test suite in and of itself.

## Should you test logging?

1. If side effects are meant to be observed by anyone other than developers themselves, then logging is an observable behaviour and thus must be tested.
2. If the only audience is the developers, then it’s an implementation detail that can be freely modified without anyone noticing, in which case it shouldn’t be tested.

Because logging involves unmanaged out-of-process dependencies, when it comes to testing it, you need to use mocks to verify interactions between your application and the log storage.
