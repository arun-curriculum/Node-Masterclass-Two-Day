# Web Development with Node

Node can also be used as a web development technology. Actions like listening for HTTP requests and responding with appropriate functionality can be handled using Node's built-in tools.

## Overview of Web Applications

- Middleware: Software that is run between the request and response cycle.
- Model: Models contain data persistence layer interaction code.
- View: Views contain information pertaining to presenting data. This is usually HTML or compiled to HTML.
- Controller: Controllers are basically the brain of the application. They interact with the model to retrieve and store data, and are responsible for the eventual presentation of the data.
- Server: A server is a piece of software that intercepts a HTTP request, routes it to the correct functionality, and then issues a response to the client.

## Simple Web Server in Node

- To create a web server in node we will need a callback function since Node is event-driven.
- The `createServer` method allows us to listen for incoming HTTP requests.

```javascript
const http = require("http");

const greet = (req, res) => {
	res.writeHead(200, { "Content-Type": "text/html" });
	res.write("Hello World");
	res.end();
}

const server = http.createServer(greet);

server.listen(3000);
```

- The downside of this however is that every request no matter what will run the same functionality.

## Routing

- Routing is the ability to change the response to the client based on the request type and url.
- To do this, we will need two pieces of information from the request object - `method` and the `url`.

```javascript
const http = require("http");

const greet = (req, res) => {
	if (/^\/hello$/.test(req.url) && req.method === "GET") {
		res.writeHead(200, { "Content-Type": "text/html" });
		res.write("Hello World!");
		res.end();
	} else {
		res.writeHead(404, { "Content-Type": "text/html" });
		res.write("No routes match");
		res.end();
	}
}

const server = http.createServer(greet);

server.listen(3000);
```

## Express JS

- When it comes to web applications with Node, it is challenging to implement middleware, routing, etc. from scratch everytime.
- Express JS is a light framework for Node that does not enforce any coding patterns or conventions, but provides an easy to use API for routing and middleware handling.
- The above example can be distilled down to this with Express:

```javascript
const express = require("express");
const app = express();

app.get("/hello", (req, res) => {
  res.send("Hello World");
});

app.all("*", (req, res) => {
  res.status(404).send("No routes match");
});

app.listen(3000);
```

## Route Parameters

- Route parameters should be used when you need to pass data through the URL but need to trigger a new set of data to be returned.
- Route parameters are often used to uniquely identify a resource, and as such often contain data like IDs.
- Here is an example:

```
http://localhost:3000/books/:id
```

- Here `id` is a route parameter that will change with different requests.
- It can be used to identify exactly which book we are referring to.
- We can access these data as an attribute of the `req` object:

```javascript
app.get("/hello/:name", (req, res) => {
    res.send(`Hello ${req.params.name}!`);
});
```

## Query String Parameters

- Query string parameters should be used when you need to pass data through the URL but want to still interact with the same data set.
- The best example of this is to use query string parameters to apply filters to data. You're still working with the same data set, but a filtered result is returned.
- Here is an example:

```
http://localhost:3000/books?title=The+Great+Gatsby
```

- We can access these data as an attribute of the `req` object:

```javascript
app.get("/books", (req, res) => {
    res.send(`The title is ${req.query.title}`);
});
```

## Server-Side Rendering vs Client-Side Rendering

- When the server issues the final response to the client, the format is generally one of two options - HTML or JSON.
- If HTML is the response type, it is up to the server to determine how to present data and render it on the view.
- If JSON is the response type, it is up to the browser to determine how to present the data, and eventually incorporate that into a HTML presentation.
- We will first look at server-side rendering through the EJS module:

#### Install with NPM:

```
npm install ejs --save
```

#### Set up EJS for use:

```javascript
app.set("view engine", "ejs");
```

- Now instead of using `res.send` to return HTML responses we can use `res.render`:

```javascript
res.render("hello", {
    name: req.params.name
});
```

- If you use server-side rendering, you will also need to tell Express where to find static assets like CSS stylesheets and client-side JavaScript:

```javascript
app.use(express.static("./assets"));
```

- Client-side rendering is much easier from a Node perspective because we are simply returning raw data back to the client instead of a fully-formed presentational response.
- Usually these raw data are in the form of JSON. Let's take an example from the above:

```javascript
res.status(200).json({
    message: `Hello ${req.params.name}`
});
```

