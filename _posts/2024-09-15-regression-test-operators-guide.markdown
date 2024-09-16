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