---
layout: post
title:  "Data Structures - Stacks"
date:   2016-09-05 10:10:47 -0500
categories: javascript data-structures hackreactor stacks
comments: true
permalink: /:title/
---

Stacks are a linear data structure that you have probably already seen used without realizing it. Have you ever clicked your browser's back button? Ever washed dishes? Then you already know more about stacks than you may have thought.  <!--excerpt-->

Stacks are commonly referred to as being  'last in, first out' (LIFO). That is, as you add to your stack, you add to the top of it, and when you grab from your stack, you grab the item at the very top, which also happens to be the item you most recently added. 

So, at a minimum, stacks allow you to add new elements to the top of the stack (push), and remove elements from the top of the stack (pop).

You'll likely see stacks implemented in one of two ways - as an array, or as a singly-linked-list. 

For now, we'll just explore its implementation as an array. We'll need a place to store our items, 'this.storage,' and a way to keep track of how many items we have in our stack, 'this.size'. 

{% highlight js %}
    var Stack = function() {
        this.storage = {};
        this.size = 0;
    };
{% endhighlight %}


Now, we need a way to add to the stack. We should be able to feed in the value of the item we are adding and have this method add it to the top of the stack. Remember that we also need to keep track of how many items are in the stack, so each time we add we will need to increase the 'size' variable.

{% highlight js %}
    Stack.prototype.push = function(value) {
        this.storage[this.size] = value;
        this.size++;
    };
{% endhighlight %}

In addition to using  'size' to keep track of the number of items, we can use it as the key with which we store our items in  'storage'. 

![/downloads/push.png](/downloads/push.png){:class="img-responsive"}

Now, we need a way to remove the most recent value from the stack. If we know that 'size' is the key for the next item to be added to the stack, we know that 'size' - 1, should give us the key for the most recently added item. 

{% highlight js %}
    Stack.prototype.pop = function() {
        // Check that there are items in the stack to be removed
        if (this.size && this.size--) {
            var stored = this.storage[this.size];
        }
        // Delete from storage 
        delete this.storage[this.size];
        // return the item that was just popped off
        return stored;
    };
{% endhighlight %}

![/downloads/pop.png](/downloads/pop.png){:class="img-responsive"}

To put it all together, we get the following:

{% highlight js %}
    var Stack = function() {
        this.count = 0;
        this.storage = {};
    };

    Stack.prototype.push = function (value) {
        this.storage[this.count] = value;
        this.count++;
    };
    Stack.prototype.pop = function () {
        if (this.count && this.count--) {
            var stored = this.storage[this.count];
        }
  
        delete this.storage[this.count];
        return stored;
    };
{% endhighlight %}

![/downloads/stack.png](/downloads/stack.png){:class="img-responsive"}

I'll keep touching on some data structures every couple days. 

When I heard we would be learning data structures at HackReactor, what came to mind was the demagogan from 'Stranger Things'. Data structures seemed like this incredibly complex, kinda intimidating topic that I knew I couldn't ignore. And yes, data strucutres can definitely get complex, which is why we start small and go from there. If a particular data structure interests you, dig deeper! 
