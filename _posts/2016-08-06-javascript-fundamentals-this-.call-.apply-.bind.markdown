---
layout: post
title:  "JavaScript Fundamentals - 'this', .call, .apply, and .bind"
date:   2016-08-06 17:10:47 -0400
categories: hackreactor javascript this .call .bind .apply
---

This (not ‘this’) is a continuation of my previous post on fundamentals.

As I mention at the beginning of each of this series’ posts, JavaScript is a pretty unique and quite flexible language. As a result, it can sometimes feel magical - some things just work and you’re not quite sure why. <!--excerpt-->Or maybe what you thought should work doesn’t and you find yourself doing some brute force debugging. These posts are by no means a comprehensive list, but they are some fundamentals that may give you more insight as to the inner workings of JS.

The keyword ‘this’ can, at times, feel impossible to understand. Before understanding its use and how to figure out what it referred to, I tended to try and avoid it where possible or console log like crazy to try and make sense of it.

Let’s dive in.

### ‘this’ and the Global Object

When you run your code, a global execution context is created that includes a global object, and a binding for ‘this’. Let’s say we have an empty document and all we add is:

{% highlight javascript %}
var whatIsThis = this;
{% endhighlight %}

If we run the code and console log whatIsThis, what does it show?

![/downloads/console1.png](/downloads/console1.png){:class="img-responsive"}

It shows a ‘Window’ object with a bunch of properties and methods attached.

Fun aside, if you open up the ‘Window’ object and scroll way down you’ll find that the 'Widnow' object now has a 'whatIsThis' property with the value ‘Window':

![/downloads/console2.png](/downloads/console2.png){:class="img-responsive"}

As you might imagine, calling ‘this’ by itself in the global scope (that is, not inside a function or method), isn’t all too useful, but it does exist!

You can also now see what’s meant when people say that variables, functions and objects created within the global scope are added to the global object.

### ‘this’ and Functions/Methods

**Functions**

Above we saw that when you run your code, a global execution context is created with a global object and access to the ‘this’ keyword. Similarly, each time a function is invoked and a new execution context is created, we are given access to the ‘this’ keyword. When I say that we are ‘given access’ I mean that the keyword ‘this’ is not something we ourselves have to declare or create.

When a free function (meaning, it is not a method) is invoked, ‘this’ refers to the global object:

{% highlight javascript %}
var a = function(){
  return this;
}

a() // returns the 'Window' object
{% endhighlight %}

We could log ‘this’ from a function within a function, within a function, and ‘this’ would still refer to the global object:

{% highlight javascript %}
var a = function(){
  function b(){
    function c(){
      console.log(this);
    }
  }
}

a() // returns the 'Window' object
{% endhighlight %}

Why would this still log ‘Window’? Even though the function doing the console log is nested within other functions, it still follows the rule that when a free function is invoked, ‘this’ refers to the global object(‘window’ in your browsers);

**Methods**

Things do change up when we are no longer invoking a free function like above, but instead a method - a function stored within an object as a value.

Methods are invoked using a call time dot (something.method()). When invoking a method, ‘this’ is bound to the object found to the left of the dot at call time. Key word (or phrase really) here is 'at call time' - you check what was to the left of the dot when the method was invoked, not where the method was created or passed in as an argument. We’ll get to that later.

Let’s take a look:

{% highlight javascript %}
var a = {
  fruit: "I like",
  method: function(){
    this.fruit = "I like bananas";
  }
}

a.method();
console.log(a.fruit); // Logs 'I like bananas'
{% endhighlight %}

Why were we able to change the ‘fruit’ property to “I like bananas”? If we find where the method (conveniently named ‘method’) is invoked, to the left of the call time dot we find ‘a’. So when ‘method’ ran, we were essentially saying a.fruit = 'I like bananas'. The object 'a' being to the left of the call time dot became what the keyword 'this' was bound to.

**Beware!**

Normally, your code is not as simple as these examples, and you may find functions nested within methods.

{% highlight javascript %}
var a = {
  fruit: "I like",
  method: function(){
    this.fruit = "I like bananas";
    var apples = function(){
      this.fruit = 'Actually, I like apples';
    };
    apples();
  }
}

a.method();
console.log(a.fruit); // Logs 'I like bananas'
{% endhighlight %}

This code also logs “I like bananas” - shouldn’t it log “Actually, I like apples”. No, no. This is kind of a frustrating feature, but one that when examined, makes sense. When is apples() invoked? Does it have anything to the right of the call time dot? Does it even have a call time dot? Nope. We previously said that when free functions are invoked, ‘this’ refers to the global object. In fact let's log the value of 'this' within apples() to see what shows up.

{% highlight javascript %}
var a = {
  fruit: "I like",
  method: function(){
    this.fruit = "I like bananas";
    var apples = function(){
      this.fruit = 'Actually, I like apples';
      console.log(this);
    };
    apples();
  }
}

a.method(); //Logs the 'Window' object
{% endhighlight %}



There is, however, a way to have 'this' refer to object 'a' from within apples().

{% highlight javascript %}
var a = {
  fruit: "I like",
  method: function(){
    this.fruit = "I like bananas";
    var that = this;
    var apples = function(){
      that.fruit = 'Actually, I like apples';
    };
    apples();
  }
}

a.method();
console.log(a.fruit) //Logs 'Actually, I like apples'
{% endhighlight %}

We saw that from within ‘method’, ‘this’ is bound to the ‘a’ object, so we can store that reference in a variable (var that). Because of lexical [scoping], apples() will have access to the variable ‘that’ and can now use it within to access and modify the properties on the ‘a’ object.


