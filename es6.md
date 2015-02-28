# ES6 Coding Style

## Casing

- Regular identifiers MUST be formatted `likeThis`.

- Only functions with constructor semantics, classes, or namespaces MUST start with an Uppercase letter (PascalCase), `LikeThis`.

- Macros (to be processed at build time) MUST be formatted `__LIKE_THIS__`.

- Magic values (eg. numerical constants) MUST be formatted `LIKE_THIS`.

- Private properties and methods MUST be formatted `_likeThis`.

```js
const regularIdentifier;
function Constructor() { ... }
class Class { ... }
const Namespace = {};

if(__MACRO__) { ... }
const SOME_MAGIC_NUMBER = 1337;
const {
  _privateMethod() {
    ...
  }

  publicMethod() {
    ...
  }
}
```


## Objects

- Regular objects MUST be created using the Object syntax literal.

```js
// bad
const obj = new Object();
const obj = Object.create(Object.prototype);

// good
const obj = {};
```

- Object literals keys MUST be camelCase (this doesn't apply to programmatically generated keys).

```js
// bad
const obj = {
  'not-camel-cased': ...
};

// good
const obj = {
  camelCased: ...
};
// acceptable
const myKey = 'something-from-somewhere';
const obj = {
  [myKey]: ...
};
```

- An object declaration MUST have spaces after each `{` and before each `}`.

- If the declaration is multiline, each line MUST end with a comma, including the last one.

- If the declaration is not multiline, the last value MUST NOT end with a comma.

- In an object declaration, there MUST NOT be space before each colon but at least one after each colon.

```js
// bad
const a = {
  k1: v1,
  k2: v2
};
const b = { k1: v1, k2: v2, };
const c = {k1:v1};

// good
const a = {
  k1: v1,
  k2: v2,
};
const b = { k1: v1, k2: v2 };
const c = { k1: v2 };
```

## Arrays

- Arrays MUST be created using the Array syntax literal.

```js
// bad
const a = new Array();

// good
const a = [];
```

## Strings

- Static strings MUST be declared using single quotes or backticks.

- Dynamic strings MUST be declared using ES6 template literals (backticks). Dynamic strings MUST NOT be declared using single- or double-quotes.

- Never use double-quotes.

```js
// bad
const a = "foobar";
const b = 'foo' + a + 'bar';

// acceptable
const c = `foobar`;

// good
const a  = 'foobar';
const b = `foo${a}bar`;
const c = 'foobar';
```

## var, let, const

- `var` MUST NOT be used.

- `const` SHOULD be used dy default. If you really need to use `let`, its acceptable, but its most often a sign that you should refactor.

- `const` (or `let`) MUST be used exactly once per variable declaration.

```js
// bad
const a = 1, b = 2, c = 3;

// good
const a = 1;
const b = 2;
const c = 3;

// actually best
const [a, b, c] = [1, 2, 3];
```

## Functions

- Regular (named and hoisted) unction declarations SHOULD be used instead of anonymous functions by default.

- When using anonymous functions, arrow function SHOULD be used instead of anonymous `function`.

- When using 'anonymous' `function`, a name MUST be given.

```js
const a = [1, 2, 3];
// bad
a.reduce(function(x, y) { return x + y; }, 0);

// better, but still bad
a.reduce(function sum(x, y) { return x + y; }, 0);

// acceptable but convoluted
function sum(x, y) {
  return x + y;
}
a.reduce(sum, 0);


// good
a.reduce((x, y) => x + y, 0);
```

- Parens MUST be used for arrow functions parameters, even when there is only one parameter.

```js
// bad
const triple = x => 3*x;

// good
const triple = (x) => 3*x;
```

- Arrow functions SHOULD be used instead of `Function.prototype.bind` when applicable. `self` / `_this` / `that` trickery MUST NOT be used.

```js
function method(...params) {
  ...
}

// terribly bad
const self = this;
const boundMethod = function(...params) {
  return method.apply(self, params);
}

// acceptable
const boundMethod = method.bind(this);

// best
const boundMethod = (...params) => method.apply(this, params);
```

## Function parameters

- A parameter MUST NOT be named `arguments`.

- Parameters SHOULD BE explicitly typechecked in a `__DEV__` block. They CAN be documented in addition.

- ES6 default parameter value SHOULD be used when applicable.

- Destructuring parameter syntax SHOULD be used when applicable.

- Explicit variadic expansion SHOULD be used over ES6 rest syntax. If you really need true dynamic variadic functions, ES6 rest syntax MUST be used, not `arguments` inspection.

```js
// bad
function sum() {
  let sum = 0;
  for(let k = 0; k < arguments.length; k++) {
    sum += arguments[k];
  }
  return sum;
}

// good
function sum(a = 0, b = 0, c = 0, d = 0, e = 0, f = 0, g = 0, h = 0, i = 0) {
  return a + b + c + d + e + f + g + h + i;
}

// acceptable
function sum(...rest) {
  return rest.reduce((x, y) => x + y, 0);
}
```

- Object parameters (with default values) SHOULD be used for options parameters. Boolean parameters MUST NOT be used.

```
// bad
function divide(a, b, shouldCheckForZero = false ) {

}

// good
function divide(a, b, { shouldCheckForZero = false } = {}) {

}
```

## Properties

- Dot/subscript notation MUST be used consistently.

- Dot notation SHOULD be used when possible.

```js
// bad
t.x = 3;
t['y'] = 4;

// good
t['x'] = 3;
t['y'] = 4;

// better
t.x = 3;
t.y = 4;
```

## Operators

- Short-hand operators `+=`-like and `++`-like operators MUST NOT be used. Also, consider refactoring to get rid of the underlying `let`.

```js
let n = 0;
// bad
n += 1;
n++;

// good
n = n + 1;
```

- Non-strict comparison operators `==` or `!=` MUST NOT be used. Strict comparison operators `===` or `!==` MUST be used instead.

- Boolean force-casting `!!expr` MUST NOT be used. `expr` MUST BE used directly.

```js
// bad
if(!!expr) {
  ...
}

// good
if(expr) {
  ...
}
```

## Comments

- `/* ... */`-style MUST be used for non-jsdoc multiline comments.

- `/** ... */`-style MUST be used for jsdoc comments.

- Be very careful when using trailing `//`. For single-line comments, comment on the previous line of whatever you're commenting.

## Whitespace

- Tab characters MUST not be used. Ever. Tabs are evil. Don't use them.

- Soft-spacing 2-characters MUST be used.

```js
// bad
if(true) {
    return 42;
}

// good
if(true) {
  return 42;
}
```

- 1 space MUST be used before the leading brace `{`.

```js
// bad
if(true){
  ...
}

// good
if(true) {
  ...
}
```

- 1 space MUST be used before the ending brace `}` in single-line object literals or function declaration.

```js
// bad
function answer() { return 42;}
const a = { a: 42};

// good
function answer() { return 42; }
const a = { a: 42 };
```

- Space MUST NOT be used before the leading bracket `[` or the ending bracket `]` in single-line array literals.

```js
// bad
const a = [ 1, 2 ];

// good
const a = [1, 2];
```

- Space MUST NOT be used before the leading paren `(`.

```js
// bad
if (true) {
  ...
}

// good
if(true) {
  ...
}
```

- Spaces MUST BE used between infix operators.

```js
// bad
const a = b+c;
const a= b + c;

// good
const a = b + c;
```

- Files MUST end with a single newline character.

- Leading dot and proper indentation MUST be used for method chaining.

```js
// bad
a.then(...).
then(...).
catch(...);

a.then(...)
.then(...)
.catch(...);

// good
a.then(...)
  .then(...)
  .then(...)
.catch(...);
```

## Blocks

- Braces MUST BE used for blocks, even single-statement blocks. Only exception is 1-expression arrow functions.

```js
// bad
if(true)
 return 42;
if(true) return 42;
const id = (x) => { return x; };

// good
if(true) { return 42; }
if(true) {
  return 42;
}
const id = (x) => x;
const id = (x) => {
  return x;
}
```

## Commas

- Trailing commas MUST be used for multiline literals, and MUST NOT be used for single-line literals.

```js
// bad
const a = { a: 42, b: 43, };
const a = {
  a: 42,
  b: 42
};

// good
const a = { a: 42, b: 42 };
const a = {
  a: 42,
  b: 43,
};
```


## Semicolons

- Trailing semicolons MUST be used, even when they are not strictly required.

```js
// bad
function id(x) { return x }

// good
function id(x) { return x; }

// actually better
const id = (x) => x;
```

## Method short notation

- ES6 method short notation SHOULD be used when applicable.

```js
// bad
const a = {
  f: function() {
    ...
  }
}

// good
const a =  {
  f() {

  }
}
```

## ES6 modules

- ES6 modules MUST be used, `require`-style module MUST NOT be used. Destructuring assignment SHOULD be used when applicable.

```js
// bad
const moduleA = require('moduleA');
const func1 = moduleA.func1;
const func2 = moduleA.func2;

// good
import { func1, func2 } from 'moduleA';
```


## undefined

- `undefined` MUST NOT be used, `void 0` MUST be used instead.

```js
// bad
if(x === undefined) {
  ...
}

// good
if(x === void 0) {
  ...
}
```

## Object extension

- Object reshaping SHOULD be avoided by declaring their shape early with `null` values.

- In case reshaping is unavoidable, `Object.assign` MUST be used. In particular, `Object.assign` MUST be used to define prototype properties on ES6 classes.

- In the particular case of a constructor function, an object SHOULD not be reshaped after the end of the constructor.

```js
// bad
const a = {};
a.x = 3;

// if reshape unavoidable
const a = {};
Object.assign(a, { x: 3 });

// good
const a = { x: null };
a.x = 3;


function F() { ... }

// bad
F.prototype.staticA = 43;
F.prototype.staticB = 44;
F.prototype.protoProp = null;

// good
Object.assign(F.prototype, {
  staticA: 43,
  staticB: 44,
  protoProp: null,
});