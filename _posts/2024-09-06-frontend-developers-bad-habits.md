---
layout: post
title: "Frontend Developers bad habits"
categories: javascript
tags: javascript typescript
author: Patryk Androsiuk
date: 2024-09-06
---

Short introduction to post about bad habits of FE Devs...

#### `Array.reduce()` with spread operator (JS)

```js
const arr = ["a", "b", "c"];

// standard
arr.reduce((acc, cur) => ({ 
  ...acc, [cur]: true 
}), {}); // <- creates new object 1+n times

// optimal
arr.reduce((acc, cur) => {
  acc[cur] = true;
  return acc;
}, {}); // <- creates new object once
```

It's fine to just spreed and create new object for each item in short arrays, like is used on FE most time, but when comes to bigger set of data, that can be a big issue. Some of linters can display rule about modyfi object, especially in React, but its optimal approach.

#### `Array.find()` as default value accessor (JS)

```js
// standard
const arr = [{ id: "a" }, { id: "b" }, { id: "c" }];
const getItem = (id) => arr
  .find((it) => it.id === id); // complexity O(n)

// optimal
const hashMap = {
  a: { id: "a" },
  b: { id: "b" },
  c: { id: "c" },
};
const getItem = (id) => hashMap[id]; // complexity O(1)
```

Example use with filters in react:

```jsx
// standard
const filtered = arr.filter((it) => it.some === 'a');
const render = filtered.map((it) => (
  <ItemComponent key={it.id} id={it.id} some={it.some} />
));

// optimal
const filtered = Object.keys(hashMap)
  .filter((key) => getItem(key).some === 'a'); // eg. with useMemo
const render = filtered.map((id) => (
  <ItemComponent key={id} id={id} />
)); // utilize getItem(id) inside ItemComponent
```
When we use react context, we can store all data there, and our list component should be only care what to display, not about whole item type.

#### Enum's imports everywhere (TS)

```ts
const func = (choice: ChoiceType) => {};

// standard
enum ChoiceType = { Yes = 'yes', No = 'no' };

func(ChoiceType.Yes) // need import ChoiceType
func('yes') // gives TypeError

// optimal
const choiceType = { Yes: 'yes', No: 'no' } as const;
type ChoiceType = typeof choiceType[keyof typeof choiceType];

func(choiceType.Yes) // need import choiceType
func('yes') // but now we can use string without importing enything
```
