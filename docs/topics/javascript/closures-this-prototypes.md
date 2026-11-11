# JavaScript Closures, this, call/apply/bind and Prototypes

## 1. Closures

A closure is a function together with access to the lexical variables from the scope where it was created. The outer function can finish, but the inner function can continue using captured variables.

```js
function createCounter() {
  let count = 0;
  return function next() {
    return ++count;
  };
}
const next = createCounter();
console.log(next()); // 1
console.log(next()); // 2
```

Each call to createCounter creates a separate lexical environment.

## 2. Practical uses of closures

- Private state and encapsulation.
- Function factories and configuration.
- Event handlers and callbacks.
- Memoization and partial application.

Closures can also retain memory. If a long-lived callback keeps references to a large object, that object remains reachable.

## 3. this in normal functions

For a normal function, `this` is determined by how the function is called (with special rules for strict mode, constructors and binding).

```js
const user = {
  name: 'Asha',
  show() { console.log(this.name); }
};
user.show(); // Asha

const fn = user.show;
fn(); // undefined in strict mode
```

Passing a method as a callback can lose its receiver. Bind it or use an arrow wrapper where appropriate.

## 4. Arrow function this

Arrow functions do not create their own this. They capture this lexically from the surrounding scope. They also do not have their own arguments object and cannot be used as constructors.

Use arrow functions for callbacks that should keep the surrounding this. Use normal methods when dynamic receiver behavior is intended.

## 5. call, apply and bind

- call(thisArg, a, b): invokes immediately with arguments listed separately.
- apply(thisArg, [a, b]): invokes immediately with arguments in an array-like value.
- bind(thisArg, a): returns a new function with bound this and optional leading arguments.

```js
function greet(prefix) {
  return prefix + ' ' + this.name;
}
const person = { name: 'Mina' };
greet.call(person, 'Hello');
greet.apply(person, ['Hello']);
const greetMina = greet.bind(person, 'Hello');
```

## 6. Prototypes

Objects can inherit properties and methods from another object through the prototype chain. Property lookup checks the object first, then its prototype, and continues upward until found or the chain ends.

JavaScript class syntax uses prototypes for instance methods. Instance fields are generally own properties.

## 7. Practical scenarios

- Closure: create a counter or a configured validator.
- this: fix a callback that loses the component/object receiver.
- bind: create a callback with a fixed context.
- Prototype: share methods across instances instead of copying a function per object.

## 8. Common mistakes

- Assuming this is determined by where a function is written.
- Assuming arrow functions have their own this.
- Forgetting that bind returns a new function.
- Creating accidental memory retention through long-lived closures.
- Confusing an object's own property with an inherited prototype property.

## 9. Interview questions

**Q1. What is a closure?** A function that retains access to its lexical environment.

**Q2. Why use closures?** For private state, callbacks and function factories.

**Q3. How is this determined in a normal function?** By the call form, subject to JavaScript's binding rules.

**Q4. Arrow vs normal function?** Arrow functions capture this lexically and cannot be constructors; normal functions have call-site this behavior.

**Q5. call vs apply vs bind?** call/apply invoke now; bind returns a new bound function.

**Q6. What is the prototype chain?** The sequence of objects searched during property lookup.

## 10. Practice

Create two independent counters using closures. Then pass an object method as a callback, observe lost this, and fix it using bind and an arrow wrapper.
