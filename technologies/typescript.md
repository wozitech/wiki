---
title: typescript
description: 
published: true
date: 2019-11-25T14:04:03.274Z
tags: 
---

![Typescript Logo](/uploads/logos/typescript-logo.png "Typescript Logo"){.pagelogo}
<!-- TITLE: Typescript -->
<!-- SUBTITLE: A collection of Typescript -->

# nodejs
* Create a node.js project using typescript: https://medium.com/javascript-in-plain-english/typescript-with-node-and-express-js-why-when-and-how-eb6bc73edd5d.

[reference project and instructions for a node.js typescript project](https://github.com/microsoft/TypeScript-Node-Starter)

# Inheritance
```
interface IType {
	methodA():void {}
	methodB(param1:String):Integer {}
}
// an interface can extend another interface
interface ITypeTwo extend IType {
	methodD():Integer {}
}

class MyBaseClass {
	constructor(param1:IType) {
	}
	
	// method with callback (taking a String and return Integer)
	methodC(cbk(thisParam:String) => Integer):void {}
}

class ThisClass implements ITypeTwo {
	methodA():void {}
	methodB(param1:String):Integer {}
	methodD():Integer {}
	
	private myMethod():void {}
}

class MyClass extends MyBaseClass {
	private MyType _myAttr;
	String myVar;
	
	// a class must provide concretes for all constructors
	constructor() {
	}
	constructor(param1:IType) {
		super(param1);
	}
}
```

> When a class both implements and extends, then extends must come first.
```
class MyClass extends BaseClass implements IFunc
```
# Import/Export
Exports:
```
export const MY_PI = 3.14959;
export interface Iface {
};
export class MyClass {
};

class MyClassTwo {
};
export { MyClassTwo };
export { MyClassTwo as ThatClass };

// re-export
export { ThatClass as ThisClass} from './thatClass';

export default class DefClass {
}

// CommonJS style
export = MyClass;
```

Imports:
```
import {MY_PI} from './types';
import {MY_PI as THIS_PI} from './types';

// multiple exports in the file
import {MY_PI, MyClassTwo} from './types';

// import default export
import DefClass from '/.types';

// import the entire set of definitions
import * as MyTypes from './types';
let myVar = new MyTypes.MyClass();

// CommonJS style - must use require
import MyType = require("./type");
let MyInst = new MyType();
```