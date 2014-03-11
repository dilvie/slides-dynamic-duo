> "Empty your mind, be formless. Shapeless, like water. If you put water into a cup, it becomes the cup. You put water into a bottle and it becomes the bottle. You put it in a teapot, it becomes the teapot. Now, water can flow or it can crash. Be water, my friend." - Bruce Lee

---

# Static Types Are Overrated: The Dynamic Duo – Loose Types And Object Extension

_Eric Elliott_

---

![Adobe Logo](adobe.jpeg)

![Tout](tout.jpg) ![BandPage](bandpage.jpg) ![Zumba Fitness](zumba.jpg)

---

!["Programming JavaScript Applications"](pja.jpg)

---

## What are static types?

---

## What are dynamic types?

---

## Benefits of static types

---

> "A  helpful analogy to understand the value of static typing is to look at it as putting pieces into a jigsaw puzzle. In Haskell, if a piece has  the wrong shape, it simply won't fit. In a dynamically typed language,  all the pieces are 1×1 squares and always fit, so you have to constantly  examine the resulting picture and check (through testing) whether it's  correct." — Bryan O'Sullivan, et al. "Real World Haskell"

---

## Catch type errors

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
* Tern can parse JSDoc for type hints
* Google Closure Compiler warnings
* WebStorm inline hints
* DocBlockr SublimeText

---

## JSDoc type annotations

[link](https://developers.google.com/closure/compiler/docs/js-for-compiler?csw=1#types)

---

## Jigsaw puzzles - every piece fits in its designated place, and _doesn't change size_.

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

> "Kill yer crew before ya sail!" - Colt McAnlis ["The Joys of Static Memory JavaScript"](http://www.youtube.com/watch?v=RWmzxyMf2cE) #perfmatters @duhroach

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
```
wait for it...

---

```c
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

Tada! Simple!

---

```c
incr(TYPE_INT, &i);
incr(TYPE_LONG, &l);
incr(TYPE_FLOAT, &f);
```

---

## Better implementation: Pass in a function pointer to operate on individual types.
(more on this soon...)

---

## In JavaScript
```
function increment(x) {
  var result;

  result = x + 1;
  return result;
}

```
_But there's only one number type in JS, so this isn't fair. But wait! ..._

---

## Woo hoo! Parametric polymorphism for free!
```
function increment(x) {
  var result;

  // Now with string concatenation!
  result = x + 1;
  return result;
}

increment('1'); // 11?
// WTF, JS! You're killin' me here!
```

> That awkward moment when your example proves the counterpoint...

---

## I know these examples could be better. Focus, people!

---

# Generic functions

---

## Generics work on any type that matches requirements.

---

## Lots of JS builtins are (mostly) generic.

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
  return [].map.apply(this, arguments).join('');
};

'123'.map(function (n) {return +n + 1; });
// '234'
```

---

## Reality check
Please don't extend built-in prototypes

(except for standard polyfills)

---

## Collection manipulation
`fn([x], fn(x):*): [x]`

* `x` = any type
* `[x]` = list of `x`

e.g.: `.map()`, `.filter()`...

---

## Transforms
`fn(el [, options...] [, prev] [, index] [, list]): x`

* `el` = current element of type `x`
* `options` = optional params
* `prev` = previous element in list
* `index` = list index of current el
* `list` = the list reference

---

## Filters
`fn(el [, options...] [, prev] [, index] [, list]): bool`

Truthy = on the list

---

## Reduction
`fn(el [, options...] [, prev] [, index] [, list]): x`

Accumulates a single value to return.

---

# Lifting

---

## Type specific
```js
function sum() {
  var i = 0,
    result = 0;

  while (i < arguments.length) {
    result += +arguments[i];
    i++;
  }

  return result;
}

sum(2, 2, 2); // 6
```

---

## What if you need to sum item prices?
```js
var item1 = {
    name: 'Gibson Les Paul',
    price: 4299.95
  },
  
  item2 = {
    name: 'Epiphone ES 339 Pro',
    price: 395
  };

sum(item1, item2); // NaN
```

---

## Lifted
```js
function sum() {
  var i = 0,
    result = 0;

  while (i < arguments.length) {
    result += +arguments[i].valueOf();
    i++;
  }

  return result;
}
```

---

# Item factory
```js
function item(options) {
  var instance = Object.create(item.prototype);

  instance.name = options.name;
  instance.price = options.price;

  return instance;  
}

item.prototype = {
  valueOf: function () {
    return this.price;
  }
};
```

---

```js
var item1 = item({
    name: 'Gibson Les Paul',
    price: 4299.95
  }),
  
  item2 = item({
    name: 'Epiphone ES 339 Pro',
    price: 395
  });

sum(item1, item2); // 4694.95
```

---

## Of course, numbers still work:

sum(1, 2, 3); // 6

---

## Old Requirements
* Args must be coercible to numbers.

---

## New Requirements
* Args must have a `.valueOf()` method which returns a type coercible to number.

---

## Notice:
* No special syntax for generic functions
* No void *
* No type noise at all in type-agnostic functions

---

# Generics in C++ and Java melt my brain.

---

# sum() in C++
```c
template<std::CopyConstructible T>
  requires Addable<T>, Assignable<T>
  T sum(T array[], int n, T result) {
    for (int i = 0; i < n; ++i) {
      result = result + array[i];
    }

    return result;
  }
```
(Source: [generic-programming.org](http://www.generic-programming.org/languages/conceptcpp/tutorial/))

---

# Poor readability is every bit as dangerous as ambiguous types.

---

# Styles of type polymorphism

---

## Ad hoc polymorphism
* Special branching logic manually written for each type.

---

## Parametric polymorphism
* Parameters satisfy generic requirements. No type branching needed.

---

## Other cool features of dynamic types

---

## Dynamic object extension

---

## Add properties at runtime

```js
var burger = {
  pickels: 3,
  lettuce: 1,
  tomatoes: 1,
  onions: 1
};
```

---

```js
function addToppings(sandwich, toppings) {
  var topping;

  for (topping in toppings) {
    if ( toppings.hasOwnProperty(topping) ) {
      sandwich[topping] = toppings[topping];
    }
  }

  return sandwich;
}

var myBurger = addToppings(burger, {
  cheese: 1
});
```

---

## Object / prototype extensibility

---

## Mixins

var myModel = _.extend(Object.create(model), Backbone.Events);

---

## Plugins

* jQuery

---

# Recap

---

## JavaScript's code analysis tools and static performance are actually pretty good.

---

## The ability to (easily) skip type annotations makes generic programming easier.

---

## Dynamic object extension is the  heart and soul of prototypal OO.

---

> "Be like water making its way through cracks. Do not be assertive, but adjust to the object, and you shall find a way around or through it. If nothing within you stays rigid, outward things will disclose themselves." - Bruce Lee

---

## Office hours starting now
