---
layout: post
title:  "Regression Test Operator's Guide"
date:   2024-09-15 11:18:00 -0700
categories: jekyll update
---

## Regression Testing in Theory

Regression testing evaluates if the system under test has regressed to a devolved, or more primitive, state following a release.
In good practice, we test the changed area, or the code issued in the release.
In better practice, we test the changed area and any areas transitively affected by that change.
In best practice, we test the entire system. Optimally, no change goes untested.

![Test Pyramid]({{ site.baseurl }}/images/test-pyramid.png)

**Test Scope**

Regression tests hold the second-tier position at the top of the pyramid to suggest the space they ought to occupy. The higher the position, the less occupancy. That is meant to be an advisory.

Depending on where they execute and what they execute on, regression tests are often large in scope. That is, there are many systems and many machines that must be functioning properly for the test to succeed. Otherwise the test may fail. And the assertion could be entirely unrelated. Thus, debugging difficulties.

I’ve included an excellent diagram from _Software Engineering at Google, O'Reilly (2020)_ that represents the growing complexity of regression tests from the Test Pyramid.

![Test Scope, Software Engineering at Google (O’Reilly, 215, 2020)]({{ site.baseurl }}/images/test-scope.png)

## Regression Testing in General Practice

The act of regression testing is the action of testing your subject against a set of expectations. Those expectations are ideally created by the product’s owner, who deeply understands the behavior that the subject ought to have.

Perhaps you are accustomed to Quality Assurance engineers performing a regression test on your behalf. That is, once development is complete, testing your code against a set of acceptance criteria.

In many cases, this is done manually, with the Quality Assurance engineer checking your work performs properly against rows of test cases issued by the product’s owner.

The aim is for “quality all the way through.” In theory, we aim for _shifted-left_ testing such that we can go _without_ embedded Quality Assurance engineers. In practice, our regression testing happens at the end of the software development lifecycle (SDL), after the code has already been merged.

Automated regression testing is arguably more repeatable and accurate than a Quality Assurance engineer manually testing your code.
When done well, they can operate in place of, or as a supplement to, a QA.

### Test Cases

It is recommended that you write test cases in advance of automation. Preferably at the onset of a new project. Consult with the project’s owner. They tend to be the individuals deeply familiar with the intended behavior of the shippable code. Plain-text languages, like Gherkin, can help translate the language between product owner and engineer.

**Gherkin**

Gherkin is a popular language to write test cases, a personal favorite. Gherkin is a plain-text testing language in the behavior-driven style. It takes the the guesswork out of writing test cases by prompting you with a user, an action and an assertion. It reads naturally and yields a shorter time to automate.

If you recall from Test Scope section, restricting test size becomes beneficial within regression tests. Gherkin is meant to be read naturally.

**Given** _some condition_

**When** _some action is taken_

**Then** _there is expected behavior_

"And" may be used to denote more complex or supplemental behavior. "And" can be placed anywhere after the "Given" clause in the test case. It can be used more than once if needed.

**Given** _some condition_

**When** _some action is taken_

**And** _some additional action is taken_

**Then** _there is expected behavior_

### Test Conditions

Before you publish your tests you must understand the testing conditions necessary to qualify your tests as complete and accurate. They may require a certain user type or user state. This depends entirely on the subject.