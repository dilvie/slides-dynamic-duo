---

# Static Types Are Overrated: The Dynamic Duo – Loose Types And Object Extension

---

## What are static types?

---

## Benefits of static types

---

> "A  helpful analogy to understand the value of static typing is to look at it as putting pieces into a jigsaw puzzle. In Haskell, if a piece has  the wrong shape, it simply won't fit. In a dynamically typed language,  all the pieces are 1×1 squares and always fit, so you have to constantly  examine the resulting picture and check (through testing) whether it's  correct." — Bryan O'Sullivan, et al. "Real World Haskell"

---

## Catches type errors

---

# (Overrated)

---

# Myth:

It's impossible to write large programs in JavaScript because there is no way to know if your program is even passing around correct types.

---

# Reality:

37Signals, Adobe, Dow Jones, eBay, Flickr, Paypal, LinkedIn, Groupon, Microsoft, Trello, Uber, Voxer, Yahoo!, Yammer, Walmart all have substantial projects written in JavaScript (Node).

---

## Are they all crazy?

---

That warm cozy feeling you have about your compile time checking is a false sense of security.

---

### Type correctness does not guarantee program correctness.

---

## Type correctness does not guarantee program correctness.

---

# Type correctness does not guarantee program correctness.

---

> "The first step is fully admitting that the code you write is riddled with errors." - John-Carmack "Static Code Analysis" blog post
(must read)

---

## You should be doing automated testing for program correctness in _every language_.

---

## JavaScript's tools are _underrated_.

---

## JSHint catches:
* Variable name typos
* Undefined variables
* Unused variables
* Cyclomatic complexity

---

## Tern.js
* Autocompletion on variables and properties
* Function argument hints
* Querying the type of an expression (type inference)
* Finding the definition of something
* Automatic refactoring

---

## Outstanding runtime debugging

---

## Trace.gl
* Records every function call
* Traces every value as it flows through the program
* Allows you to navigate asynchronous function calls by clicking on the functions
* Shows delay between lambda creation and call time

---

## Theseus for Adobe Brackets
* Inline code analysis
* Integrates with live running Chrome and Node
* Visual code coverage
* Function call counts
* Function return values

---

## Chrome developer tools heap profiler

* Comparison view - before / after snapshots to find memory leaks
* Dominators view - make sure objects are being garbage collected

---

## Static analysis can never replace the need to exercise code.

---

## Testing

---

## Keep modules small and independent

---

## Test the surface API of every module (unit tests)

---

## Holy grail: Aim for 100% code coverage
* Write tests first
* Never write code that does not satisfy a test assertion

---

## Test the whole system as a single unit (integration tests)

---

## A well maintained JavaScript codebase is:
* Composed from simple modules
* Linted
* Unit tested
* Peer reviewed
* Integration tested

---

## By the time you're done with all that, most type-related errors are caught.

---

> "But... but... WHY do all that if you can just let the compiler do static analysis!?" - Dude looking at his cell phone a few minutes back

---

# Type correctness does not guarantee program correctness.

---

# True benefits of static analysis

---

## Reduce cognitive load

```js
function foo(options) {
  //...
}

var myThing = foo({ bar: ? type ?});
```

---

## JSDoc static analysis
* Google Closure Compiler
* WebStorm
* DocBlockr SublimeText
* Tern can parse JSDoc for type hints

---

## JSDoc type annotations

[link](https://developers.google.com/closure/compiler/docs/js-for-compiler?csw=1#types)

---

> "A  helpful analogy to understand the value of static typing is to look at it as putting pieces into a jigsaw puzzle. In Haskell, if a piece has  the wrong shape, it simply won't fit. In a dynamically typed language,  all the pieces are 1×1 squares and always fit, so you have to constantly  examine the resulting picture and check (through testing) whether it's  correct." — Bryan O'Sullivan, et al. "Real World Haskell"

---

## Implies a good mapping of the memory graph.

---

## MAJOR perf implications

---

## Easy analysis of required memory allocation

---

## Tame the garbage collector

---

## You do have some control in JavaScript

---

## Code like a pirate!

---

> "Kill yer crew before ya sail!" - Colt McAnlis ["The Joys of Static Memory JavaScript"](https://developers.google.com/live/shows/905868411)
#perfmatters

---

## Disadvantages of static types

---

> "Any sufficiently complicated C or Fortran program contains an ad hoc, informally-specified, bug-ridden, slow implementation of half of Common Lisp." - Greenspun's tenth rule

---

# When languages lack dynamic types, people use ugly hacks to fake it.

---

## The void * hack

---

```c
int increment(int x) {
  int result;

  result = x + 1;
  return result;
}
```

---

# Only works for ints. What about long, float, etc...?

---

```c
enum type { 
    TYPE_INT,
    TYPE_LONG,
    TYPE_FLOAT
};

void increment(enum type t, void *x) {
  switch (t) {
    case TYPE_CHAR:
      (*(char *)x)++;
    break;

    case TYPE_INT:
      (*(int *)x)++;
    break;

    case TYPE_FLOAT:
      (*(float *)x)++;
    break;
  }
}
```

---

```c
incr(TYPE_INT, &i);
incr(TYPE_LONG, &l);
incr(TYPE_FLOAT, &f);
```

---

## In JavaScript
```
int increment(x) {
  var result;

  // now with string concatenation!
  // wait... wtf?
  result = x + 1;
  return result;
}

(ok... bad example. But this is going somewhere, really!)

```

---

## I know these could be shorter. Focus, people!

---

# Generic functions

---

## Generics work on any type that matches requirements.

---

## Lots of JS built-ins are (mostly) generic.

An approximation of ES6 String.prototype.contains()...
```js
if (!('contains' in String.prototype)) {
  String.prototype.contains = function(str, startIndex) {
    return ''.indexOf.call(this, str, startIndex) !== -1;
  };
}
```

---

## Also works for Arrays (Warning: Not standardized!):
```js
if (!('contains' in Array.prototype)) {
  Array.prototype.contains = function(str, startIndex) {
    return ''.indexOf.call(this, str, startIndex) !== -1;
  };
}
```

---

## .map() for Strings! (Don't try this in prod.)
```js
String.prototype.map = function () {
  var args = [].slice.call(arguments);
  return [].map.apply(this, args).join('');
};
```

---

## Reality check
Please don't extend built-in prototypes

(except for standard polyfills)

---

## Collection manipulation
`fn(x*): x*`

* x = any type
* x* = list of x

e.g.: `.map()`, `.filter()`...

---

## Transforms
`fn(el [, options...] [, prev] [, index] [, list]): x`

* el = current element of type x
* options = optional params

---

## Filters
`fn(el [, options...] [, prev] [, index] [, list]): bool`

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

---
