---
layout: post
title: "Frontend Developers bad habits"
categories: javascript
author: Patryk Androsiuk
date: 2024-09-06
---

Short introduction to post about bad habits of FE Devs...

#### `Array.reduce()` with spread operator
```ts
const arr = ["a", "b", "c"];

// bad
arr.reduce((acc, cur) => ({ ...accu, [cur]: true }), {}); // <- creates new object 1+n times

// good
arr.reduce((acc, cur) => {
  acc[cur] = true;
  return acc;
}, {}); // <- creates new object once
```
It's fine to just spreed and create new object for each item in short arrays, like is used on FE most time, but when comes to bigger set of data, that can be a big issue. Some of linters can display rule about modyfi object, especially in React, but its good approach.

#### `Array.find()` as default object access
```ts
// bad
const arr = [{ id: "a" }, { id: "b" }, { id: "c" }];
const getItem = (id: string) => arr.find(it => it.id === id)

// good
const hashMap = {
  a: { id: "a" }, 
  b: { id: "b" }, 
  c: { id: "c" }
}
const getItem = (id: string) => hashMap[id]
```