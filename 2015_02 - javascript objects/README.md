# javascript objects

---
---

## Plain old javascript object

```javascript
x = {a: 1, b: 2, c: 3};
x.a, x.b, x.c
y = {a: [0, 1, 2, 3, 4, 5]};
```

---
---

## Cloning / shallow copy

1. create another object ( {}  ===  new Object() )
2. copy all properties one by one

```javascript
x = {a: 1, b: 2, c: 3};
y = {};
for (i in x) y[i] = x[i];
```

(extend, clone, assign etc.)

---
---

## deep copy

```javascript
x = {a: [0, 1, 2, 3]};
y = {a: [4, 5]};

z = $.extend(true, x, y);
z = [4, 5, 2, 3];
```

On a deep extend, Object and Array are extended, but object wrappers on primitive types such as String, Boolean, and Number are not.

"you probably don't need deep copies"

---
---

## this

```javascript
x = {a: 1, echo: function () { alert(this.a); } };
y = {a: 9};
x.echo.apply(y); // echoes "9"
```

---
---

## this "without" a scope is the window object

```javascript
(function () {
  alert(this.top);  // = window.top!
}());

X = function () {
  this.a = 1;
};

X(); // we have created a global variable :(
```

---
---

## prototypes

```javascript
x = new X();
alert(x.a);
```

when used with the "**new**" keyword, this has a different scope :)

Prototypes are NOT CLASSES!

---
---

## "new" keyword in javascript has nothing to do with new in most other languages

* unfamiliar (prototypic chain)
* no visibility
* no isolation
* no interfaces
* no attributes

But: it's efficient (it's language feature)

---
---

## copy via prototype

```javascript
x = {a: 1, b: 2, c: 3};

y = function () {};
y.prototype = x;
y = new y();

// Crockford's simple shim
// (without caring for the constructor or multiple objects):
Object.create = function (o) {
  function F() {};
  F.prototype = o;
  return new F();
};
```

---
---

## prototypes

```javascript
X = function (a, b) {
  this.a = a;
  this.b = b || this.b;
};
X.prototype.a = 1;
X.prototype.b = 2;
x = new X(3);
// ==== [VS] ====
X = {
  a: 1,
  b: 2,
  init: function (a, b) {
    this. a = a;
    this.b = b || this.b;
  }
};
(x = Object.create(X)).init(3);
x = $.extend({}, X)
x.init(3);
```

---
---

## function with prototypes -> new -> object

```javascript
typeof X === function
typeof x === object
x.hasOwnProperty("a") === false
x.c = 4;
x.hasOwnProperty("c") === true
```

---
---

## prototype is just a shadow

x.a - a is on the prototype chain
(x has own property "a"? no)

`x.a = 1;`

a is now a "static" variable, "above" the prototyped one
(x has own property "a"? yes!)

---
---

## ...and a cookie cutter

```javascript
String.prototype.rtrim = function () {};
"hello    ".rtrim();
```

* retroactively modify (existing) objects
* Object.prototype ist verboten (versus: ex nihilo creation, __proto__ = null)
* shims (yes it's not good, but let us fix it)

```javascript
Object.prototype.today = "friday";
x = {};
for (i in x) console.log(i);
```

---
---

## class-like inheritance (simplified)

```javascript
Dog = function () {};

Dog.prototype = new Mammal();

Dog.prototype = {legs: 4, tail: true}; // etc.

Dog.prototype.parent = Mammal.prototype;

dog = new Dog();
```

---
---

## can be hard to understand

### why??

JavaScript was originally developed in Netscape, by Brendan Eich. Battling with Microsoft over the Internet, Netscape considered their client-server solution as a distributed OS, running a portable version of Sun Microsystems' Java.

Because Java was a competitor of C++ and aimed at professional programmers, Netscape also wanted a lightweight interpreted language that would complement Java by appealing to non professional programmers, like Microsoft's Visual Basic.

---
---

## plain objects are flexible enough

mixins, spices, curries

```javascript
X = {a: function () {...}}
Y = {b: function () {...}}

Z = {
  a: X.a,
  b: function () { this.doSomething(); Y.b.call(this) }
}

z = $.extend({}, Z); // please do not forget to clone
```

---
---

## our current usage

site code:
* mixin/inheritance: shallow $.extend
* instanciation: Object.create()

AC2 code (wrappers):
* mixin/inheritance:
* obj.mixRawVM (viewmodels), $.extend (pojo)
* instanciation: obj.toVM, obj.toInsta (or smg similar, new will be removed)

---
---

## classes future (es6 harmony)

```javascript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);
  }

  public update(camera) {
    super.update();
  }
}
```

---
---

## objects future (and present)

getters and setters (short syntax):

```javascript
var square = {
    length: 10,
    get area() { return this.length * this.length },
    set area(val) { this.length = Math.sqrt(val) }
}
```

(square.length is still writable though)

---
---

## advanced properties

```javascript
// get prop metadata:
Object.getOwnPropertyDescriptor

// set prop metadata:
Object.defineProperty(square, "length", {
    value: 10,
    writable: false,
    configurable: true,
    enumerable: false,
    get: function () { ... },
    set: function () { ... },
});
```

---
---

## prevent adding new properties (preventExtensions, partial seal)

```javascript
x = {a: 1};
Object.preventExtensions(x);
x.a = 9; // ok
x.b = 1; // x.b = undefined
Object.defineProperty(x, "c", {value: 1}); // throws error
delete x.a; // ok ↔ Object.seal(x)
```

seal: no delete, no config, allow write!!!

---
---

## freeze: no add, no remove, no prop-change (including value)

```javascript
x = {a: [0, 1, 2], b: 1}
Object.freeze(x);
delete x.b; // false, b is not deleted
x.c = 9; // x.c is undefined (no error)
x.b = 9; // x.b remains 1 (no error)
x.a = 0; // x.a remains an array (no error)
x.a[0] = 2; // the array's first item changes
```

---
---

## it's not visibility and it's only partial encapsulation?

```javascript
(function() {
    // to be filled in later
    var name;
    mixin(object, {
        get name() {
            return name;
        }
    });
    // let's just say this is later
    name = "Nicholas";
}());
```

"the example is potentially disaster prone, not in that specific form, but in the way a getter with private scope access could be."
