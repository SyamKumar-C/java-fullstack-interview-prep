# JavaScript Variables, Scope, Hoisting and TDZ

var is function-scoped and can be redeclared/reassigned. let is block-scoped and can be reassigned but not redeclared in the same scope. const is block-scoped and cannot be rebound; object contents can still mutate.

```js
console.log(a); // undefined
var a = 10;
// console.log(b); // ReferenceError: TDZ
let b = 20;
```

Declarations are processed before execution, but initialization differs: var starts as undefined; let/const are in the temporal dead zone until declaration evaluation. Function declarations can be called before their declaration.

Lexical scope means functions resolve variables based on where they are written.

**Interview:** Does const make an object immutable? No; it prevents rebinding, not property changes.
