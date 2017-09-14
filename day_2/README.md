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