<!-- TITLE: Typescript -->
<!-- SUBTITLE: A collection of Typescript -->

# Inheritance
```
class MyClass {
	private MyType _myAttr;
	integer
}
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