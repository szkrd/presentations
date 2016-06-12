# Building frontend resources

----
----

## Popular builds

* **concatenation** (group and/or join js, css)
* **minify/obfuscate** (YUI compressor, Dean Edwards' packer, Closure, UglifyJs etc.)
* **linting** (we already have access to all the resources, why not do some extra? css lint / js lint / js hint etc.)
* **generate documentation** (docco, coffeedoc)
* **run unit tests** (qunit, jasmine; env.js, phantom, nodeunit etc.)
* **augment code** (replace, modify certain parts)
* **transcompilers** (compile from one language to another: sass - css, coffeescript - js etc.)
* **compiler frameworks** (gwt)

---
---

## Simple building

1. `copy /b one.js+two.js+three.js temp.js`
2. `java -jar yui_compressor.jar temp.js final.js`
3. `del temp.js`

* shellscript (bat, wsh, sh etc.)
* knockout: build-windows.bat, build-linux etc.
* testing?
  * how big is the difference?
  * does it work locally?
  * if not, when shall we build?
  * IDE support

---
---

## Heavyweight builders

* java tanks (ant/maven + rhino)
* node.js build tools (preconfigured solutions: smoosh, jake, grunt...)
* jquery 1.8 source uses grunt
  * Concatenate files
  * Project scaffolding from templates
  * Validate files with JSHint
  * Minify files with UglifyJS
  * Run QUnit unit tests
  * Start a static web server
  * Run unit tests with nodeunit
  * Run predefined tasks whenever watched files change

---
---

## Common problems

* **concatenate**: how to setup groups
* **minification**: vs. runtime code inspection (function name); how to increase minification ratio?!
* **linting**: exclude 3rd party code
* **unit testing**: headless browser infrastructure
* **code augmentation**: local proxy (local vs remote)
* **transcompilation**: vs. debugging
* **framework**: may be slow, frequent building

---
---

## Example

* **php** -> templates to raw php, translations get rendered (+varnish, +memcached)
* **css**: concatenate and minify, resolve paths (local (relative) vs cdn/server absolute)
* **javascript**: concatenate + minify (munge vs nomunge) + remove individual header/footer from js files, use common for built one
* **deploy** all resources to CDN
* package i18n **labels** (inline vs built js)
* frontend **groups** (bundles)
* **future**: javascript template wrappers

---
---

## templates/userData.js
```javascript
'<p>Hello <%= name %>!</p>' +
'<label>Address</label>' +
'<input value="<%= address %>" />';
```

```html
<p>Hello <%= name %>!</p>
<label>Address</label>
<input value="<%= address %>" />
```

---
---

## localization

```html
<script type="text/javascript" language="javascript">
	l['js.label.hello'] = 'Hello World';
	l['js.label.lorem'] = 'Lorem Ipsum';
    // + many other groups
</script>
```

```html
<script src="/l10n/labels.js?r=12345" type="text/javascript"></script>
```

---
---

local (direct reference):
```html
<script src="js/item1.js" type="text/javascript"></script>
<script src="js/item2.js" type="text/javascript"></script>
<script src="js/item3.js" type="text/javascript"></script>
```

built (sha1 or version number):
```html
<script src="/js/item123.js?r=1341156569651" type="text/javascript"></script>
```

common problems:
* css problems: IE - max 32 style tags
* solution: 31 import in one style tag

---
---

## manipulate js files for better compression ratio

```javascript
(function(myFunc){
    var apple = "pear";
    myFunc(apple);
}(other.stuff.far.away.func))
```

after "minification":
```javascript
(function(a){ var b = "pear"; a(b); }(other.stuff.far.away.func))
```

---
---

## slight modifications

```javascript
$(".myButton").on("click", $.proxy(this.onClick, this))
```

```javascript
$(".myButton").click << this.onClick
```

* valid syntax (rarely used bitshift operator)
* one liner (debug - line numbers)

---
---

## css
regexp replacers, simple vars etc.

vanilla:
```css
box-shadow: 0 1px 3px rgba(0, 0, 0, 0.6);
-moz-box-shadow: 0 1px 3px rgba(0, 0, 0, 0.6);
-webkit-box-shadow: 0 1px 3px rgba(0, 0, 0, 0.6);
```
vs.
```css
/* $shadow = rgba(0, 0, 0, 0.6) */
box-shadow: 0 1px 3px $shadow;
```

---
---

## Always minimize differences! (local vs remote)

`<script src="/pp.jsp?myfile.js&r=12345" type="text/javascript"></script>`

* pp (preprocessor) shares code with server side builder
* major plus: disables local caching (r=random/timestamp)
* expressive filename  (debugging)

---
---

## Differences = headaches

example: frontend controlled inclusion (js)
```javascript
include("classes/StringUtils.js");
include("classes/Events.js");
```
* more popular approach: bundles
* static/frozen string (misleading)
* preprocessor concatenates (debugging?!)
* similar problems: sass / less, coffeescript etc.


