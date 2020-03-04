# advanced-javascript-concepts
:dragon_face: A deep study about advanced javascript concepts

## Summary

- [ How the browser understands javascript code ](#how-the-browser-understands-javascript)
- [ Stack and Heap Memory ](#stack-and-heap-memory)
- [ Garbage collection ](#garbage-collection)
- [ Main causes of memory leak ](#main-causes-of-memory-leak)
- [ Javascript runtime ](#javascript-runtime)

<a name="javascript-foundation"></a>

## How the browser understands javascript code

There is a **Engine** which is capable of making some work to make the javascript code to be compiled into machine code during its execution and so interpreted dynamically.

Every browser has its own engine, today the most famous is called **V8 Engine** and it is used by Google Chrome and new versions of Internet Explorer.

Below you can see the steps followed by the **Engine** in order to make the javascript code interpretable by computer:

- ```Parser```
	- The javascript code is parsed based on its syntax (variable declarations, function declarations, etc) into a **AST - Abstract Syntax Tree** (it is basically a JSON of the code formated into a tree).

After the step above, the following ones happen at the same time:

- ```Interpreter```
	- Turns the **AST** into **Bytecode** (not a machine code, but executable with engine help) that gets executed by the computer on the fly (it is a work around to make the code to be executed as fast as possible by the browser, since it is not actually optimized and a real machine code but is executable anyway).

- ```Compiler```
	- While the **Interpreter** is running, it makes optimizations on the created **Bytecode**. After making the needed optimizations, it replaces the **old non-optimized Bytecode** with the new changes.

So, with the steps above (called JIT Compiler - Just In Time Compiler), the code gets executed on the fly while making needed optimizations to improve the application speed.

<a name="stack-and-heap-memory"></a>

## Stack and Heap Memory

The common declarations below will be done with **Stack Memory**:
```js
// declaring numbers and strings
const x = 1;
const y = "mota";

// calling a function in the simple way
function something() {
	console.log("something");
};
something();
```

And the following ones will be done with **Heap Memory**:
```js
// object declaration
const user = {
	name: "mota"
};

// using the 'new' keyword to call a function
function userName(userName) {
	this.userName = userName;
};
new userName("mota");

// using the 'new' keyword to call a class
class UserLastName {};
new UserLastName();
```

<a name="garbage-collection"></a>

## Garbage collection
In Javascript, the garbage collection occurs automatically (since it is a manual job on another languages like C).

Basically, the garbage collector works on variables that are not pointing to any memory space, per example:

```js
var user = {
	name: "Mota",
	lastName: "Bromonschenkel"
}

user = 1

// The garbage collector will clear the last value and keep the new one
```

<a name="main-causes-of-memory-leak"></a>

## Main causes of memory leak

A memory leak occurs when we start using some space in memory and it doesn't get back to us with the garbage collector help.

In generally there are mainly causes of memory leak, per example:
- Global variables
- Event listeners
- setInterval functions

<a name="javascript-runtime"></a>

## Javascript runtime

Generally the common javascript code will run on Call Stack memory and browser functions (derived from the object **window**) will run on the Web API (what means this kinda code will run asynchronously).

Example of Web API asynchronous functions: ```fetch, setTimeout```.

So if we run the following code:
```js
console.log(1) // first

setTimeout(() => console.log(2), 3000) // third

console.log(3) // second
```

- The ```console.log(1)``` gets into the Call Stack and gets executed.
- The ```setTimeout(() => console.log(2), 3000)``` gets into the Web API.
- The ```console.log(3)``` gets into the Call Stack and gets executed.
- After 3s the ```setTimeout(() => console.log(2), 3000)``` gets into the Callback Queue.
- The Callback Queue checks if the Call Stack is empty and if the response is **yes** the ```setTimeout(() => console.log(2), 3000)``` goes to Call Stack and ```console.log(2)``` gets executed.