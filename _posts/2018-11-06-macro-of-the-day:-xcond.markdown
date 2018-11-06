---
layout: post
date: 2018-11-06 09:40 -0500
title: Macro of the Day - xcond
categories: 
---

Reading the paredit sources I found this short simple bit:

{% highlight lisp %}
  (defmacro xcond (&rest clauses)
    "Exhaustive COND.
Signal an error if no clause matches."
    `(cond ,@clauses
           (t (error "XCOND lost."))))
{% endhighlight %}

Basically - it adds an erroring default fall through, the "exhaustive" conditional.