### ‘this’ and Constructors

Object constructors can be thought of as functions that create numerous instances of an object with shared methods. An easy tell for identifying object constructors is that the first letter is capitalized.

Let’s say we have an object constructor 'Phone':

{% highlight javascript %}
var Phone = function(memory, color, frontCamera, backCamera){
  this.memory = memory;
  this.color = color;
  this.frontCamera = frontCamera;
  this.backCamera = backCamera;
};
{% endhighlight %}

Now let’s create some phones:

{% highlight javascript %}
var flipPhone = new Phone('2GB', 'red', false, true);
var iPhone = new Phone('16GB', 'black', true, true);
{% endhighlight %}

Under the hood, when you call ‘new Phone(...)’ : 1) the keyword ‘this’ is assigned an empty object (first commented out line),  2) properties are assigned to the new object, and 3) the new object is returned.

{% highlight javascript %}
var Phone = function(memory, color, frontCamera, backCamera){
  // this = Object.create();
  this.memory = memory;
  this.color = color;
  this.frontCamera = frontCamera;
  this.backCamera = backCamera;
  // return this;
};
{% endhighlight %}

So, when invoking an object constructor, the keyword ‘this’ refers to an empty object that is created in the background by JS when you use the ‘new’ keyword. It then proceeds to assign the properties - in this case, those properties include memory, color, frontCamera, and backCamera.

You will see the use of constructors quite a lot in JS.

### .call() & .apply()

You may or may not have noticed that all functions have access to these three methods - .call(), .apply(), and .bind(). So what are they for?

{% highlight javascript %}
 var greeting =  function(firstName, lastName){
    console.log("Woof, my name is " + firstName lastName + " and I am a " + this.color + " " + this.breed);
  }

var dog = {
  color: "brown",
  breed: "mutt"
}

greeting(); // 'Woof, my name is undefined undefined and I am a undefined undefined.''
{% endhighlight %}

Were we to invoke greeting() as is, the ‘this’ keyword’ would be pointing to the global object. It would be looking for the property ‘color’ and the property ‘breed’ in the global object. How can we get it so that when we call greeting(), the ‘this’ keyword is bound to the dog object?

Calling dog.greeting() won’t work because greeting() is not a method of dog.

Here is where we can use .call or .apply to help us out. I’m going to invoke greeting as follows:

{% highlight javascript %}
greeting.call(dog, “Roo”, “Parker”); // 'Woof, my name is Roo Parker and I am a brown mutt.'
{% endhighlight %}

The first parameter passed to .call is what you want the keyword ‘this’ to get bound to when calling greeting() - in other words, you are passing through the context. The other parameters that follow are the arguments to be passed into the greet() function.

.apply() works exactly the same way except for one difference - it takes the context and an **array** of arguments:

{% highlight javascript %}
greeting.apply(dog, [“Roo”, “Parker”]); // 'Woof, my name is Roo Parker and I am a brown mutt.'
{% endhighlight %}

That’s it!

### Bound Functions

The .bind() function is a bit trickier than .call() and .apply(). The .bind() function creates an anonymous function that sets the value of the ‘this’ keyword as the first parameter passed in. This may make more sense when we look at some examples.

Let’s say we want to store a method to call at some later point.

{% highlight javascript %}
var dog = {
  color: 'brown',
  breed: 'mutt',
  greeting: function(){
    console.log("Hello, I am a " + this.color + " " + this.breed);
  }
}

var greet = dog.greeting;
greet();
{% endhighlight %}

What do you expect to be logged?

What gets logged is “Hello, I am a undefined undefined”. When we assign greet to dog.greeting, we are saying:

{% highlight javascript %}
var greet = function(){
    console.log("Hello, I am a " + this.color + " " + this.breed);
 };
{% endhighlight %}

Do you remember what ‘this’ is bound to within a free function? It is bound to the global object, which does not contain a color or breed property.

So let’s instead try using .bind(). We pass in the object we want ‘this’ to be bound to as the first parameter. Were greeting() itself to accept parameters, we would add these as well within .bind().

{% highlight javascript %}
var dog = {
  color: "brown",
  breed: "mutt",
  greeting: function(){
    console.log("Hello, I am a " + this.color + " " + this.breed);
  }
}

var greet = dog.greeting;

var boundGreet = greet.bind(dog);
boundGreet(); // Hello I am a brown mutt
{% endhighlight %}

This time around we get the result we hoped for. We can now breath easy knowing that when we call boundGreet(), the ‘this’ keyword will be bound to the ‘dog’ object.

But wait! One last question, why do we need to invoke boundGreet? Well, because when we assign boundGreet to greet.bind(dog) we are saying:

{% highlight javascript %}
var boundGreet = function(){
  greet.call(dog);
};
{% endhighlight %}

Hopefully by now you’re able to see two important unique qualities to .bind():
1. .bind() does not immediately invoke a method like .call and .apply
2. .bind() creates an anonymous function that assigns the value of the ‘this’ keyword as the first parameter passed in


### Takeaway
That was a lot of info so let’s sum it up:
- ‘this’ used on the global object or within a free function is bound to the window at call time
- ‘this’ used inside a constructor is bound to the object created in the background by JS when you use the ‘new’ keyword to instantiate an object
- ‘this’ within methods is bound to what is to the left of the call time dot upon invocation of the method
- .call is used to explicitly bind ‘this’ to an object and pass through arguments
- .apply is used exactly like .call except that you pass the parameters within an array


[scoping]: https://yctercero.github.io/hackreactor/javascript/scope/lexical/in-memory/2016/08/03/jacvascript-fundamentals-scope.html
