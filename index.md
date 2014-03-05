---

What are generic functions?

---

# Generics work on any type that matches requirements.

---

## Most JS built-ins are mostly generic (ES6):
```js
if (!('contains' in String.prototype)) {
  String.prototype.contains = function(str, startIndex) {
    return ''.indexOf.call(this, str, startIndex) !== -1;
  };
}
```

---

## Also works for Arrays (Not yet standardized):
```js
if (!('contains' in Array.prototype)) {
  Array.prototype.contains = function(str, startIndex) {
    return ''.indexOf.call(this, str, startIndex) !== -1;
  };
}
```

---

## .map() for Strings!
```js
String.prototype.map = function () {
  var args = [].slice.call(arguments);
  return [].map.apply(this, args).join('');
};
```

---

## Reality check
* Please don't extend built-in prototypes

(except for standard polyfills)

---

## Collection manipulation
`fn(x*): x*`

X = any type
X* = list of x

e.g.: `.map()`, `.filter()`...

---

## Transforms
`fn(el [, options...] [, prev] [, index] [, list]): x`

el = current element of type x
options = optional params

---

## Filters
`fn(el [, options...] [, prev] [, index] [, list]): bool`

---

# Lifting

---

## Type specific
```js
function increment(x) {
  return +x + 1;
}
```

---

## Lifted
```js
function increment(x) {
  return x.increment();
}
```

---

## Requirements
* x must have an `.increment()` method.

---
