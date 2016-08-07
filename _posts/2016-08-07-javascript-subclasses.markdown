---
layout: post
title:  "JavaScript Pseudoclassical Subclasses"
date:   2016-08-07 22:10:47 -0500
categories: javascript hackreactor subclasses pseudoclassical inheritance
---

When we talk about classes and subclasses, what are we talking about usually? Inheritance! That is, the ability of one object to have access to the properties and methods of another object.<!--excerpt-->

Before reading up on subclasses, it is helpful to understand how classes/constructors work. Essentially, they allow you to build multiple objects with similar properties and methods. If you're coming from a different language, it's important to know that classes in JavaScript are a bit unique (even with the new class keyword in ES6).

A previous HackReactor student wrote a really great blog introducing the [different implementations of functional classes]. It even includes a graphic with side by side comparisons of each. I suggest that before reading on about subclasses you take a look at his post.

Keep in mind that with each of the four different constructors - functional, functional-shared, prototypal, and pseudoclassical - the goal remains the same, but the method used to get there is what differs. From what I have gathered, there is no wrong or right way, but there are strong preferences and some, like prototypal and pseudoclassical, make better use of the unique characteristics of JavaScript.

Below, we'll be using pseudoclassical inheritance in discussing subclasses.

Let's geek out.

Right now, we have a constructor, 'Being()', with some properties and methods.

{% highlight javascript %}
var Being = function(name, characteristic){
  this.name = name;
  this.legs = 2;
  this.arms = 2;
  this.body = true;
  this.characteristic = characteristic;
}

Being.prototype.move = function(){};
Being.prototype.eat = function(){};
Being.prototype.speak = function(){
  return "Hello";
};
{% endhighlight %}

We could use it to create a bunch of little walking, talking beings:

{% highlight javascript %}
  var jim = new Being("Jim", "tall");
  var dwight = new Being("Dwight", "beet farmer");
{% endhighlight %}

Pretty boring. Well, what if we use 'Being()' as the parent class to build out a few of the different Lord of the Rings characters? Hobbits also move, eat, and speak, but they also have some pretty unique characteristics. How could we have it so that our Hobbits have the properties and methods of 'Being()' plus more? We use subclassing.

{% highlight javascript %}
var Hobbit = function(name, characteristic){ //1
  Being.call(this, name, characteristic); // 2
  this.height = "3' 5 in"; // 3
  this.feet = 'hairy'; // 4
}

Hobbit.prototype = Object.create(Being.prototype); // 5
Hobbit.prototype.constructor = Hobbit; // 6

Hobbit.prototype.goAdventure = function(){ // 7
  return false;
};

Hobbit.prototype.smokePipe = function(){}; // 8

var bilbo = new Hobbit('bilbo', 'moody');
{% endhighlight %}

Let's explore the above code a bit more to understand what is happening.

On line 1 we are declaring our 'Hobbit' constructor (remember that it is common practice to capitalize the first letter of your constructor). We are also passing in two arguments - name and characteristic.

On line 2 it looks like something kinda funky's happening. Remember we said that we wanted 'Hobbit' to have some of the same properties as 'Being'. So we could have gone ahead and rewritten those properties within 'Hobbit' ourselves. But that's not efficient, especially if we had 50 different properties to rewrite. The [.call] method allows us to invoke 'Being' while binding the keyword 'this' to the object returned by 'Hobbit'. Remember that when we instantiate an object using the 'new' keyword, like we would with 'Hobbit', in the background 'this' is assigned to a new empty object that later gets returned. That is precisely what we are passing through when we write 'Being.call(this, name, characteristics)'.

{% highlight javascript %}
var Hobbit = function(name, characteristic){ // 1
  // this = Object.create();
  Being.call(this, name, characteristic); // 2
  this.height = "3' 5 in"; // 3
  this.feet = 'hairy'; // 4
  // return this;
}
{% endhighlight %}

After calling line 2, our 'this' within 'Hobbit' that was previously an empty object now has the properties 'this.name = name', 'this.legs = 2', 'this.arms = 2', 'this.body = true', 'this.charachteristic = charachteristic'.

Lines 3 and 4 are pretty simple - we are adding a 'height' and 'feet' properties to our object.

Line 5 is a bit more interesting:

{% highlight javascript %}
Hobbit.prototype = Object.create(Being.prototype); // 5
{% endhighlight %}

Constructors (object functions) come with a special '.prototype' property that stores methods each new instance will have reference to via the [prototype chain]. Were we to try to call 'bilbo.move()' we would get an error. While 'Hobbit' is now able to inherit properties from 'Being', we have not established any other relation.

On line 5, 'Object.create(Being.prototype)' creates a new object whose own prototype is 'Being.prototype'. Now when we call bilbo.move() it will first look to see if bilbo itself has a .move method, and since it does not, it looks down the prototype chain and sees .move() on 'Being' - bilbo is now able to move!

