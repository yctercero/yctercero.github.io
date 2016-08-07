---
layout: post
title:  "JavaScript Fundamentals - Scope"
date:   2016-08-02 22:10:47 -0500
categories: hackreactor javascript scope lexical in-memory
---

JavaScript is a pretty unique and quite flexible language. As a result, it can sometimes feel magical - some things just work and you’re not quite sure why. Or maybe what you thought should work doesn’t and you find yourself doing some brute force debugging. <!--excerpt-->This is by no means a comprehensive list, but they are some fundamentals that may give you more insight as to the inner workings of JS.

Scope can be broken up into two parts - lexical and in-memory.

### Lexical
You can think of lexical scope as the literal place in your code where you can validly access a variable.  The example I’ll be using is from Anthony Alicea’s JavaScript: Understanding the Weird Parts. Let’s look at the example.

**Figure 1**

{% highlight javascript %}
function b(){
  console.log(example);
}

function a(){
  var example = 2;
  b();
}

var example = 1;
a();
{% endhighlight %}

What would you expect to get logged?

If you expected ‘1’ to be logged, you’re correct! But, why exactly? Why wouldn’t it be undefined since the variable example is not defined within b()? Or maybe, why would it not be ‘2’  since b() is being invoked from within a() and in a() the variable example is assigned ‘2’?

Every time you create a function in your code, everything between those function’s brackets is within a new scope. It may be easier to visualize it as follows:

**Figure 2**

![/downloads/scope1.png](/downloads/scope1.png){:class="img-responsive"}

If every color box represents a lexical scope - the light blue would be the global scope and the others are the local scopes (local to the functions). We can see that in Figure 2 even though b() was invoked within a(), its lexical scope does not overlap with that of a(), and as a result has no access to the ‘var example = 2’ created within a(). However, it does overlap with the blue box - the global scope - and so is able to access ‘var example = 1’.

What if, on the other hand, we had this?

**Figure 3**

![/downloads/ex2.png](/downloads/ex2.png){:class="img-responsive"}

What gets logged is ‘2’ because you can see that function b()’s scope (green) overlaps with a()’s (redish). Again, ‘2’ is logged not because of where b() is invoked, but because, in the code, b() was created within a().


### In-Memory

Often, in-memory scope is referred to as the execution context. You can think of execution contexts as structures that store variables and their corresponding values as the program runs. While lexical scopes are created as you write your code, execution contexts are created during execution of the code, as the name might suggest.

Let’s keep working with the same example (I apologize ahead of time for the terrible drawings). I always imagine execution contexts as lock boxes that store our variables and keys that give us access to other boxes (execution contexts). If we don’t find what we’re looking for in one box, we can use the keys to search the other corresponding boxes. What keys are in a lock box and the order in which to use the keys is decided by the function’s lexical position. Every box contains at least one key, a key to the global scope.

Example 1 (Figure 4)

{% highlight javascript %}
function b(){
  console.log(example);
}

function a(){
  var example = 2;
  b();
}

var example = 1;
a();
{% endhighlight %}

![/downloads/1b.png](/downloads/1b.png){:class="img-responsive"}

1. Global execution context created
2. a() is invoked and new execution context is created
3. Within a(), b() is invoked and new execution context is created
4. b() tries to console.log(example) but does not find the value of 'example' in its own execution context
BUT it has a key to the global execution context, so it searches there and finds that example is assigned the number 1 and logs it
5. b() is finished and pops off the stack
6. a() is finished and pops off the stack


__Example 2 (Figure 5)__
{% highlight javascript %}

function a(){
    var example = 2;
    function b(){
      console.log(example);
    }
    b();
    b();
}

var example = 1;
a();
{% endhighlight %}

![/downloads/bb.png](/downloads/bb.png){:class="img-responsive"}

1. Global execution context created
2. a() is invoked and new execution context is created
3. Within a(), b() is invoked and new execution context is created
4. b() tries to console.log(example) but does not find the value of 'example' in its own execution context
BUT it has a key to a()’s execution context so it searches there and finds that 'example' is assigned the number 2 and logs it
5. b() is finished and pops off the stack
6. b() is invoked again and a new execution context, completely separate from that of the first b() is created and it has it’s own set of keys to a()’s execution context and the global execution context
7. Repeat steps 4 & 5
8. b() is finished and pops off the stack
9. a() is finished and pops off the stack

The execution stacks for each of these examples seem similiar, even though the lexical scopes are quite different. In fact, they result in two different values being logged. That’s why it is important to recognize that when speaking of scopes, there are actually two parts to it (lexical and in-memory).

Last, I’ll leave you with this as an introduction to closures. What gets logged?

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
