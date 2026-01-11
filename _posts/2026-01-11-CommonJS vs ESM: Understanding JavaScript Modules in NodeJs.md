---
title: "[Basics]CommonJS vs ESM: Understanding JavaScript Modules in NodeJs"
categories: js
tags: [Basic, JavaScript]
layout: single
share: false
---

If you’ve ever tried to use `import` / `export` in a Node.js project and were greeted with confusing errors, you’re not alone.  
This confusion usually comes from not understanding **JavaScript module systems**—specifically **CommonJS** and **ES Modules (ESM)**.

This article explains **why these two systems exist**, **how they differ**, and **what practical impact they have on your code today**.

## Why Do We Need Modules in JavaScript?

In the early days, JavaScript had **no module system**.

```
<script src="a.js"></script>
<script src="b.js"></script>
````

* All variables lived in the global scope
* Name collisions were common
* Large applications were hard to maintain

When **Node.js** appeared, JavaScript suddenly needed a way to:

* Split code into files
* Share functionality safely
* Build large applications

That’s where **CommonJS** came in.

---

## What Is CommonJS?

**CommonJS** is a module system created specifically for **Node.js** (around 2009).

### Basic Syntax

```
// math.js
function add(a, b) {
  return a + b;
}

module.exports = { add };
```

```
// app.js
const { add } = require("./math");
console.log(add(1, 2));
```

### Key Characteristics

| Feature         | CommonJS                    |
| --------------- | --------------------------- |
| Module loading  | Runtime (dynamic)           |
| Syntax          | `require`, `module.exports` |
| Origin          | Node.js                     |
| Browser support | ❌ (needs bundling)          |

### How It Works

* Modules are loaded **when the code runs**
* `require()` is just a function call
* This makes static analysis and optimization difficult

---

## The Limitations of CommonJS

While CommonJS worked well for Node.js, it had problems:

* Not a JavaScript language standard
* Poor support for static analysis
* No native browser support
* Hard to optimize (e.g., tree-shaking)

So the JavaScript community introduced a **standard module system**.

---

## What Is ESM (ECMAScript Modules)?

**ES Modules (ESM)** were officially introduced in **ES2015 (ES6)**.

They are now the **official JavaScript module standard**, supported by:

* Modern browsers
* Modern Node.js
* Most frontend tooling

### Basic Syntax

```
// math.js
export function add(a, b) {
  return a + b;
}
```

```
// app.js
import { add } from "./math.js";
```

### Key Characteristics

| Feature         | ESM                       |
| --------------- | ------------------------- |
| Module loading  | Static (before execution) |
| Syntax          | `import`, `export`        |
| Standard        | ✅ JavaScript standard     |
| Tree-shaking    | ✅ Supported               |
| Browser support | ✅ Native                  |

---

## CommonJS vs ESM: Side-by-Side

| Category          | CommonJS         | ESM              |
| ----------------- | ---------------- | ---------------- |
| Standard          | ❌                | ✅                |
| Load timing       | Runtime          | Before execution |
| Syntax            | `require()`      | `import`         |
| Exports           | `module.exports` | `export`         |
| Tree-shaking      | ❌                | ✅                |
| Top-level `await` | ❌                | ✅                |

---

## Why Is Node.js Still Confusing About Modules?

Node.js was **originally built around CommonJS**.

ESM support was added **much later**, which means Node now supports **both systems**, using rules to decide which one to apply.

### How Node Decides the Module Type

| Condition                            | Module Type |
| ------------------------------------ | ----------- |
| `"type": "module"` in `package.json` | ESM         |
| `.mjs` file extension                | ESM         |
| `.cjs` file extension                | CommonJS    |
| No config (default)                  | CommonJS    |

This dual support is the root cause of many confusing errors.

---

## What About TypeScript?

TypeScript adds another layer by **compiling module syntax**.

One particularly confusing option is:

### `verbatimModuleSyntax`

```
"verbatimModuleSyntax": true
```

This tells TypeScript:

> “Do NOT transform import/export syntax. Output it exactly as written.”

#### Why This Matters

* If your file is treated as **CommonJS**
* And you write `export const x = ...`
* TypeScript will throw an error

Because CommonJS **does not support `export` syntax**.

For most applications, this option should be:

```
"verbatimModuleSyntax": false
```

Which allows TypeScript to safely convert ESM syntax into CommonJS when needed.

---

## Practical Recommendation (2025)

For **new projects**, the best choice is:

* Use **ES Modules everywhere**
* Configure Node and TypeScript consistently

### Recommended Setup

```
// package.json
{
  "type": "module"
}
```

```
// tsconfig.json
{
  "compilerOptions": {
    "module": "ESNext",
    "verbatimModuleSyntax": false
  }
}
```

This avoids most module-related errors and aligns with modern tooling.

---

## Final Thoughts

The confusion between CommonJS and ESM isn’t your fault—it’s a result of JavaScript’s evolution and Node.js backward compatibility.

Once you understand:

* **Why CommonJS exists**
* **Why ESM was introduced**
* **How Node decides which one to use**

Most module-related errors suddenly make sense.