Remember I mentioned that all constructors come with a special '.prototype' property? Well, that prototype property itself contains a '.constructor' property that stores a reference to the constructor that created the instance's prototype. After line 5, 'Hobbit's .constructor property was overwritten.

{% highlight javascript %}
bilbo.constructor(); // gives us 'Being'
{% endhighlight %}

The above would suggest that bilbo was instantiated using 'Being', but that's not accurate. There's an easy fix, add the appropriate .constructor property back in:

{% highlight javascript %}
Hobbit.prototype.constructor = Hobbit; // 6
{% endhighlight %}

Now when we call biblo.constructor() we will be able to see that 'bilbo' was instantiated using the 'Hobbit' constructor.

Lastly, on lines 7 and 8 we are adding methods to the 'Hobbit.prototype' that will only be accessible to instances of 'Hobbit' (like bilbo).

{% highlight javascript %}
Hobbit.prototype.goAdventure = function(){ // 7
  return false;
};

Hobbit.prototype.smokePipe = function(){}; // 8
{% endhighlight %}

We're almost done! What if we want instances of 'Hobbit' to speak but we want them to say more than just 'Hello' like it says on 'Being.prototype.speak()'? We can actually mask the '.speak' method on 'Being' by writing our own for 'Hobbit':

{% highlight javascript %}
Hobbit.prototype.speak = function(){
  var formal = Being.prototype.speak(); // returns "Hello"

  return formal + " I'm from the Shire";
};

bilbo.speak(); // 'Hello I'm from the Shire'
{% endhighlight %}

We could go on and on creating more subclasses (like types of Hobbits!):

{% highlight javascript %}
var Fallohides = function(name, characteristic){
  Hobbit.call(this, name, characteristic);
}

Fallohides.prototype = Object.create(Hobbit.prototype);
Fallohides.prototype.constructor = Fallohides;

Fallohides.prototype.swim = function(){
  return "Unlike other hobbits, we like to swim";
};
{% endhighlight %}


That was a whole lot - let's do a quick summary:

1. Superclasses, like 'Being', hold default properties and methods that will be able to be shared with subclasses, like 'Hobbit'.
2. Subclasses are able to inherit properties and methods from their superclass, while also creating properties and methods unique to only them.
3. Subclasses are unable to change the values of properties on the superclasses, but are able to mask them by writing a property of their own with the same name.
4. Instances of both supercalsses and subclasses are able to delegate method lookup through the prototype chain.
5. When writing 'Child.prototype = Object.create(Parent.prototype)' we are pointing 'Child''s prototype to the 'Parent' prototype.



Below is the code all together:

{% highlight javascript %}
  var Being = function(name, charachteristic){
      this.name = name;
      this.legs = 2;
      this.arms = 2;
      this.body = true;
      this.charachteristic = charachteristic;
  }

  Being.prototype.move = function(){};
  Being.prototype.eat = function(){};
  Being.prototype.speak = function(){
      return "Hello";
  };

  var jim = new Being("Jim", "tall");
  var dwight = new Being("Dwight", "beet farmer");
  console.log(jim.speak()); // 'Hello'
  console.log(jim.constructor); // Being



  var Hobbit = function(name, charachteristic){
      Being.call(this, name, charachteristic);
      this.height = "3' 5 in";
      this.feet = 'hairy';
  }

  Hobbit.prototype = Object.create(Being.prototype);
  Hobbit.prototype.constructor = Hobbit;

  Hobbit.prototype.goAdventure = function(){
      return false;
  };

  Hobbit.prototype.speak = function(){
      var formal = Being.prototype.speak(); // returns "Hello"

      return formal + " I'm from the Shire";
  };

  Hobbit.prototype.smokePipe = function(){};

  var bilbo = new Hobbit("biblo", "moody");
  console.log(bilbo.constructor); // Hobbit
  console.log(bilbo.speak()); // 'Hello I'm from the Shire'
  console.log(bilbo.body); // true



  var Fallohides = function(name, characteristic){
      Hobbit.call(this, name, characteristic);
  }

  Fallohides.prototype = Object.create(Hobbit.prototype);
  Fallohides.prototype.constructor = Fallohides;

  Fallohides.prototype.swim = function(){
      return "Unlike other hobbits, we like to swim";
  };

  var pippin = new Fallohides('Pippin', 'curious');
  console.log(pippin.constructor); // Fallohides
  console.log(pippin.speak()); // 'Hello, I'm from the Shire'
  console.log(pippin.arms); // 2
{% endhighlight %}





[different implementations of functional classes]: http://www.ryanatkinson.io/javascript-instantiation-patterns/
[prototype chain]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain#Inheritance_with_the_prototype_chain
[.call]: https://yctercero.github.io/hackreactor/javascript/this/.call/.bind/.apply/2016/08/06/javascript-fundamentals-this-.call-.apply-.bind.html