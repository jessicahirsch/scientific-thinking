---
layout: post
title:  "Regression Test Operator's Guide"
date:   2024-09-15 11:18:00 -0700
categories: jekyll update
---

## Plan

### WHAT

**Regression Testing in Theory**

Regression testing evaluates if the system under test has regressed to a devolved, or more primitive, state following a release.
In good practice, we test the changed area, or the code issued in the release.
In better practice, we test the changed area and any areas transitively affected by that change.
In best practice, we test the entire system. Optimally, no change goes untested.

![Test Replay Visual Guide]({{ site.baseurl }}/images/test-pyramid.png)

**Test Scope**

Regression tests hold the second-tier position at the top of the pyramid to suggest the space they ought to occupy. The higher the position, the less occupancy. That is meant to be an advisory.

Depending on where they execute and what they execute on, regression tests are often large in scope. That is, there are many systems and many machines that must be functioning properly for the test to succeed. Otherwise the test may fail. And the assertion could be entirely unrelated. Thus, debugging difficulties.

I’ve included an excellent diagram from _Software Engineering at Google, O'Reilly (2020)_ that represents the growing complexity of regression tests from the Test Pyramid.