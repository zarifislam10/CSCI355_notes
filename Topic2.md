
# CS355 – Topic 2 Notes


  

## Stack vs Heap

  

### Example 1 – Primitives

| Stack: Identifier | Stack: Address | Stack: Value | Heap: Address | Heap: Value |

|-------------------|--------------------|---------------|------------------|-------------|

| n1			    | 0x01 			      | 31			  	   |                         |                   |

| n2                 | 0x02              | 31                	  |                         | 					|

  

- Fixed size

- Modern computers have **64-bit** architecture

-  **8-bit** in a pointer

- For **primitives**, JavaScript is passed by **value**

  

```js

let  n1;

n1 = 31;

let  n2;

n2 = n1;

n1 = 32;

  

console.log(n2); // 31

```

  

---

  

### Example 2 – Objects on Heap

| Stack: Identifier | Stack: Address | Stack: Value | Heap: Address | Heap: Value |

|-------------------|-------------------|----------------|--------------------|---------------------|

| f1 						| 0x01 				| 0xE1 			| 0xE1 					| { num: 3, den: 4 } |

| f2 						| 0x02					 | 0xE1 			| 								| 								|

  

```js

let  f1;

f1 = { num:  3, den:  4 };

  

let  f2;

f2 = f1;

  

f1 = { num:  1, den:  2 };

  

console.log(f2); // { num: 3, den: 4 }

```

  

---

  

### Example 3 – Modifying Shared Reference

| Stack: Identifier | Stack: Address | Stack: Value | Heap: Address | Heap: Value |

|--------------------|------------------|---------------|-------------------|----------------|

| f1 						  | 0x01 			     | 0xE1		    | 0xE1 		         | { num: 1, den: 2 } |

| f2 						  | 0x02                | 0xE1            |                             |                              |

  

```js

let  f1;

f1 = { num:  3, den:  4 };

  

let  f2;

f2 = f1;

  

f1.num = 1;

f1.den = 2;

  

console.log(f2); // { num: 1, den: 2 }

```

  

---

  

### Example 4 – Adding Methods

| Stack: Identifier | Stack: Address | Stack: Value | Heap: Address | Heap: Value 			   |

|--------------------|------------------|---------------|-------------------|--------------------------|

| f1 						| 0x01					 | 0xE1 			| 0xE1					 | { num: 5, den: 4, toDecimal: fn() }|

| f2 						| 0x02 				| 0xE1 			| 							|									 |

  

```js

let  f1;

f1 = { num:  3, den:  4 };

  

let  f2;

f2 = f1;

  

f1.toDecimal = function() {

return  this.num / this.den;

};

  

console.log(f2.toDecimal()); // 0.75

  

f1.num = 5;

console.log(f2.toDecimal()); // 1.25

```

  

---

  

### Shallow Copy

| Stack: Identifier | Stack: Address | Stack: Value | Heap: Address | Heap: Value       |

|-------------------|-------------------|----------------|------------------|--------------------|

| f1						 | 0x01                  | 0xE1             | 0xE1                | { num: 1, den: 2 } |

| f2                       | 0x02                  | 0xE2             | 0xE2                | { num: 3, den: 4 } |

  

```js

let  f1 = { num:  3, den:  4 };

let  f2 = { ...f1 }; // shallow copy

  

f1.num = 1;

f1.den = 2;

  

console.log(f2); // { num: 3, den: 4 }

```

  

If object has nested objects, spread only copies the **first level**.

  

```js

let  f1 = { num:  3, den:  4, inverse: { num:  4, den:  3 } };

let  f2 = { ...f1 };

  

f1.num = 1;

f1.den = 2;

f1.inverse.num = 2;

f1.inverse.den = 1;

  

console.log(f2);

// { num: 3, den: 4, inverse: { num: 2, den: 1 } }

```

  

- In frontend, you might need to work with **legacy code**.

  

---

  

## Scopes

  

### 3 Major Types

1.  **Global Scope**

2.  **Function Scope**

3.  **Block/Lexical Scope (`let` / `const`)**

  

---

  

### Global Scope

```js

function  myScope() {

x = 5; // x becomes global

}

myScope();

  

console.log(x); // 5

```

  

-  `Math` is itself a library

```js

Math.random();

```

  

---

  

### Function Scope

- Variables declared inside a function only exist **inside** that function.

  

```js

function  myScope(a) {

var  x;

if (a < 10) {

x = 10;

}

console.log(x);

}

myScope(11); // undefined

```

  

```js

function  myScope(x) {

if (true) {

var  y = 5;

}

console.log(y); // 5 (var is function-scoped)

}

myScope();

```

  

---

  

### Block / Lexical Scope (`let` / `const`)

- Accessible only in the block where they are defined `{ }`.

  

```js

function  myScope(a) {

let  x = 5;

if (true) {

let  x = 10;

console.log(x); // 10

}

console.log(x); // 5

}

myScope();

```

  

```js

{

let  x = 5;

let  y = 7;

}

console.log(x); // ReferenceError

```

  

---

  

## Higher Order Functions

  

- Functions that either:

1. Take a function as input

2. Return a function as output

3. Or both

  

```js

let  a = function(c) {

return  1;

};

  

function  run(b) {

return  b;

}

  

run(a);

```

  

---

  

### 1) Array `.map()`

**Input:** some array + function

**Output:** transformed array

  

```js

function  sq(x) {

return  x * x;

}

  

[1, 2, 3, 4, 5, 6].map(sq);

// [1, 4, 9, 16, 25, 36]

  

[1, 2, 3, 4, 5, 6].map(() =>  0);

// [0, 0, 0, 0, 0, 0]

```

  

---

  

### 2) Create Adder

```js

function  createAdder(x) {

return  function(y) {

return  x + y;

};

}

  

const  add5 = createAdder(5);

  

add5(10); // 15

add5(20); // 25

```

  

---

  

### 3) Negating Functions

```js

function  not(func) {

return  function(x) {

return !func(x);

};

}

  

let  is_even = (n) =>  n % 2 === 0;

const  is_odd = not(is_even);

  

is_even(6); // true

is_odd(6); // false

```

  

---

  

### 4) Closures

```js

function  outer(a) {

let  b = 20;

let  unused = 50;

  

return  function  inner(c) {

let  d = 40;

return [a, b, c, d];

};

}

  

let  i = outer(10);

let  j = i(30);

  

console.log(j); // [10, 20, 30, 40]

```

  

**Closure definition:**

- A closure is when a function “remembers” variables from its scope, even after that scope has finished executing.

- The function maintains a **pointer to the memory address** of those variables.

-  **Interviewers like to ask this.**
---
