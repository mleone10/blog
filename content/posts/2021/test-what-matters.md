---
title: "Test What Matters"
summary: "What makes a good unit test?"
date: 2021-06-01T15:22:03-04:00
tags:
  - Tech
  - Testing
---

I write a decent amount of Java at work. A lot of the code we write looks something like this:

```java
// Imports omitted

public class FooService {

    @Autowired
    public FooService(BarUtil barUtil, ApiUtil apiUtil) {
        this.barUtil = barUtil;
        this.apiUtil = apiUtil;
    }

    public Optional<String> performBar(String fizz) {
        String buzz = barUtil.bar(fizz);

        Optional<String> foo = Optional.empty();
        try {
            foo = Optional.ofNullable(apiUtil.callFooApi(buzz).getBody());
        } catch (RestException e) {
            throw FooServiceException("problem calling Foo API", e);
        }

        return foo;
    }
}
```

Basically, we have services that do some logic, call and API, and hopefully return some data. Fairly straightforward.

For the purposes of this example, let's say that `BarUtil` is a convenient utility class that does a lookup based on the value of `fizz`. The specifics don't matter, only that the call to `barUtil.bar()` has no side effects - we only care about the returned value.

My question is this: **should the unit tests for `FooService` perform any verification on the call to `barUtil.bar()`?**

No!

The correctness of `FooService` and its `performBar()` method relies on two things:

- The specific request being made to `apiUtil.callFooApi()`
- Error handling for the response from `apiUtil.callFooApi()`.

That is, we should only be verifying (asserting) that:

- The `callFooApi()` method is called with the correct parameters.
- When `callFooApi()` returns a `RestException`, a certain `FooServiceException` is thrown.
- When the response from `callFooApi()` is null, an empty `Optional` is returned.

The fact that `FooService.performBar()` outsources the `fizz` to `buzz` mapping to `barUtil.bar()` is an implementation detail. While we may very well have to mock it to ensure that an expected value is returned (you did remember to mock both the `BarUtil` and `ApiUtil`, right?), there's no need to explicitly assert that `barUtil.bar()` was called.

Why is this? Because a written specification for the `performBar()` method would only mention that the `ApiUtil` is called in a certain way, and that certain errors from that call are handled. The call to `BarUtil` (which we already know is free of side effects) is subject to change at a later date - it's a helper service used to extract some common logic from the `FooService`, but its invocation is by no means a requirement to the successful operation of the class.

Now, it may very well be that there's a team norm or other guideline that states, "all `fizz` to `buzz` mapping should use the `BarUtil`", but a unit test is not the place to assert that. Rather, some kind of static code analysis tool like Sonar may be of use in that situation. Reliance on a unit test for such an assertion, however, is liable to fail as a control - the verification might be forgotten or incorrectly copied from elsewhere in the project. A control that is subject to human error isn't much of a control at all!

## The Bigger Picture

Test-Driven Development (TDD) is a practice and methodology for producing Good™ code. It emphasizes writing tests _first_, then writing the code to satisfy the test. In doing so, the core requirements of a software project are understood and codified (literally) before doing anything else. This is in contrast to a "code-then-test" strategy that is more akin to double-entry bookkeeping - write the code, then "lock it in" with some tests.

This latter strategy is less optimal than the former. Although the engineer may enjoy protection against unintended change, the initial implementation of the code in question is done without guardrail or intent. Without first thinking about the software's requirements, it is entirely likely that whole swaths of unnecessary code will be written, committed, reviewed, and deployed without actually solving the original need. Meanwhile, the aspects of the code's functionality that we _do_ care about may be forgotten entirely.

I often encounter these sorts of mistakes and flawed testing approaches during code reviews, and I can't help but wonder if the quality of software projects the world over might be improved drastically by adhering more strictly to TDD. It's not enough to simply write tests - you have to follow the process.

Until next time,  
\- Mario
