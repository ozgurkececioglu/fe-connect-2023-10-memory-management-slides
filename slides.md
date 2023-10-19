---
theme: seriph
layout: cover
title: Memory Management for JS and browser
---

# Memory Management for JavaScript and browser

by Ozgur

---
layout: image-right
image: './images/transistors-city.jpeg'
---

# Browser is a VM

- Browser is a VM that runs JavaScript code.
- It has a memory management system that allocates memory for variables, objects, and functions and frees memory when they are not used anymore.
- It has its own memory part in operating system's memory.
- Browser uses a garbage collector to free memory. Garbage collector is a part of the memory management system.

---
layout: center
---

# Memory Management in JavaScript

- In high level languages like JavaScript, memory management is done automatically by the browser.
- Browser allocates memory for variables, objects, and functions and frees memory when they are not used anymore.
- `References` are used to access memory.
- The system that frees memory is called `garbage collector`.

---
layout: image-right
image: './images/garbage-collector.jpeg'
---

# Garbage Collector

- Garbage collector is an mechanism that frees memory when it is not used anymore.
- It uses an algorithm called `mark and sweep`.

<br>

### Mark and Sweep:

- It marks all the variables, objects, and functions that are used.
- It consistently checks these marks and frees the ones that are not used anymore.
<!-- After some time, it takes time to mark and check for garbage -->

---
layout: image-right
image: './images/memory-management.jpeg'
---

# How memory is allocated in JavaScript?

- JavaScript engine also uses references to access memory.
- Memory is allocated in two places: `stack` and `heap`.
- `Stack` is used for primitive data types like `number`, `string`, `boolean`, `undefined`, and `null` and also function call stack.
- `Heap` is used for complex data types like `object`, `array` and `function`.

---
layout: center
---

# Stack

- Stack is used for primitive type as well as function calls (function execution). It's called `call stack`.

> demo: https://www.jsv9000.app/

<br>

# Heap

- Heap is used for complex data types like `object`, `array`, and `function`.
- Heap is an unstructured memory space.
- JS engine allocates memory for complex data types in heap and keeps a reference to them in stack.
<!-- When you push a new item to array/object, it tries to use the same space or move everything -->

---
layout: center
---

# Heap / Functions

- Functions are also objects, so they are allocated in heap.
<!-- function code is part of the object -->
- When you define a function like `function foo() {}`, it is allocated in heap and a reference to it is kept in stack.
<!-- Variable declarations within the function, execution context when the function is invoked.
This also includes closure scopes -->

- If you define a function inside another function, it is allocated in heap. When the outer function returns, the inner function will get garbage collected eventually.
<!-- So, defining a function inside and outside of render will have different effects on memory usage.
- If you define a function inside a loop (render), it will be allocated in heap for each iteration. It may cause performance issues because each old render will be garbage collected and will bring calculation cost for garbage collector. -->

---
layout: center
---

# Achieving memory efficiency (1/3)

<!-- let's go over some hints about how to achieve that -->

### 1. Use `WeakMap` and `WeakSet`

- `WeakMap` and `WeakSet` do not prevent their key objects to be garbage collected.

<br>

### 2. Use proper data type for the job:

<!-- it is clear that one type may be more efficient than another for specific use-case but -->

- Use `Set` instead of `Array` if you don't need to keep the order of items.
<!-- since Set has O(1) efficiency and prevent item duplication -->
- Use `Map` instead of `Object` if you need to keep the order of items.
  <!-- better for execution efficiency and memory efficiency -->
  <!-- may be less readable since objects are highly populer build-in types -->

---
layout: center
---

# Achieving memory efficiency (2/3)

### 3. Minimize the use of `closure`

- Closures retain the scope of the outer function and leading to memory inefficiency.

```jsx
// better
function filterOutside(employees) {
  return employees.filter(someConditionFn)
}

const Component = () => {
  const [employees, setEmployees] = useState(someData)

  // avoid
  const filterInside = () => {
    return employees.filter(someConditionFn)
  }
  
  return(
    <div>{filterInside().map(...)}</div> // avoid
    <div>{filterOutside(employees).map(...)}</div> // better
  )
}
```

---
layout: center
---

# Achieving memory efficiency (3/3)

### 4. Limit global variables

- Global variables are not garbage collected and remain in the memory all the time.
- Defining local variables instead of global variables will help garbage collector to free memory.

<br>

### 5. Reuse complex variables

- Reusing complex variables like `Objects` instead of defining new ones will help garbage collector to free memory.
<!-- I will go into this one in next section -->

<br>

### 6. Prevent memory leaks

- Ensure that you don't have memory leaks in your code like removing event listeners when they are not needed anymore.

---

# Immutable data and structural sharing

- React requires immutable data to be able to compare previous and current states.
- From memory management perspective, it is more efficient to reuse whatever possible instead of creating a new one or copying it.
<!-- we commonly use immer.js to achieve that -->
- Immer.js uses `structural sharing` to achieve immutability.

```jsx
// without immer.js
const nextState = baseState.slice() // shallow clone the array
nextState[1] = {
    // replace element 1...
    ...nextState[1], // with a shallow clone of element 1
    done: true // ...combined with the desired update
}
// since nextState was freshly cloned, using push is safe here, but doing the same thing 
// at any arbitrary time in the future would violate the immutability principles and introduce a bug!
nextState.push({title: "Tweet about it"})

// with immer.js
const nextState = produce(baseState, draft => {
    draft[1].done = true
    draft.push({title: "Tweet about it"})
})
```

<!-- there is also https://immutable-js.com/ for the same purpose -->

---
layout: image-right
image: './images/question.jpeg'
---

# Question to you:

## How parameters are passed to functions in JavaScript? By value or by reference?

---
layout: image
image: './images/wall-e.jpeg'
class: 'm-auto absolute max-w-150'
---
