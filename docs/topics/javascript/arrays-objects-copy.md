# JavaScript Arrays, Objects and Copying

## 1. Arrays

Arrays are ordered, zero-indexed objects. They can contain mixed value types and can grow dynamically.

```js
const users = [{ id: 1, name: 'Asha' }, { id: 2, name: 'Ravi' }];
const active = users.filter(user => user.id === 1);
```

Common methods:
- `map`: transform each item and return a new array.
- `filter`: keep matching items.
- `find`: return the first matching item.
- `some` / `every`: test whether any/all items match.
- `reduce`: accumulate a result.
- `forEach`: perform a side effect; returns undefined.

## 2. Mutation vs non-mutation

Methods such as push, pop, splice, sort and reverse mutate the original array. Methods such as map, filter, slice and concat return a new array.

Modern non-mutating alternatives include toSorted(), toReversed() and toSpliced() in supported runtimes.

## 3. Objects and references

Objects are reference values. Assigning an object to another variable copies the reference, not the object.

```js
const a = { name: 'Asha' };
const b = a;
b.name = 'Mina';
console.log(a.name); // Mina
```

## 4. Shallow copy

Spread syntax and Object.assign create shallow copies. Nested objects remain shared.

```js
const original = { name: 'Asha', address: { city: 'Hyderabad' } };
const copy = { ...original };
copy.address.city = 'Pune';
console.log(original.address.city); // Pune
```

## 5. Deep copy

structuredClone() can deep-copy many structured-cloneable values, including cycles, but not every JavaScript value (for example, functions). JSON stringify/parse is limited: it loses or changes values such as undefined, functions, symbols, dates and special numeric values, and fails on cycles.

Choose a copy strategy based on the actual data shape.

## 6. Destructuring and spread

Destructuring extracts values; spread expands iterable elements or enumerable object properties.

```js
const { id, name } = user;
const updated = { ...user, active: true };
const combined = [...first, ...second];
```

Spread does not create a deep copy.

## 7. Practical scenarios

- Angular state update: create a new object/array when using immutable state patterns.
- API response: map DTO data into a view model.
- Remove one item without mutation: filter by ID.
- Update one property: spread the old object and override the property.

## 8. Common mistakes

- Assuming spread creates a deep copy.
- Mutating an array while iterating it.
- Using map when no transformed array is needed.
- Using reduce for simple filtering or mapping.
- Comparing objects with === and expecting structural equality.

## 9. Interview questions

**Q1. map vs forEach?** map returns a transformed array; forEach runs a callback for side effects and returns undefined.

**Q2. slice vs splice?** slice returns a selected copy without changing the original; splice changes the original array.

**Q3. Is spread a deep copy?** No, it is shallow.

**Q4. How do you remove an item immutably?** Use filter and return the resulting array.

**Q5. How do you deep-copy data?** Use structuredClone for supported data or a domain-specific copy function; avoid assuming JSON round-trip is universal.

## 10. Practice

Given an array of employee objects, create a filtered list, sort a copy by salary and update one employee without mutating the original array.
