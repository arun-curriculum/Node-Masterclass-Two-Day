# Node Masterclass

## A Re-Introduction to JavaScript

- JavaScript is an [ever changing minefield](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f).
- JavaScript is currently at the ES6 standard, which replaces ES5 and adds many features.
- There are also many features of ES7 that are becoming supported on a nightly basis: http://node.green.
- JavaScript is isomorphic, meaning that it has the ability to run on the server and the client.

## Node JS

- Node JS is a JavaScript runtime aimed at allowing JavaScript to perform server-type operations.
- This runtime is based on the Google Chrome V8 Engine, which as the name suggests, also powers Google Chrome.
- Server-side Node is asynchronous just like client-side JavaScript.

![Event Loop](img/event-loop.jpg)

## Node REPL

- Node JS comes with Read-Evaluate-Print-Loop (REPL) software built-in.
- This allows you as the developer to write code in the terminal and run it immediately.

```bash
node
```

```bash
> console.log("Hello World");
Hello World
```

## Running Node Files

- If you want to run .js files with Node you can simply use terminal commands to accomplish this:

app.js

```javascript
console.log("Hello World");
```

Bash:

```bash
node app.js
```

## Node Modules

- CommonJS modules are the most widely-used way to create a share functionality in Node applications.
- They work by taking objects, functions, and more, and packaging them into a format that can be "required" into other JavaScript files.
- Let's take an example:

adder.js

```javascript
const adder = (num1, num2) => {
    return num1 + num2;
}

module.exports = adder;
```

index.js

```javascript
const adder = require("./adder");

console.log(adder(2, 3)); // 5
```

- In this example, the function "adder" is adder to a property of the global "module" called "exports", which enables the use of the "require" keyword in index.js.

## NPM and Module Dependencies

- NPM allows developers to use third-party Node modules in their code thanks to a centralized repository that can be accessed via the command line.
- NPM comes with a dependency management file called package.json that references all related modules in both development and production environments.
- We can generate this file by the `npm init` command.

## Unit Testing with Mocha-Chai

- Mocha is a spec runner framework for Node that allows us to write tests with asynchronous code and run them in the terminal.
- Chai is an assertion library that gives us the "expect" syntax along with a number of matchers to test cases.
- Let's create a test for our adder module, which will negate the need to manually test it by creating an index.js and console.logging behavior:

```javascript
const expect = require("chai").expect;
const adder = require("../adder");

describe("Testing module functionality", () => {
    it("Should add two numbers", (done) => {
        expect(adder(2, 3)).to.equal(5);
    });
});
```

## Module Lab

- Please refer to the [project located here](https://github.com/arun-projects/Node-TDD-Algorithms-1).
- Note: Each of your functions should be a separate module.

## Asynchronous Programming with Node

- Node is simply JavaScript, and as such can perform actions asynchronously.
- ES6 / ES7 has given us a few different approaches to handling asynchronous operations effectively.
- We will look at promises and async/await.
- We will do this through the [request module](https://www.npmjs.com/package/request).
- We will make a request to the [OpenWeather API](https://openweathermap.org/current) to retrieve weather conditions in your current city:

```
https://api.openweathermap.org/data/2.5/weather?units=imperial&q=San Francisco, CA
```

- You can use this appid:

```
appid=0ceda40f34407a7f88d4657fdf79f91d
```

## Option 1 - Callbacks

- The callback is the classic approach to asynchronous programming.
- It is essentially a function that gets called after an asynchronous operation is complete.
- Let's see an example of this:

```javascript
const request = require("request");

const getWeather = (callback) => {
  request({
  	method: "GET",
  	uri: "https://api.openweathermap.org/data/2.5/weather",
  	qs: {
  		units: "imperial",
  		q: "San Francisco",
  		appid: "0ceda40f34407a7f88d4657fdf79f91d"
  	}
  }, function(err, response, body) {
    if (err) {
      return callback(err, null);
    }

    callback(null, body);
  });
}

getWeather((err, data) => {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});
```

## Option 2 - Promises

- Promises are returned from asynchronous functions, and provide `.then()` and `.catch()` methods to handle success and error cases respectively.

```javascript
const request = require("request");

const getWeather = () => {
  return new Promise((resolve, reject) => {
    request({
    	method: "GET",
    	uri: "https://api.openweathermap.org/data/2.5/weather",
    	qs: {
    		units: "imperial",
    		q: "San Francisco",
    		appid: "0ceda40f34407a7f88d4657fdf79f91d"
    	}
    }, function(err, response, body) {
      if (err) {
        return reject(err);
      }

      resolve(body);
    });
  });
}

getWeather()
.then((data) => {
  console.log(data);
})
.catch((err) => {
  console.log(err);
});
```

## Option 3 - Async/Await

- Async/Await is new functionality given to us by ES7 to handle returned promises more conveniently.
- Instead of a typical `.then()` / `.catch()` format, we have an `async` function with an `await` keyword.
- Under the hood this acts the same way as promises, but it easier to read.

```javascript
const request = require("request");

const getWeather = () => {
  return new Promise((resolve, reject) => {
    request({
    	method: "GET",
    	uri: "https://api.openweathermap.org/data/2.5/weather",
    	qs: {
    		units: "imperial",
    		q: "San Francisco",
    		appid: "0ceda40f34407a7f88d4657fdf79f91d"
    	}
    }, function(err, response, body) {
      if (err) {
        return reject(err);
      }

      resolve(body);
    });
  });
}

const showWeather = async () => {
  try {
    const weatherPromise = await getWeather();

    console.log(weatherPromise);
  } catch(err) {
    console.log(err);
  }
}

showWeather();
```

## Async Lab

- For this lab we will be working with async/await to create a simple countdown timer.
- Step 1: Create a variable called "count" that will start at 20
- Step 2: Create a function that returns a promise that decreases the count by 1 after 1 second.
- Step 3: Create an async function that calls the function 20 times and awaits for the promise to be resolved.
- Step 4: In the second function, create a console.log statement to show the count in the terminal.