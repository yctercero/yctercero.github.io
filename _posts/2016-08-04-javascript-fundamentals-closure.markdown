---
layout: post
title:  "JavaScript Fundamentals - Closure"
date:   2016-08-04 22:10:47 -0400
categories: hackreactor javascript scope lexical in-memory closure
---

This is a continuation of my previous post on fundamentals. JavaScript is a pretty unique and quite flexible language. As a result, it can sometimes feel magical - some things just work and you’re not quite sure why. <!--excerpt-->Or maybe what you thought should work doesn’t and you find yourself doing some brute force debugging. This is by no means a comprehensive list, but they are some fundamentals that may give you more insight as to the inner workings of JS.

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

![/downloads/scopeIllust.png](/downloads/scopeIllust.png){:class="img-responsive"}

1. Global execution context is created
2. a() is invoked and it’s result (function b()) is stored in the variable resultA
3. a() is done running and pops off the stack
4. b() is invoked (resultA()) and a new execution context is created
5. b() tries to console.log(example), but it does not find it inside of its own execution context
6. But wait! b() still has a key to access a()’s execution context
7. Here’s where closure comes in and saves the day. Even though a() has been popped off, JS creates a closure, which I picture as an almost ghost like version of a()’s context and because b() has that key to a(), it can go ahead and access var example = 2.
8. ‘2’ gets console logged
9. b() gets popped off

So what would be the closures in the example above. I would argue that it is 1:

**Figure 3**

![/downloads/closure1.png](/downloads/closure1.png){:class="img-responsive"}

However, my understanding is that you could also consider a closure between the functions and the global environment since var example = 1; would be the ‘free’ variable that function a() is ‘remembering’.

**Figure 4**

![/downloads/closure2.png](/downloads/closure2.png){:class="img-responsive"}

The reason I think of it as only one closure is that while you can create a closure lexically, in the sense that you decide where in your code to create what functions and variables, closures really come into play during execution. Consider the execution stack on Figure 2, to the execution stack below:

**Figure 5**

{% highlight javascript %}
function b(){
  console.log(example);
}

function a(){
    var example = 2;
    b();
}

var example = 1;
a()
{% endhighlight %}

![/downloads/closure5.png](/downloads/closure5.png){:class="img-responsive"}

A closure was necessary in the first instance (Figure 2) because when b() was invoked, a()’s execution context was no longer ‘on’ the stack. No closure is needed in Figure 5 between a() and the global environment because there is no danger that the global context would be popped off before the invocation of a().

May call for some more research!