---
slug: Node-introduction-post
title: Node.js is the New Black
authors: Louis
tags: [Node]
---


If you’ve been paying attention to news about web technology in the last year, you’ve most likely heard the name node.js mentioned at least once or twice. What happened next probably went like this: you asked, “What is it?” and someone (or Google) told you that it was a way to write web servers using JavaScript. If that didn’t scare you away, you might then have asked, “Why would you want to use it?” and the answer might have been along the lines of it taking advantage of non-blocking, event-driven IO to enable high concurrency in long polling or comet-based applications.

At which point you stopped asking questions. I don’t blame you. To help break apart that wall of jargon, here’s my attempt at explaining what node.js is and why you should be paying attention to it.

So here’s how it is, how it’s always been: a browser sends a request to a website. The site’s server receives the request, tracks down the requested file, performs any database queries as needed, and sends a response to the browser. In traditional web servers, such as Apache, each request causes the server to create a new system process to handle that request.

Then there was Ajax. Instead of requesting a whole new page each time, we’d only request the piece of information we actually wanted. Okay, that’s an improvement.

But now think about how you’d go about building a service like FriendFeed, where each user’s feed is updating in real time, all the time. The only way that’s possible is if each user has an active connection to the server at all times. The simplest way to do that at present is through long polling.

Long polling essentially tricks HTTP into acting like a persistent connection: as soon as the page loads, it fires off an Ajax request to the server, even if the page doesn’t want anything in particular. But, unlike a normal Ajax request, the server doesn’t respond right away. It just waits, and fires back a response only when it has something new it wants the browser to display. For example, as soon as one of your friends adds a new post, the server returns the response telling the browser to update its display. As soon as the browser receives the response, it shoots back another request. This way the browser is always waiting for a new event to happen on the server side.

Now think about what that means for a traditional web server like Apache. For each and every user connected to the site, your server has to keep a connection open. Each connection requires a process, and each of those processes will spend most of its time either sitting idle (consuming memory) or waiting on a database query to complete. This means that it’s hard to scale up to high numbers of connections without grinding to a near halt and using up all your resources.

So what’s the solution? Here’s where some of that jargon from before comes into play: specifically non-blocking and event-driven. What those terms mean in this context is less complicated than you might fear. Think of a non-blocking server as a loop: it just keeps going round and round. A request comes in, the loop grabs it, passes it along to some other process (like a database query), sets up a callback, and keeps going round, ready for the next request. It doesn’t just sit there, waiting for the database to come back with the requested info.

If the database query comes back — fine, we’ll deal with that the same way: throw a response back to the client and keep looping around. There’s theoretically no limit on how many database queries you can be waiting on, or how many clients have open requests, because you’re not spending any time waiting for them. You deal with them all in their own time, and that’s what event-driven means: the server only reacts when an event occurs. That could be a request, a file being loaded, or a query being executed — it really doesn’t matter.

FriendFeed uses a non-blocking framework written in Python, called Tornado, to do this. The nginx web server also behaves in this way. Node.js, however, has an ace up its sleeve: because it’s using JavaScript — running on Google’s crazy-fast V8 engine — it never needs to worry about whether a request it makes to another piece of code might cause the loop to block up. The reason is that JavaScript is intrinsically event-driven. Think about it: when you write JavaScript for the browser, you’re just attaching event handlers and callbacks. That’s the way the language was designed.

Node.js is still in its infancy, so if you want to write an application based on it you’ll need to do some fairly low-level coding. But with the impending arrival of WebSockets in next-generation browsers (eliminating the need for long polling altogether), this type of technology will only become more important on the Web. I hope I’ve given you the incentive to start messing around and getting your head around these concepts.

Translation：https://www.zhihu.com/question/33578075/answer/56951771


