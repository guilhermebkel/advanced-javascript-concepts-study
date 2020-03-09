# advanced-javascript-concepts
:dragon_face: A deep study about advanced javascript concepts

## Summary

- [ How the browser understands javascript code ](#how-the-browser-understands-javascript)
- [ Stack and Heap Memory ](#stack-and-heap-memory)
- [ Garbage collection ](#garbage-collection)
- [ Main causes of memory leak ](#main-causes-of-memory-leak)
- [ Javascript runtime ](#javascript-runtime)
- [ Hoisting ](#hoisting)
- [ Function Invocation ](#function-invocation)
- [ Strict Mode ](#strict-mode)
- [ Block scope ](#block-scope)
- [ Manipulating 'this' keyword ](#manipulating-this-keyword)
- [ Javascript types ](#javascript-types)

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

<a name="hoisting"></a>

## Hoisting

A hoist occurs when we call a variable/function before defining it (and it works without errors), because the Javascript Engine reads all code during the creation phase, asks for some space on the heap memory to put some declared variables/functions (in order to improve first time execution speed) and then executes it. Below you can see an example of hoisting:
```js
console.log(lastName) // Prints 'mota'
var lastName = "mota"

console.log(getFirstName()) // Prints 'guilherme'
function getFirstName() {
	return "guilherme"
}
```

If you want to avoid hoisting, you can do the following:
```js
console.log(firstName) // Error 'firstName' is not defined'
const firstName = "guilherme"

console.log(lastName) // Error 'lastName' is not defined'
let lastName = "mota"

console.log(getNumber()) // Error 'getNumber' is not defined'
(function getNumber() {
		return 1
})
```

<a name="function-invocation"></a>

## Function Invocation

We can define a function as an expression or a declaration. The main difference between them is that **Function Expressions** get defined during the code execution and **Function Declarations** get defined during the code parsing.

```js
// Function Expression
const printName = () => {
	console.log("Guilherme")
}

// Function Declaration
function printLastName() {
	console.log("Mota")
}
```

<a name="strict-mode"></a>

## Strict mode

If we run the following code, it will not trigger any error:

```js
function test() {
	tested = 1
}

test() // Runs without errors
```

In order to prevent it from happening, we can add the 'use strict' keyword that will force some ECMAScript validations:

```js
'use strict'

function test() {
	tested = 1
}

test() // Triggers error: 'tested is not defined'
```

<a name="block-scope"></a>

## Block scope

When we create some statement using brackets, we're only able to access variables if they are declared with 'var' keyword

```js
// Works
if (5 > 4) {
	var a = 1
}
console.log(a)

// Does not work
if (5 > 4) {
	const a = 1
}
console.log(a)

// Does not work
if (5 > 4) {
	let a = 1
}
console.log(a)

```

<a name="manipulating-this-keyword"></a>

## Manipulating 'this' keyword

When dealing with **this** keyword, we have the following methods (that are created during the function instancing): **call, apply, bind**.

```js
const wizard = {
	name: "Merlin",
	health: 100,
	heal(firstHp, secondHp) {
		this.health += firstHp + secondHp;
	}
}

const archer = {
	name: "Robin Hood",
	health: 30
}

// Use function 'heal' of wizard on archer
// passing '100' as the function argument
wizard.heal.call(archer, 100, 100) // Heals by 200
wizard.heal.apply(archer, [100, 100]) // Heals by 200

// Returns the updated method, without executing it
archer.heal = wizard.heal.bind(archer, 100)
archer.heal(100) // Heals archer by 200 hp
```

<a name="javascript-types"></a>

## Javascript types

- Primitive Types: Usually represents a single type on memory
```js
typeof 5 // number
typeof true // boolean
typeof "mota" // string
typeof undefined // undefined
typeof null // object
typeof Symbol("mota") // symbol
```

- Non Primitive Types: Does not represent a type on memory
```js
typeof {} // object
typeof [] // object
typeof function(){} // function (but it is actually an object)
```

Even with these multiple types, we can say that **almost everything in Javascript** is an **object**, since the wrappers used to create other types are objects, per example:
```js
// true has property to string even being an boolean
true.toString() // 'true'

// Because the above is the same as using the boolean wrapper that is an object
Boolean(true).toString() // 'true'
```