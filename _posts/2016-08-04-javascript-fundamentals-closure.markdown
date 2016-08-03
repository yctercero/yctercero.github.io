---
layout: post
title:  "JavaScript Fundamentals - Closure"
date:   2016-08-04 22:10:47 -0400
categories: hackreactor javascript scope lexical in-memory closure
---

This is a continuation of my previous post on fundamentals. JavaScript is a pretty unique and quite flexible language. As a result, it can sometimes feel magical - some things just work and you’re not quite sure why. Or maybe what you thought should work doesn’t and you find yourself doing some brute force debugging. This is by no means a comprehensive list, but they are some fundamentals that may give you more insight as to the inner workings of JS.

At the end of the post on scope, I left you with the below problem and asked what would get console logged.

**Figure 1**

{% highlight javascript %}
function a(){
    var example = 2;
    return function b(){
      console.log(example);
    }
}

var example = 1;
var resultA = a();
var resultB = resultA();
{% endhighlight %}

The answer is ‘2’. But how can it log ‘2’ if by the time b() gets invoked, a()’s execution stack has popped off.

That’s where closures come into play. Let’s start with the official MDN definition of a closure:

>Closures are functions that refer to independent (free) variables (variables that are used locally, but defined in an enclosing scope). In other words, these functions 'remember' the environment in which they were created.

Now let’s take a look at a visual representation of the execution stack of the example above. Again, so sorry for the crappy drawings, but hopefully they get the point across.

I always imagine execution contexts as lock boxes that store our variables and keys that give us access to other boxes. If we don’t find what we’re looking for in one box, we can use the keys to search the other corresponding boxes. What keys are in a lock box are determined by the lexical scope. Every box contains at least one key, a key to the global scope.

**Figure 2**

{% highlight javascript %}
function a(){
    var example = 2;
    return function b(){
      console.log(example);
    }
}

var example = 1;
var resultA = a();
var resultB = resultA();
{% endhighlight %}
