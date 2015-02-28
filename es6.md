# ES6 Coding Style

## Casing

- Regular identifiers MUST be formatted `likeThis`.

- Only functions with constructor semantics, classes, or namespaces MUST start with an Uppercase letter.

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

- Object literals keys should be camelCase (this doesn't apply to programmatically generated keys).

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

- An object declaration should have spaces after each `{` and before each `}`.

- If the declaration is multiline, each line MUST end with a comma, including the last one.

- If the declaration is not multiline, the last value MUST not end with a comma.

- In an object declaration, there MUST be NO space before each colon but at least one after each colon.

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

- Use the literal Array syntax for creation.

```js
// bad
const a = new Array();

// good
const a = [];
```

## Strings

- Use single quotes for static strings. Backticks are also acceptable.

- Use backticks for dynamic strings. Never use single quotes for dynamic strings.

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

- Never use `var`.

- Use `const` dy default. If you really need to use `let`, its acceptable, but its most often a sign that you should refactor.

- Use one `const` declaration per variable.

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

- Use named function declarations instead of anonymous functions by default.

- If you need to use an anonymous function, use the arrow function syntax by default.

- If you really need to use a non-arrow, anonymous function, always give it a name.

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

- Always use parens for arrow functions parameters, even when there is only one parameter.

```js
// bad
const triple = x => 3*x;

// good
const triple = (x) => 3*x;
```

- Use arrow functions instead of `Function.prototype.bind` when you can. Never use `self` / `_this` / `that` trickery.

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

- Never name a parameter `arguments`.

- Always typecheck parameters explicitly in a `__DEV__` block. Optionally document them.

- Use ES6 default parameter value when applicable.

- Prefer destructuring parameter syntax when applicable.

- Prefer explicit variadic expansion over ES6 rest syntax. If you really need true dynamic variadic functions, use ES6 rest syntax, not `arguments` inspection.

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

- When not sure if more parameters could be added laters, use object parameters.

- Never use boolean parameters. Use named options object.

```
// bad
function divide(a, b, shouldCheckForZero = false ) {

}

// good
function divide(a, b, { shouldCheckForZero = false } = {}) {

}
```

## Properties

- Use dot/subscript notation consistently; prefer dot notation by default.

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

- Never use `+=`-like and `++`-like operators. Also, consider refactoring to get rid of the underlying `let`.

```js
let n = 0;
// bad
n += 1;
n++;

// good
n = n + 1;
```

- NEVER use `==` or `!=`. ALWAYS use `===` or `!==`.

- Never use `!!expr` to force casting to a boolean. Use the `expr` directly.

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

- Use `/* ... */` for non-jsdoc multiline comments. Use `/** ... */` for jsdoc comments.

- Be very careful when using trailing `//`. For single-line comments, comment on the previous line of whatever you're commenting.

## Whitespace

- NEVER, EVER, use tabs characters. Tabs are evil. Don't use them.

- Use 2-characters soft-spacing.

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

- Use 1 space before the leading brace `{`.

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

- Use 1 space before the ending brace `}` in single-line object literals or function declaration.

```js
// bad
function answer() { return 42;}
const a = { a: 42};

// good
function answer() { return 42; }
const a = { a: 42 };
```

- Don't use space before the leading bracket `[` or the ending bracket `]` in single-line array literals.

```js
// bad
const a = [ 1, 2 ];

// good
const a = [1, 2];
```

- Don't use space before the leading paren `(`.

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

- Use spaces between infix operators.

```js
// bad
const a = b+c;
const a= b + c;

// good
const a = b + c;
```

- End files with a single newline character.

- Use leading dot and indentation for method chaining.

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

- Always use braces for blocks, even single-statement blocks. Only exception is 1-expression arrow functions.

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

- Always use trailing commas for multiline literals, never use them for single-line literals.

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

- Always use trailing semicolons, even when they are not strictly required.

```js
// bad
function id(x) { return x }

// good
function id(x) { return x; }

// actually better
const id = (x) => x;
```

## Method short notation

- Always use ES6 method short notation when applicable.

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

- Always use ES6 modules, not `require`. Use destructuring assignment when applicable.

```js
// bad
const moduleA = require('moduleA');
const func1 = moduleA.func1;
const func2 = moduleA.func2;

// good
import { func1, func2 } from 'moduleA';
```


## undefined

- Never use `undefined`. Always use `void 0`.

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

- Avoid object reshaping by declaring their shape early with `null` values.

- If you need to reshape an object, always use `Object.assign`. In particular, use `Object.assign` to define prototype properties.

- In the particular case of a constructor function, an object should never be reshaped after the end of the constructor.

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