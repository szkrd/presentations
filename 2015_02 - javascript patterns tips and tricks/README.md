# Useful javascript patterns, tips and tricks

---
---

## Declaring variables
* one var, per scope
* all in the same place
* minimize the chances of global leaks
* easier to comment

---
---

## Be careful, use lint

```javascript
(function () {
    var a = 1,
        b = 2
        c = 3;
}());
```

Problem at line 3 character 14: Missing semicolon.

---
---

## Never chain vars

```javascript
(function () {
    var a = b = c = 3;
}());

log(window.a); // undefined
log(window.b); // 3
log(window.c); // 3
```

---
---

## Function declaration

* declare functions as variables
* construction: parse time vs run time
* use named functions: `myFunc = function myFunc (...)`

---
---

```javascript
// Nothing happens
functionOne();
function functionOne() {}
```
```javascript
// Uncaught TypeError: undefined is not a function
functionTwo();
var functionTwo = function(){}
```

---
---

## Better for loops

```javascript
var arr = ["a", "b", "c", "d", "e", "f"],
    i, l;
for (i = 0, l = arr.length; i < l; i += 1) {
    log(i + " - " + arr[i]);
}
```

* cache the length property
* single var

---
---

```javascript
i = arr.length;
while (i--) {
    log(i + " - " + arr[i]);
}
```

* compact
* reverse order
* implicit typecast (i = i - 1; i === 0?)

---
---

## For in

To be used with objects! (key:value pairs)

```javascript
for (var i in myObj) {
    log(i, ": ", myObj[i]);
}
```

* whoops: object.prototype ist verboten
* `if (Object.prototype.hasOwnProperty.call(obj, i))`
* shorthand: `hasOwn = Object.prototype.hasOwnProperty`

---
---

## For each

* array.forEach = already in most browsers (like map, filter, indexOf etc.)
* `[1,2,3].forEach(function(el, i){ log(el); });`
* anon callback function
* object? string? jQuery chain?

---
---

## Implied typecasting

* always use strong comparators: === !==
* falsy values:
  * 0
  * ~~'0'
  * '0'*1
  * '  '
  * '\t\n'
  * null
  * undefined
* "\t\t" == 0 is true

---
---

# setTimeout, setInterval (avoid eval)

```javascript
/*global log*/
/*jslint browser: true*/
var timer,
    counter = 3,
    sleep = 2000;
timer = setInterval(function () {
    counter -= 1;
    log("tick");
    if (!counter) {
        clearInterval(timer);
    }
}, sleep);
```

---
---

## parseInt

```javascript
// x = "08"                  | x = "08a"
parseInt(x, 10) // --> 8     | 8
x * 1 + 10      // --> 18    | NaN
+x + 10         // --> 18    | NaN
~~x + 10        // --> 18    | 0 + 10 = 10
```

---
---

## typeof (native vs wrappers)

* null, array, arguments, new Date()... = object!
* NaN = number!
* new Boolean(false) vs. false


* primitive (11) vs object (new Number(11))
* internal type:
  `x = [0, 1, 2, 3]; Object.prototype.toString.call(x) == "[object Array]"`
* jQuery.type() = array/regexp/date/number/function etc.
