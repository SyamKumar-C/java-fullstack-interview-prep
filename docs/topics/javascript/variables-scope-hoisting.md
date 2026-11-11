# JavaScript Variables, Scope and Hoisting

## 1. var, let and const

| Keyword | Scope | Reassign? | Redeclare in same scope? |
|---|---|---|---|
| var | Function (or global) | Yes | Yes |
| let | Block | Yes | No |
| const | Block | No binding reassignment | No |

Prefer const by default, let when reassignment is needed, and avoid var in modern code.

## 2. Scope

- Global scope: accessible throughout the relevant script/module environment.
- Function scope: variables declared with var are scoped to the function.
- Block scope: let and const are scoped to the nearest block.
- Lexical scope: scope is determined by where code is written, not where a function is called.

```js
if (true) {
  let message = 'inside';
  var status = 'visible';
}
// message is not available here
console.log(status);
```

## 3. Hoisting

Declarations are processed before code execution, but behavior differs:
- Function declarations can generally be called before their declaration.
- var is hoisted and initialized to undefined.
- let and const are hoisted but remain uninitialized until their declaration is evaluated.

## 4. Temporal Dead Zone

The Temporal Dead Zone (TDZ) is the period from entering a scope until a let/const declaration is initialized. Accessing the variable during this period throws ReferenceError.

```js
console.log(value); // ReferenceError
let value = 10;
```

## 5. Function declaration vs expression

A function declaration is hoisted with its function body. A function expression is assigned to a variable; the variable's initialization rules apply.

```js
sayHello(); // works
function sayHello() {}

const sayBye = function () {};
// sayBye(); only after initialization
```

## 6. Practical scenarios

- Use block-scoped variables in loops to avoid shared var behavior.
- Use const for references that should not be reassigned.
- Understand TDZ when debugging initialization-order errors.
- Use modules to avoid accidental global variables.

## 7. Common mistakes

- Saying let/const are not hoisted; they are hoisted but uninitialized in the TDZ.
- Assuming const makes an object immutable.
- Using var in loops with asynchronous callbacks and getting unexpected shared values.
- Confusing lexical scope with dynamic call-site scope.

## 8. Interview questions

**Q1. var vs let?** var is function-scoped and can be redeclared; let is block-scoped and cannot be redeclared in the same scope.

**Q2. Is const immutable?** The binding cannot be reassigned; an object stored in it may still be mutable.

**Q3. What is hoisting?** The language's declaration-instantiation behavior before statements execute.

**Q4. What is TDZ?** The time before a let/const binding is initialized, when accessing it throws ReferenceError.

**Q5. Are arrow functions hoisted?** The variable holding an arrow function follows its declaration rules; a const arrow function cannot be used before initialization.

## 9. Practice

Predict the output/errors for examples using var in a loop, let in a loop, function declarations and const function expressions. Explain each result using scope and initialization.
