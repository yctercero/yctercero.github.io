---
layout: post
title:  "Node.js Broken Down"
date:   2016-09-18 12:10:47 -0500
categories: node.js javascript server-side npm v8 
comments: true
permalink: /:title/
---

Node.js - let's break it down and answer a simple question, so what? We've all heard of it, tried it, maybe even actively use it, but do we really know what it is? Would you be able to explain it in plain and simple English? <!--excerpt-->After using it for some time myself, and reading lots of really great **[documentation]**, this is what I got - **Node.js is a platform which extends JavaScript's basic functionality.** It allows us to do really cool things like read files, write to files, and interact with a database...using only JavaScript! These are all things that, prior to Node.js, you would have had to do with a server-side language like PHP. Luckily, Node.js allows us to maintain language consistency through all of our code and write both the front AND back-end in only Jacascript.

So what's the official definition of Node.js? Their documentation describes it as follows: 

> Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.

Let's break this definition down and tackle it one bit at a time. We'll analyze the following Node.js attributes: its runtime, V8 engine, event-driven model, non-blocking I/O model, and package ecosystem. 

### JavaScript Runtime

In the context of the above Node.js definition, the runtime refers to the environment set up needed in order for the program to do its job. If you start googling "Javascript runtime" you're going to get a lot of definitions, answers, and differing opinions on what it actually is. This variation is largely due to the inherent difference that exists between client side and server side JavaScript. 

On the client side, we are writing JavaScript that will be interacting with the browser, specifically, the DOM. On the server side, using Node.js, we are writing JavaScript to interact with the operating system. In both cases, we utilize the V8 engine (explained in the next section), but Node.js also comes with additional core modules that expand functionality and are loaded into our document using require statements. The confusion comes in deciding what makes up the JavaScript runtime. Some see the V8 engine as being separate from the JavaScript runtime, and others include it in their definition.    

Regardless of how you decide to define the JavaScript runtime, it is important to understand the different roles that JavaScript plays in the client side and on the server side.  

**Plain English: Node.js provides you with the all the basic tools you need to interact with your operating system.**  

### Chrome's V8 JS Engine

The **[V8 engine]** is what takes the awesome and bug-free JavaScript we write and converts it into machine code, a language that your computer processor can understand. Digging more into the documentation, I found that the V8 engine itself is written in C++, and in fact, so is Node.js! This came as a surprise because if you've worked with client side libraries and frameworks, you know that those are written in JavaScript. Node.js takes advantage of the capabilities of C++, like file reading and writing, as well as operating system interactions. Node.js makes those capabilities available to JavaScript through the V8 Engine. 

So in essence, there's a whole lot of C++ code listening for certain instructions written in JavaScript. Let's take the function 'fs.readFile()' as an example. the function triggers some hidden C++ code which reads the specified file, and returns its contents for us to display or manipulate.

What's great is that Node.js includes a whole **[section on addons]** in their documentation. If you're familiar with C++, you can use it to write your own modules to extend functionality and load it into Node.js.

As an aside, Anthony Alicea does an amazing job of explaining this in great detail in his course, **[Learn and Understand NodeJS]**.  

**Plain English: One of those tools provided, is the Chrome V8 Engine that is used to expand JavaScript's core functionality and allows the computer to understand your JavaScript code.**

### Event Driven

The event-driven model can be a bit tricky to understand, but it is a really important concept because it is in great part what makes Node.js so efficient. Node.js has something called emitter objects. You can think of the emitter object as an object that sends out a flare to say 'Hey, listen up, this task here was completed.' Node.js also has listener functions. The listener functions is the code that gets executed once the particular event emmitter they are listening for sends up the flare. It helps me to think of an event as the combination of an emitter object and listener function.

When discussing events in Node.js, there are two types of events. There are the events that occur in the half we directly interact with, that is, our JavaScript file. Then there are also the events being processed in the background by the C/C++ interacting with the operating system. To be more precise, **[libuv]** is the library that Node.js uses to interact with the operating system and which deals with these background events. 

So how do these two halves, our JavaScript and libuv, interact? Here's a really simplified example of how our JavaScript interacts with libuv: 

1. In your JavaScript you tell it, 'Please get this document for me and read the file. When you finish doing that log to the console "I am finished reading the file." In the meantime, I'm gonna continue on executing this JavaScript one line at a time.' 
2. This message is processed by the V8 engine and libuv sends off a request to the operating system - 'Please find and grab this file for me. When you're done, let me know.' 
3. The operating system responds to libuv's request and an event is added to libuv's [queue] indicating that the task was complete. 
4. Libuv's event loop, which is constantly checking the queue, sees that there is something in the queue. 
5. Libuv processes it and alerts our V8 Engine that 'Hey, I finished doing what you asked of me. Here's that file you asked for.'
6. The message 'I am finished reading the file' is logged to the console.