- At this point it is up to the client-side code to determine what to do with these data.

## User Manager Code-Along

- We will be demonstrating server-side rendering through coding along a user manager using the [request module](https://www.npmjs.com/package/request) from before.
- We will be using the Person API located here: http://myapi-profstream.herokuapp.com
- We will be using the user interface here: https://github.com/arun-projects/User-Manager

## Book Manager Lab

- In this lab we will be practicing server-side rendering by creating a book manager application.
- We will be using the Book API located here: http://myapi-profstream.herokuapp.com
- We will be using the user interface here: https://github.com/arun-projects/Book-Manager
- Your job is to implement the show all books and add new book functionality.

## MVC Structure and Data Persistence with MongoDB

- The Model-View-Controller pattern is a popular way of developing applications due to its ability to keep code clean, modular, and testable.
- We will look at using the MVC pattern by recreating the user manager using MongoDB, Mongoose, and Node through a TDD approach.
- First we will have to connect to MongoDB and set up our model through Mongoose:

```javascript
const mongoose = require("mongoose");

if (process.env.NODE_ENV === "test") {
  mongoose.connect("mongodb://localhost/user_manager_test");
} else {
  mongoose.connect("mongodb://localhost/user_manager");
}

const Person = mongoose.model("Person", {
  firstname: String,
  lastname: String,
  username: String,
  email: String
});

module.exports = Person;
```

## MVC and Data Persistence Lab

- In this lab you will create an API using Node, MongoDB, and Mongoose to serve data for the book manager application.
- Your job is to create this using the TDD approach.

## Blog.Ly Lab

- To put it all together you will now be developing a backend to serve a frontend written in React JS.
- Please refer to the project here: https://github.com/arun-projects/Blog-Ly-Backend

## Websockets

- One of the most powerful uses for Node is its ability to handle seamless "real-time" experiences.
- Sockets are a way for a browser and server to communicate without the standard request-response cycle.
- Chat clients, real-time data feeds, and operational dashboards are some examples of where sockets have been used effectively.

## How it Basically Works

- A client makes an initial request out to the server and a "handshake" is created - AKA a connection has been established.
- This "handshake" is given a unique socket with a unique ID.
- Essentially this request never completes and remains open for the duration of the session.
- Every further request-response simulation is done via a manifestation of a JavaScript event.
- In a perfect world this is how things would always operate with sockets but certain factors such as browser incompatibility and more can interfere with a proper handshake. As a result, a more brute-force approach of "polling" may be required.

## Socket.io

- Socket.io is a library that essentially manages browser capabilities to connect a client with a server through web sockets in the most ideal way possible.
- It can switch between polling and sockets automatically and basically automate the handshake process.
- Socket.io works on the client and the server side to achieve seamless interaction.

#### Server Setup

- In order to use Node and Express to communicate over websockets we will have to install socket.io:

```javascript
const express = require("express");
const app = express();

const server = require("http").createServer(app);
const io = require("socket.io")(server);

server.listen(3000);
```

- We can now use the `io` instance to listen for events over all sockets:

```javascript
io.on("connection", (socket) => {
    // "socket" now refers to a specific connection that was made from the client
});
```

- Individual sockets receive emitted data that can be listened for:

```javascript
socket.on("some event", (data) => {
    // "data" refers to any payload coming from the broadcast
});
```

- Sockets can also emit data back to all other connections (including your own):

```javascript
io.emit("some event", params);
```

- Or simply to everyone but yours:

```javascript
socket.broadcast.emit("some event", params);
```

#### Client Setup

- The client setup is very similar to the server setup.
- We will first link the socket.io library: https://socket.io
- Next let's connect to our socket server:

```javascript
var socket = io.connect("server url");
```

- Notice that we do not define an "io" variable. This is because the client is only connected to one socket vs the server that has to connect to many different sockets.
- The client can now listen for events:

```javascript
socket.on("some event", function(data) {
    // "data" represents the payload of data coming over the socket
});
```

- The client can also emit events:

```javascript
socket.emit("some event", data);
```

- Notice that there is no need to "broadcast" the data since these data will only be emitted from this one socket back to the server.

## Real-Time Chat Lab

- In this lab we will be working to create a real-time chat application.
- Please refer to the project here: https://github.com/arun-projects/Socket-Chat