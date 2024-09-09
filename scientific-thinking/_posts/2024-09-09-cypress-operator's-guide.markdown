---
layout: post
title:  "Cypress Operator's Guide"
date:   2024-09-09 11:18:00 -0700
categories: jekyll update
---

## Cypress 101
Cypress is a friendly, frontend testing tool that resembles legacy testing tools like Selenium. It can test anything that runs in a browser. Published tests may be small and omit any dependencies.

### What does Cypress Offer?
Cypress comes complete with a built-in test runner, a Cloud environment, video recordings and test replays to closely examine reported failures.

### Test Replays
With Test Replay, Cypress Cloud regenerates the entire UI for viewing the run and debugging CI failures. You can:
- watch the simulated version of the app in realtime
- time travel, within the frontend simulator, second by second
- pause at the point-in-time where your test failed

If your test results are recorded to Cypress Cloud you do not have to worry about manually reproducing bugs. You can replay the failure directly in the Cloud.

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
