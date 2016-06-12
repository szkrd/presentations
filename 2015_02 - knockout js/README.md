# Knockout

---
---

## KO, MVC, SPA

* Steve Sanderson
* pattern (observers)
* recommended coding style
* template engine
* good tutorial (but no api docs)

---
---

## observability

* `var myBool = ko.observable(true);`
* `getter: myBool()` <--> `setter: myBool(false)`
* attach observer / listener: `myBool.subscribe(function (value) { console.log(value); });`

---
---

## template engine

```html
<p data-bind="text: name"></p>
<script>
    ko.applyBindings({name: "Peter Parker"});
</script>
```

---
---

## templates with observability

```html
<p data-bind="text: name"></p>
```

```javascript
var name = ko.observable("John Doe");
ko.applyBindings({name: name});
setTimeout(function(){
    name("Bruce Wayne");
}, 2000);
```

---
---

## click handler

```html
<p data-bind="text: name"></p>
<button data-bind="click: changeName">Change</button>
```

```javascript
var name = ko.observable("John Doe");
changeName = function () {
    name("Clark Kent");
};
ko.applyBindings({name: name});
</script>
```

---
---

## auto-populate

```html
<p data-bind="text: name"></p>
<input data-bind="value: name" />
```

* on blur name changes
* populates all subscribers

---
---

## coding style, inheritance: antipattern

```javascript
ko.applyBindings({
    name: ko.observable("John Doe"),
    changeName: function () {
        this.name("Flash Gordon");
    }
});
```

---
---

## inheritance problems

* prototype, Object.create, extend etc.
* scope is hard to follow
* this, self, function.bind, $.proxy etc.
* solution: fat constructors

---
---

```javascript
var Hero = function () {
    var self = this,
        heroName = "Spiderman",
        secretName = "Peter Parker";

    self.name = ko.observable(heroName);
    self.changeName = function () {
        self.name(secretName);
    };
};

ko.applyBindings(new Hero());
```

---
---

## pros and cons

* solves scope problems
* readable
* memory consumption
* inheritance may become messy (power constructor, override via constructor param)

---
---

## more complex example: loop, $root, customBinding

```javascript
<ul data-bind="foreach: {data: fruits}, stripe: fruits">
    <li>
        <span class="name" data-bind="text: name"></span> -
        #<span data-bind="text: id"></span>
        <a class="delete" data-bind="click: $root.deleteFruit">delete</a>
    </li>
</ul>
```

---
---

## mixed mode / transition

### 1. standard template, ko island

```javascript
ko.applyBindings(new SimpleTemplate(), $("#SimpleTemplate").get(0));
```

* needs mediator for different contexts
* good for interim solution (projects with mixed template renderers)
* "probably" not needed: late binding

---
---

### 2. ko template, standard island

* Ryan Niemeyer's blog
* special binding handler

```html
<div>
    <h2 data-bind="text: header"></h2>
    <div data-bind="stopBinding: true">
        <div id="profile">
            <input data-bind="value: first" />
            <input data-bind="value: last" />
        </div>
    </div>
</div>
```

---
---

## late binding into an existing global viewmodel

```html
<div id="Late">ajax-load content here</div>
<input type="button" value="inject" data-bind="click:inject" />
```

applyBindingsToDescendants:
```javascript
self.inject = function () {
    var html = '<p data-bind="text: name">... ... .. .';
    $("#Late").html(html);
    ko.applyBindingsToDescendants(self, $("#Late").get(0));
};
```

---
---

## custom binding handler

```html
<p data-bind="textWithInitials: name"></p>
Peter Parker
<strong>P</strong>eter <strong>P</strong>arker
```

```javascript
ko.bindingHandlers.textWithInitials = {
    update: function(element, valueAccessor, allBindingsAccessor, viewModel) {
        var value = valueAccessor();
        value = ko.utils.unwrapObservable(value);
        value = value.replace(/(\w*)\b/g, function(s) {
            return "<b>"+s.substr(0, 1)+"</b>"+s.substr(1);
        });
        $(element).html(value);
    }
};
```

---
---

## dom events and ko viewmodels

* mostly not needed (upshot/map/merge)
* if so: no need to modify the entity (entity.onClick)
* access viewmodel from dom event handler
* recursive tree: hard to find the real root: getSelf() vs ko.contextFor()

---
---

## onClick meets ko context

```javascript
$("#Late").on("click", "p", function (e) {
    var el = $(e.currentTarget),
        context = ko.contextFor(e.currentTarget);
    context.$data.name("Spiderman"); // = ko.dataFor
});
```

---
---

## extensibility

### bindingHandlers
* formMask (coverlayer)
* leave + clickElsewhere (dropdown menu)

### custom functions
* fn (observable, observableArray, computed, subscribable)
* ko.observableArray.fn.findById (static! myObsArray.findById("12345"))
* ko.subscribable.fn.subscribeDs (subscribe to datasource)

### utilities
* ko.utils (extend, makeArray, isIe6 etc.)
* ko.utils.cloneUnwrap (recursive unwrap + snapshot)
