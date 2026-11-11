# Closures, this, call/apply/bind and Prototypes

A closure is a function with access to its lexical environment.

```js
function counter() {
  let value = 0;
  return () => ++value;
}
const next = counter();
console.log(next(), next()); // 1 2
```

Closures support private state, callbacks and function factories, but can retain memory while references remain reachable.

In a normal function, this depends on how it is called. Arrow functions do not create their own this; they capture it lexically.

call(thisArg, a, b) invokes now; apply(thisArg, [a,b]) invokes now with an array; bind(thisArg) returns a new function with bound this/leading arguments.

Objects inherit through the prototype chain. JavaScript classes are syntax built on prototypes.

**Interview:** Arrow vs normal function this? Lexically captured vs determined by call-site rules.
