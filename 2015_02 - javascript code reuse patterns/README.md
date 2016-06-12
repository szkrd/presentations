# popular js code reuse patterns

---
---

## popular approaches

* module, revealing module pattern
* power constructor pattern
* raw prototypes
* prototypes: fat constructors
* extending plain objects
* Object.create + extend
* uber/super/parent

---
---

## module / revealing module

```javascript
var horse = function () {
    var likes = "apples", // private/secret
        eat;

    eat = function () { // exposed via returning object
        log("Eats " + likes + ".");
    };

    return {
        eat: eat
    };
};

var tornado = horse();
tornado.eat();
```

---
---

## pros and cons

* strong encapsulation (private variables)
* good readability (can save "this", no need for applies, calls, proxies, binds etc.)

vs.

* no inheritance (private variables nested into the scope, "revealing" happens on "instantiation")
* high memory consumption (new objects, new functions)

---
---

## Intermezzo: javascript objects are references, not copies

we all know this, but it's so easy to forget

```javascript
AClass = Class({
    prop: {
        a: 1, b: 2, c: 3
    }
});

BClass = A.extend();

a = new AClass(),
b = new BClass();

console.log(a.prop); // Object { a=1, b=2, c=3}
a.prop.b = 99;
console.log(b.prop); // Object { a=1, b=99, c=3}
console.log(a.prop); // Object { a=1, b=99, c=3}
```

---
---

## Power constructor

* mixture of extending with objects and modules (and their visibility features)
* can be quite complicated (even without prototypes)
* maintains per object private properties
* small example (see jsfiddle for more)

---
---

```javascript
persian = function (pri) {
    var self = {}, parent;
    pri = pri || {
        message: "purr",
        action: function () {
            log("number of legs: " + self.legs);
        }
    };
    parent = cat(pri);
    return $.extend(self, parent, {
        legs: 4,
        say: function () {
            parent.say.apply(this, []);
            log("(and that was a persian cat)");
            pri.action();
        }
    });
};
```

---
---

## Raw prototypes

* javascript is prototypal, but it still has a "new" keyword (so everyone may be happy)
* javascript is object oriented, not class oriented
* javascript + classes + type hinting / generics = as3 (es4 draft implementation)
* javascript + Object create factories, sealing, freezing, cookie cutter mechanisms = future javascript, minimal shift in philosophy
* transcompilers: coffeescript, jangaroo, gwt etc.

---
---

```javascript
// constructor
Mammal = function (name) {
    this.name = name;
    this.offspring = [];
};

Mammal.prototype.haveABaby = function () {
    var baby = new Mammal("Baby " + this.name);
    this.offspring.push(baby);
    return baby;
};

Mammal.prototype.logInfo = function () {
    log ("Mammal '" + this.name + "' has "
        + this.offspring.length + " baby/babies.");
};
```

---
---

## Intermezzo: hasOwnProperty, obj.prototype.x vs obj.x

shows us if a property/method is attached to the class (prototype chain) or to the instance itself

* miffy.hasOwnProperty("name") --> true
* miffy.hasOwnProperty("logInfo") --> false

Object.prototype.verboten = 1;

* miffy.hasOwnProperty("verboten") --> false
* (miffy.verboten === 1) --> true

think about for-in loops, and the "bad parts".

---
---

## Subclassing with prototypes

```javascript
Cat = function (name) {
    this.parent.constructor.apply(this, arguments);
};

Cat.prototype = new Mammal();
Cat.prototype.constructor = Cat;
Cat.prototype.parent = Mammal.prototype;

Cat.prototype.haveABaby = function () {
    var kitten = this.parent.haveABaby.call(this);
    log("meow, meow!");
    return kitten;
};

var miffy = new Cat("Miffy");
miffy.haveABaby();
miffy.logInfo();
```

---
---

## pros and cons

* very well supported by editors, documentation tools (dates back til the creation of the language)
* well known inheritance model (1:1, no augment, no mixin)
low memory consumption

vs

* needs boilerplate (proto, proto.constructor, proto.parent)
* way too verbose, ugly (can do better)
* no private/protected, no interfaces, no abstracts etc. (language design)

---
---

## possible workarounds

