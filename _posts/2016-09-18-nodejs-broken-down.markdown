---
layout: post
title:  "Node.js Broken Down"
date:   2016-09-18 12:10:47 -0500
categories: node.js javascript server-side npm v8 
comments: true
permalink: /:title/
---

Node.js - so what? There's a lot of really great **[documentation]** on Node.js, but when I was first circling Node.js, I just wanted to know, at it's most basic, in plain English, what is it. Node.js extends JavaScript's functionality - it allows us developers to do things like read files, write to files, and interact with a database using JavaScript. These are all things that prior to Node.js, or rather without it, you would have to do with a server-side language like PHP. With Node.js, you are able to write full-stack applications, that is build out both the front and back-end, using JavaScript.

So what's the official definition of NodeJS? On their site they describe it as follows: 

> Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.

Let's break this down, and then take a look at some code.

### JavaScript Runtime

If you start googling 'runtime' you're going to get a whole lot of answers and even some differing opinions. In the context of the above Node.js definition, the runtime refers to environment set up needed in order for the program to do its job. When you are dealing with JavaScript on the client-side, part of the runtime environment includes the broswer and V8 engine - the front-end runtime provides us developers with the setup we need to write code that can interact with the DOM. On the server-side, Node.js comes with core modules which can be loaded into our document using 'require()', one of which is the V8 module, that allow us to use JavaScript to interact with our operating system.    

Plain English: Node.js provides you with the all the basic tools you need to interact with your operating system.  

### Chrome's V8 JS Engine

The **[V8 engine]** is what takes the JavaScript we write and converts it into machine code, that is, it converts it to a language that the computer processor can understand. What is interesting is that the V8 engine itself is written in C++, and in fact, so is Node.js. Node.js takes advantage of the capabilities of C++, like being able to read files, write to files, and ineract with your operating system, and makes those capabilities available to JavaScript through the V8 Engine. So in essence, there's a whole lot of C++ code listening for certain instructions written in JavaScript, for example, fs.readFile(), that triggers the C++ code block that goes ahead and reads a file for us.

In fact, Node.js includes a whole [section on addons] in their documentation. Using C++ you can write your own modules to extend functionality and load it into Node.js.

Anthony Alicea does an amazing job of explaining all this in his course, **[Learn and Understand NodeJS]**.  

Plain English: One of those tools provided, is the Chrome V8 Engine that is used to expand functionality and allows the computer to understand your JavaScript code.

### Event Driven

This one is really important to understand. It helps me to think of an event as the combination of the emitter objects and listener functions that are key to making Node.js efficient. You can think of the emitter object as an object that sends out a flare to say 'Hey, listen up, this task here was completed.' The listener functions are the code that gets executed once the particular event emmitter they are listening for sends up the flare. 

Here is where you start to see the two parts of Node.js. There's the part we developers interact with, that is our JavaScript file and then there are the processes working in the background - all the C/C++ interacting with the operating system. The library that Node.js uses to interact with the operating system is called **[libuv]**. And both of these parts, our JavaScript and libuv, deal with events of varying kind. 

An example of how this all plays out goes as follows: 1) In your JavaScript you tell it, 'Please get this document for me and read the file'. When you finish doing that log to the console 'I am finished reading the file'. In the meantime, I'm gonna continue on.' 2) This message is translated by the V8 engine and libuv goes off and does what you asked of it. 3) The operating system responds to libuv's request and an event is added to a [queue] indicating that the task was complete. 4) Libuv's event loop, which is constantly checking the queue, sees that there is something in the queue. 5) Libuv alerts our V8 Engine that 'Hey, I finished doing what you asked of me'. 6) The message 'I am finished reading the file' is logged to the console.

Obviously, this is an extremely simplified example, but that's essentially what is happening over and over again in Node.js. For an in depth look at the event loop, checkout this article, **[The Node.js Event Loop, Timers, and proces.nextTick()]**. 

Plain English: A core feature of Node.js are events that allow our JavaScript and all the C/C++ code behind it to communicate with each other what tasks need to be done, when tasks are done, and what to do when a task is complete.   

### Non-Blocking I/O

I/O stands for Input/Output. On their site, Node.js links to a great article, **[Overview of Blocking vs Non-Blocking]**, that defines I/O as follows:

> "I/O" refers primarily to interaction with the system's disk and network supported by libuv.

Non-blocking simply means that when in your JavaScript you ask for a file to be read, the V8 engine does not need to pause and wait for that task to be completed, it can continue executing the JavaScript that follows and then execute the designated callback
when libuv notifies it that the task was done. 

This is why you have likely heard that Node.js is asynchronous. An important distinction here is that the V8 Engine is synchronous, and therefore your JavaScript is still being executed one line at a time, even within Node.js. It is Node.js as a whole that is asynchronous. A 1,000 people can be asking for files at the same time and instead of having to pause the execution of your JavaScript and process each request one after another, libuv, being multi-threaded, can deal with all these requests and allow V8 to continue on with its job. Imagine how terribly long you would have to wait if Google could only process one search request at a time. 

Plain English: Node.js is able to deal with multiple tasks all at once making it extremely efficient.

### Package Ecosystem

Node.js comes with a lot of core modules, but there are many many more modules out there that you can install and share using **[npm]** and include in your project using require().

Plain English: There is more to Node.js than just what it comes with out of the box and you can access these packages of code that extend functionality through npm. 

### So What's It Look Like

So what does it take to build a server using Node.js?

{% highlight js %}
    // ===== SETUP =====
    // Using require() to pull in modules
    const http = require("http");

    // ===== CONFIG =====
    const port = 3000;
    const ip = "127.0.0.1";

    // ===== CREATE SERVER =====
    var server = http.createServer( (request, response) => {
        // Here is where you could check the request and route it to the
        // appropriate place - is the request coming from '/home' or '/profile' etc...
        const statusCode = 200;
        // Header to be sent back with status code 200 (everything's good)
        // and specifying what format the response is in
        response.writeHead(statusCode, { 'Content-Type': 'text/plain' });
        // This is what would get sent back to the client
        response.end('This is all it takes at it's most basic!');
    });

    // Start it up! 
    server.listen(port, ip);
{% endhighlight %}

### So What

So what?! Node.js is powerful and extremely efficient. It allows developers to build full-stack using only one language, JavaScript. Not only can you now use JavaScript to interact with the DOM, you can also use it to interact with your operating system. This is not to say that you should therefore not learn any other languages, you definitely should! But it's impressive to see what can be done with just JavaScript. 

I really enjoy working with Node.js and can't wait to see what is to come. 

Let's just put all the 'Plain English' snippets from above together to get a really basic description of Node.js:

> Node.js provides you with the all the basic tools you need to interact with your operating system. One of those tools provided, is the Chrome V8 Engine that is used to expand functionality and allows the computer to understand your JavaScript code. A core feature of Node.js are events that allow our JavaScript and all the C/C++ code behind it to communicate with each other what tasks need to be done, when tasks are done, and what to do when a task is complete. Node.js is able to deal with multiple tasks all at once making it extremely efficient. There is more to Node.js than just what it comes with out of the box and you can access these packages of code that extend functionality through npm. 

If you're interested at taking a deeper dive into Node.js, I suggest looking through the various links/resources sprinkled in the article:

1. [V8 Engine] - documentation
2. [Learn and Understand NodeJS] - Udemy course by Anthony Alicea
3. [Libuv] - documentation
4. [Overview of Blocking vs Non-Blocking]
5. [The Node.js Event Loop, Timers, and proces.nextTick()]


[V8 Engine]: https://developers.google.com/v8/
[V8 engine]: https://developers.google.com/v8/
[Learn and Understand NodeJS]: https://www.udemy.com/understand-nodejs/
[section on addons]: https://nodejs.org/dist/latest-v6.x/docs/api/addons.html
[libuv]: http://docs.libuv.org/en/v1.x/
[Libuv]: http://docs.libuv.org/en/v1.x/
[queue]: https://yctercero.github.io/data-structures-queues/
[Overview of Blocking vs Non-Blocking]: https://github.com/nodejs/node/blob/master/doc/topics/blocking-vs-non-blocking.md
[The Node.js Event Loop, Timers, and proces.nextTick()]: https://github.com/nodejs/node/blob/master/doc/topics/the-event-loop-timers-and-nexttick.md
[documentation]: https://nodejs.org/en/
[npm]: https://www.npmjs.com/