Obviously, this is an extremely simplified example, but that's essentially what is happening over and over again behind the scenes. Libuv's event loop is actually really interesting to read about. I know, nerding out a bit here, but if you're interested, I recomment you checkout this article, **[The Node.js Event Loop, Timers, and proces.nextTick()]**. 

**Plain English: A core feature of Node.js are events that allow our JavaScript and all the C/C++ code behind it to communicate with each other. Events help Node.js know what tasks need to be done, when tasks are done, and what to do when a task is complete.**   

### Non-Blocking I/O

Non-blocking I/O, that sounds kind of technical and complicated...it's not! I/O stands for Input/Output. I was trying to find a good way of describing it, but this article, **[Overview of Blocking vs Non-Blocking]**, has a really great, easy to understand definition:

> "I/O" refers primarily to interaction with the system's disk and network supported by libuv.

Remember in the example above we saw that libuv interacts with the operating system? Those interactions are what I/O refers to.

Now, for non-blocking. Simply put, it means that numerous interactions can be occuring between libuv and the operating system at the *same time*. This is why you have likely heard that Node.js is asynchronous, and it is! However, it is important to understand that while libuv is able to run asynchronously, JavaScript remains synchronous - the V8 engine still executes the JavaScript one line at a time. But how can these two things work in tandem? We use callbacks to allow JavaScript to continue executing while libuv goes off an completes a task we asked it to do. When that task is finished, JavaScript will invoke the callback you gave it. At no point is JavaScript doing two tasks at one time. 

But this is great! It means that we are able to interact with our operating system in a really efficient manner. Imagine how terribly long you would have to wait if Google could only process one search request at a time. 

**Plain English: Node.js is able to deal with multiple tasks all at once making it extremely efficient and asynchronous.**

### Package Ecosystem

Endless possibilities! If you've worked with Node.js you've probably become familiar with npm. Node.js comes with a lot of core modules out of the box, but there are many many more modules out there that you can install and share using **[npm]** and then include in your project using require statements.

Why not just include all modules at once? There are *a lot* of modules and often times you only need a few to run your program. Having all these modules installed that are not being used would be a waste of space and decrease your program's efficiency. 

**Plain English: There is more to Node.js than what it comes with out of the box, and you can access these packages of code that extend functionality using npm.** 

### So What's It Look Like

So what does it take to build a server using Node.js? It's surprisingly simple! You may have imagined hundreds of lines of code and some really complex stuff, but at its most basic, it's just a few lines.

{% highlight js %}
    // ===== SETUP =====
    // Using require() to pull in modules
    const http = require("http");

    // ===== CONFIG =====
    const port = 3000;
    const ip = "127.0.0.1";

    // ===== CREATE SERVER =====
    const server = http.createServer( (request, response) => {
        // Here is where you could check the request and route it to the
        // appropriate place - is the request coming from '/home' or '/profile' etc...
        const statusCode = 200;
        // Header to be sent back with status code 200 (everything's good)
        // and specifying what format the response is in
        response.writeHead(statusCode, { 'Content-Type': 'text/plain' });
        // This is what would get sent back to the client
        response.end('This is all it takes at it\'s most basic!');
    });

    // Start it up! 
    server.listen(port, ip);
{% endhighlight %}

### So What?

So what?! Node.js is a powerful and extremely efficient platform. It takes advantage of another language's features (C/C++), and allows us to build full-stack apps using only one language... JavaScript! Not only can you now use JavaScript to interact with the DOM, you can also use it to interact with your operating system. This is not to say that you should therefore not learn any other languages, you definitely should! But it's impressive to see what can be done with just JavaScript. It's pretty cool to think of the ways you can use one programming language to enhance another. I personally really enjoy working with Node.js and can't wait to see what is to come. 

To wrap up let's just put all the 'Plain English' snippets from above together to get a really basic description of Node.js:

1. Node.js provides you with the all the basic tools you need to interact with your operating system. 
2. One of those tools provided, is the Chrome V8 Engine that is used to expand JavaScript's core functionality and allows the computer to understand your JavaScript code. 
3. A core feature of Node.js are events that allow our JavaScript and all the C/C++ code behind it to communicate with each other. Events help Node.js know what tasks need to be done, when tasks are done, and what to do when a task is complete. 
4. Node.js is able to deal with multiple tasks all at once making it extremely efficient and asynchronous. 
5. There is more to Node.js than just what it comes with out of the box and you can access these packages of code that extend functionality using npm. 


### Resources 

If you're interested at taking a deeper dive into Node.js, I suggest looking through the various links/resources sprinkled in the article:

1. [V8 Engine] - documentation
2. [Learn and Understand NodeJS] - Udemy course by Anthony Alicea
3. [Libuv] - documentation
4. [Overview of Blocking vs Non-Blocking]
5. [The Node.js Event Loop, Timers, and proces.nextTick()]


**Special thanks to [@Michael Olorunnisola] for helping edit!


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
[@Micael Olorunnisola]: http://www.inductivereasoning.michaelolorunnisola.com/