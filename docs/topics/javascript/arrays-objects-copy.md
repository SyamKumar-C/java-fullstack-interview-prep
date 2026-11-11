# JavaScript Arrays, Objects and Copying

Objects/arrays are reference values. Two variables can point to the same object.

Spread syntax and Object.assign make shallow copies; nested objects remain shared.

```js
const original = { user: { name: 'A' } };
const copy = { ...original };
copy.user.name = 'B';
console.log(original.user.name); // B
```

structuredClone supports many common data types and cycles, but not every value (for example functions). JSON stringify/parse loses undefined, functions, symbols and special object types; it is not a general deep-copy method.

Know map, filter, reduce, find, findIndex, some, every, includes, flat, flatMap, sort, slice and splice. sort and splice mutate; slice does not.

**Interview:** Is spread a deep copy? No, it copies only the first level.