* use **Class creation helpers** (Y.extend, Base2, MooTools, Resig's simple inheritance etc. - but: vs Crockford, 2010+)
* use **pretend privacy** with underscore (though not very nice, may be deceiving - vs lint)
* **deal with it**: javascript is not Class oriented (with js2 dead and js1.8 features around this will not change soon)
* use a **transcompiler** and hope it knows things better than you (rendered code may have varying quality)

---
---

## prototypes with fat constructors

```javascript
Colorizer = function (target) {
    var self = this;
    self.color = "red";
    self.target = $(target);

    self.target.on("click", function (e) {
        e.preventDefault();
        self.target.css("color", self.color);
    });
};

colorizers = [];
$("a").each(function (i, el) {
    colorizers.push(new Colorizer(el));
});
```

---
---

## pros and cons

* easy to understand, readable code
* without subclassing it looks like classes (has new operator)
* may circumvent the "this." problem, no need to understand javascript closures, scopes

vs

* everything is virtually inside the constructor, no way to create proper inheritance, reuse code!

---
---

## Extending plain objects

```javascript
X = { // parent object X
    initialize: function (name) {
        this.name = name;
        return "initialized X";
    },
    a: function () {
        log(1);
    }
};
Y = (function (parent) { // Y "inherited" from X
    return $.extend({}, parent, {
        a: function () {
            parent.a.apply(this);
            log(2);
        }
    });
}(X));
```

---
---

initialize of course won't run by itself, either call it manually or create an instantiator function:

```javascript
neue = function (obj) {
    var ret = $.extend(true, {}, obj), i;
    if (typeof ret.initialize === "function") {
        ret.initialize.apply(ret,
                    $.makeArray(arguments).slice(1));
    }
    return ret;
};
//---
z = neue(Z, "Zed");
z.a();
```

---
---

## pros and cons

* no "new" keyword, everything is an object
* closer to the Object.create pattern
* efficient (functions are references)

vs

* may be harder to document
* less structured (compared to Class emulation)

---
---

## possible workarounds

* because of multiple-inheritance-like patterns one may need "non-standard" documentation tools (docco and friends, ronn, ???)
* complexity may require unit testing support (?)

---
---

## Object.create

* ES5 / javascript 1.8.5 ("most" browsers and IE9+)
* Object.create(prototypes, properties)
* Polyfill (Crockford 2008, simple; see also: es5 shim at github.com/kriskowal/es5-shim):

```javascript
if (!Object.create) {
    Object.create = function (o) {
        function F() {}
        F.prototype = o;
        return new F();
    };
}
```

---
---

## example

```javascript
X = Object.create({
    initialize: function (name) {
        this.name = name;
        return "initialized X";
    },
    a: function () {
        log(1);
    }
});

Y = Object.create(X);
Y.a = function () {
    X.a.apply(this);
    log(2);
};

var y = Object.create(Y);
y.initialize("Yoda");
y.a();
```

---
---

## pros and cons

* Crockford likes it :)
* compared to plain old extend, a little bit cleaner
* shows a glimpse from where javascript is heading (ES5, object special properties, getters, setters, etc.)

vs

* not much better than copying object properties (same memory usage, negligible speed difference)
* combined with power constructors can be quite complex (example: jsfiddle.net/szabi/aA7Hz/)

---
---

## Uber/_super/parent implementations

```javascript
X = Class.extend({
    init: function (name) {
        this.name = name;
        return "initialized X";
    },
    a: function () {
        log(1);
    }
});

Y = X.extend({
    a: function () {
        this._super();
        log(2);
    }
});
```

---
---

## pros and cons

* very popular
* easy to understand, document

vs

* builds on an immature language feature set (no visibility, no interfaces etc.)
* masks the language's object-oriented approach
* during extend wraps all the parented methods into another function
* object properties are still hard to deal with, workarounds cause confusion (MooTools, aggressive protoyping)

---
---

## possible workarounds

* the try harder approach: add extra code to further mimic classical inheritance (dangerous complexity, library must be very well understood)
* ease debugging (runtime inspectors, debug switches, loggers etc.)
* the laissez faire approach: use a minimal class wrapping, but do nothing more

---
---

## things to do :)

* read the Good parts book
* play with jsfiddle (with the examples and with the tool itself - it's a wonderful application)
* stop worrying and learn to love javascript objects

### Thank you!!
