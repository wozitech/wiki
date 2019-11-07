![Javascript Logo](/uploads/logos/javascript-logo.jpg "Javascript Logo"){.pagelogo}
<!-- TITLE: Javascript -->
<!-- SUBTITLE: A collection of JavaScript/TypeScript -->

# JavaScript
## Tips and Tricks
https://medium.com/@me_37286/19-ways-to-become-a-better-node-js-developer-in-2019-ffd3a8fbfe38

## hoisting and functions
Hoisting is where a delcaration can be used prior to definiion.
```
// here myfunc is used before it is declared - myfunc is hoisted
myfunc();
function myfunc () {};

// here the variable a is hoisted (var/let/const hoisted variables)
a=3;
console.log(a);
const a;

// but the following variable has value of undefined because only the declaration and not the value is hoisted
console.log(a);
const a=1;

// and the same here, "TypeError: myfunc is not a function"
myfunc();
let myFunc = function() {};
```

## Variable Scope
Global, local and block (ES6)

Without `"use strict";` though, the declaration of `b=3` within a function will make it global.

## Closures
This is where an inner function has access to the variables in an outer function's scope:
```
function generator(input) {
      var index = 0;
      return {
           next: function() {
                   if (index < input.length) {
                        index += 1;
                        return input[index - 1];
                   }
                   return "";
           } 
      }
}

var mygenerator = generator("boomerang");
mygenerator.next(); // returns "b"
mygenerator.next() // returns "o"

mygenerator = generator("toon");
mygenerator.next(); // returns "t"
```

## Arrow Functions (ES6)
```
const double = (x) => { return x * 2 };

// or
const double = (x) => x * 2;

// or given just one parameter passed the parens are not required
const double = x => x * 2;

// and if passing an object can also dereference values
const double = {width} => width * 2;

// to return an object literal
const normlaise= (a,b) => ({a, b});
// is equivalent to
function normalise(a,b) {
	return {
		a,
		b
	};
}
```

These shorthand expressions for arrow functions come in especially handing for `map`, `reduce` and `filter` Array methods, where here "smartPhones" is an array of objects having a "price" property:
```
const vatPrices = smartPhones.map({price} => price*0.2);
```

With arrow functions, `this` is a reference to the object that defined the function, whereas in a traditinal function, `this` references the object that called the function.

## use strict
> Strict mode helps out in a couple ways:
>        It catches some common coding bloopers, throwing exceptions.
>        It prevents, or throws errors, when relatively "unsafe" actions are taken (such as gaining access to the global object).
>        It disables features that are confusing or poorly thought out.

Must be quoted.

Can be applied to a file or a within a function.

* Makes debugging easier. Code errors that would otherwise have been ignored or would have failed silently will now generate errors or throw exceptions, alerting you sooner to problems in your code and directing you more quickly to their source.
* Prevents accidental globals. Without strict mode, assigning a value to an undeclared variable automatically creates a global variable with that name. This is one of the most common errors in JavaScript. In strict mode, attempting to do so throws an error.
* Eliminates this coercion. Without strict mode, a reference to a this value of null or undefined is automatically coerced to the global. This can cause many headfakes and pull-out-your-hair kind of bugs. In strict mode, referencing a a this value of null or undefined throws an error.
* Disallows duplicate parameter values. Strict mode throws an error when it detects a duplicate named argument for a function (e.g., function foo(val1, val2, val1){}), thereby catching what is almost certainly a bug in your code that you might otherwise have wasted lots of time tracking down.
	* Note: It used to be (in ECMAScript 5) that strict mode would disallow duplicate property names (e.g. var object = {foo: "bar", foo: "baz"};) but as of ECMAScript 2015 this is no longer the case.
* Makes eval() safer. There are some differences in the way eval() behaves in strict mode and in non-strict mode. Most significantly, in strict mode, variables and functions declared inside of an eval() statement are not created in the containing scope (they are created in the containing scope in non-strict mode, which can also be a common source of problems).
* Throws error on invalid usage of delete. The delete operator (used to remove properties from objects) cannot be used on non-configurable properties of the object. Non-strict code will fail silently when an attempt is made to delete a non-configurable property, whereas strict mode will throw an error in such a case.


# Destructured Assignment
```
const [a, b, ...rest] = [10, 20, 30, 40, 50];
const ({ a, b } = { a: 10, b: 20 });

function f() {
  return [1, 2];
}
const [a, b] = f(); 
```
# bind, apply and call
Take as an example:
```
var mathLib = {
    pi: 3.14,
    area: function(r) {
        return this.pi * r * r;
    },
    circumference: function(r) {
        return 2 * this.pi * r;
    }
};
```

Every function has `apply`, `call` and `bind` methods.

`call` allows for the passing of a new context to use with the function, .(it replaces the value of the `this` reference), e.g here ovveriding the value of "pi":
```
mathLib.area.call({pi: 3.14159}, 2);
```

`apply` is the same as call, except the arguments are passed as an array. With both`call` and `apply` the function is called immediately.

With `bind`, rather than calling the function, a new function reference is returned:
```
let newArea = mathlib.area.bind({pi: 3.14159});
...
newArea(2); // Now pi is 3.14159
```
# spread operator
```
function myFunction(x, y, z) { }
var args = [0, 1, 2];

myFunction.apply(null, args);
// or
myFunction(...args);
```

# Object freeze/seal
All objects in Javascript inherit `freeze` and `seal` methods:
* `freeze` prevents any changes to the object (new/deleted properties) and to the value of the properties themselves
* `seal` prevents any changes to the object (new/deleted properties), but existing properties can be changed


# async/await map
```
const list = [] //...an array filled with values

const functionWithPromise = item => { //a function that returns a promise
  return Promise.resolve('ok')
}

const anAsyncFunction = async item => {
  return await functionWithPromise(item)
}

const getData = async () => {
  return await Promise.all(list.map(item => anAsyncFunction(item)))
}

getData().then(data => {
  console.log(data)
})
```

or simply:
```
await Promise.all(mySet.map(async item => { item.doThis() });
```

# async/await reduce
```
const starterDeletedPromise = Promise.resolve(null);
await this._readyForDeletionWorkers.reduce((p, thisWorkerToDelete) => p.then(() => thisWorkerToDelete.archive(savedBy, externalTransaction, true).then(log)), starterDeletedPromise);
```

# integers are floats
In JavaScript, all integers are just floats. `console.log(0.1 + 0.2 == 0.3);` may not be true, because `0.1+0.2` may be `0.30000000001`.