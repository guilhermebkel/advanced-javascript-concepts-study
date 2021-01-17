# advanced-javascript-concepts-study
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
- [ Type coercion ](#type-coercion)
- [ Closures and Memory ](#closures-and-memory)
- [ Constructor Functions ](#constructor-functions)
- [ Pure Functions ](#pure-functions)
- [ Closure Functions ](#closure-functions)

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

Generally in Javascript we have three different environments which are needed to run the code:

1. **Call Stack / Memory Heap:** Common javascript code
```js
console.log("Call Stack / Memory Heap")
```

2. **Job Queue - Microtask Queue:** Asynchronous code
```js
Promise.resolve(console.log("Job Queue - Microtask Queue"))
```

3. **Callback Queue - Task Queue:** Browser functions derived from the global object
```js
setTimeout(() => console.log("Callback Queue - Task Queue"), 0)
```


So if we run the following code:
```js
setTimeout(() => console.log("Callback Queue - Task Queue"), 0) // Third

Promise.resolve(console.log("Job Queue - Microtask Queue")) // Second

console.log("Call Stack / Memory Heap") // First
```

1. The ```setTimeout(() => console.log("Callback Queue - Task Queue"), 0)``` gets into the **Web API**.

2. The ```Promise.resolve(console.log("Job Queue - Microtask Queue"))``` gets into the **Job Queue**.

3. The ```console.log("Call Stack / Memory Heap")``` gets into the **Call Stack** and gets executed.

4. The **Event Loop** do the following:
	- Is the Call Stack empty?
		- **True**: Is there any job on **Job Queue**?
			- **True**: ```Promise.resolve(console.log("Job Queue - Microtask Queue"))``` is moved from the **Job Queue** to the **Call Stack** and gets executed.

5. The **Event Loop** do the following:
	- Is the Call Stack empty?
		- **True**: Is there any job on **Job Queue**?
			- **False**: Is there any job on **Web API**?
				- **True**: ```setTimeout(() => console.log("Callback Queue - Task Queue"), 0)``` is moved from the **Web API** to the **Callback Queue** and gets executed.


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

Obs: If a data is explicitly of type **object** (usually we can guarantee it using the 'typeof' keyword), it will be instanced on **Heap Memory** and so **Passed by Reference** to methods.
```js
const firstCat = { name: "Bob" }

const secondCat = firstCat

secondCat.name = "Ana"

console.log(firstCat.name) // 'Ana'
console.log(secondCat.name) // 'Ana'
```

<a name="type-coercion"></a>

## Type coercion

Type coercion means the own language converts types to make use of statements, etc.

Per example, everything in Javascript is considered **true** except for the following:
```js
false
0
""
null
undefined
NaN
```

<a name="closures-and-memory"></a>

## Closures and Memory

We're able to reuse functions without having to create it in memory after every call. It can be done with help of **closure**.

```js
function heavyDuty(index) {
	const bigArray = new Array(7000).fill("mota")

	console.log("Created: heavyDuty")

	return bigArray[index]
}

function heavyDutyClosure() {
	const bigArray = new Array(7000).fill("mota")

	console.log("Created: heavyDutyClosure")

	return function(index) {
		return bigArray[index]
	}
}

heavyDuty(500)
heavyDuty(600)
heavyDuty(700)

const getHeavyDutyClosure = heavyDutyClosure()

getHeavyDutyClosure(500)
getHeavyDutyClosure(600)
getHeavyDutyClosure(700)

/*
 * The console.log will look like:
 *
 * Created: heavyDuty
 * Created: heavyDuty
 * Created: heavyDuty
 * Created: heavyDutyClosure
 */
```

<a name="constructor-functions"></a>

## Constructor Functions

Since functions in Javascript has inherit its prototype from object (what makes them a special type of object), we can create objects by using the "new" keyword on function as you can see below:

```js
function Elf(name, weapon) {
	this.name = name
	this.weapon = weapon
}

Elf.prototype.attack = function() {
	return "Attack with " + this.weapon
}

const peter = new Elf("Peter", "stones")
console.log(peter.attack()) // output: Attack with stones

const sam = new Elf("Sam", "fire")
console.log(sam.attack()) // output: Attack with fire

/**
 * In case we want to use a function inside a method while accessing
 * the "this" keyword, we have to add the "this" to a new variable
 * since a common function in Javascript is considered an object
 * and the "this" keyword inside it will refer to this function itself.
 */
Elf.prototype.build = function() {
	const self = this

	function building() {
		return self.name + "builds a house"
	}

	return building()
}
/**
 * We can make a simple workaround to the problem above by using
 * arrow functions, since them, different of common functions,
 * don't create an own scope, so it inherits the scope from the
 * parent function.
 */
Elf.prototype.build = function() {
	const building = () => {
		return this.name + "builds a house"
	}

	return building()
}
```

<a name="pure-functions"></a>

## Pure Functions

We call pure functions the ones which don't cause side effects all around the code. They are the core of **functional programming**.

```js
// Cause side-effects
const array = [1, 2, 3]
function removeLastItem(arr) {
	arr.pop()
}
removeLastItem(array)
console.log(array) // [1, 2]

// Doesn't cause any side-effects
const array = [1, 2, 3]
function removeLastItem(arr) {
	const copiedArray = [...arr]
	copiedArray.pop()
	return copiedArray
}
console.log(removeLastItem(array)) // [1, 2]
console.log(array) // [1, 2, 3]
```

<a name="closure-functions"></a>

## Closure Functions

Closure is such a way to create HOF (High order functions) that share **state** (variables, methods, etc) with **children functions**, what means that the children can change state of its parents.

```js
const closure = function() {
	let count = 0

	return function increment() {
		count++
		
		console.log(count)
	}
}

const incrementFunction = closure()

incrementFunction() // 1
incrementFunction() // 2
incrementFunction() // 3
```
