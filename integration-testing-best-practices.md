# Notes on Integration Testing

## What is an integration test

Anything that doesn’t fit all 3 criteria for a unit test

## Purpose of Integration Testing

Ensure your system works as a whole, rather than in vacuum

## Role of Integration Testing

Unit tests cover the domain model, while integration tests check the code that glues that domain model with out-of-process dependencies, usually the controllers

## Benefits of integration test cases
- Greater code coverage per test case than UTs, better for regression testing
- More detached from the production code and therefore have better resistance to refactoring than UTs.

## Why minimise integration test cases
Difficult to maintain, due to:
- The necessity to keep the out-of-process dependencies operational
- The greater number of collaborators involved, which inflates the test’s size

## Pre-requisite for writing good integration tests: Types of out-of-process dependencies
- Managed dependencies (out-of-process dependencies you have full control over)—These dependencies are only accessible through your application, e.g. db
- Unmanaged dependencies (out-of-process dependencies you don’t have full control over)— Interactions with such dependencies are observable externally. Examples include an SMTP server and a message bus: both produce side effects visible to other applications.

When in doubt - as long as the out-of-process dependency is used by another application, it is an unmanaged dependency

## General guidelines for writing an integration test
- Select the longest happy path in order to verify interactions with all out-of-process dependencies. 
- If there’s no one path that goes through all such interactions, write additional integration tests—as many as needed to capture communications with every external system.
- Also add integration tests for edge cases that can’t be covered by unit tests
- It’s better to not write a test at all than to write a bad test. A test that doesn’t provide significant value is a bad test.
- Integration tests cover controllers; unit tests cover algorithms and the domain model
- As much as possible - only add one ‘act’ per test case. Having multiple act sections in a test - It’s a sign that this test checks multiple units of behaviour, which, in turn, hinders the test’s maintainability.
- Use real instances of managed out-of-process dependencies and verify their final states; replace unmanaged out-of-process dependencies with mocks and verify interactions with it. 
    - Do this because your tests can be flaky if out-of-process dependencies are messed with at test runtime by another application
    - Why cannot mock managed dependency:
        - test real behaviour as much as possible, mocking will make testing against regression less reliable
        - Mocking out a managed dependency compromises the integration tests’ resistance to refactoring.
    - If cannot use a real instance of a managed out-of-process dependency, don’t write integration tests at all for them - focus exclusively on unit testing the domain model. Remember to always put all your tests under close scrutiny. Tests that don’t provide a high enough value should have no place in your test suite.
