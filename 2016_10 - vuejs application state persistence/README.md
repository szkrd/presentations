# Application state persistance in Vue.js

## [github.com/szkrd/](https://github.com/szkrd/)

---
---

## Vue.js

* nice [guide](http://vuejs.org/guide/)
* data binding (mostly two-way)
* uses getters/setters (others: fn, dirty check, proxies)
* computed props, deep watching
* virtual dom
* filters, directives, components
* v-model

---
---

## Example

https://jsfiddle.net/szabi/xm2bkgn6/

```html
<div id="app">
  Hello {{ name }}!<br>
  <input type="text" v-model="name">
</div>
```

```js
new Vue({
  el: '#app',
  data: () => ({ name: 'John' })
})
```

---
---

## Vue 1 vs Vue 2

* lots of small things were removed
* new lifecycle hooks
* no more two way props (or prop mutations)
* directives are simpler (and are not instanciated)
* event model (total overhaul, no more dispatch or broadcast, on/emit is for self)
* filters are for text modifications (no more v-for, v-model etc.)
* interpoation inside attributes (`<b class="icon-{{ type }}"></b>`)

---
---

## Solutions

* stuff removed :arrow_right: migration assistant
* lifecycle changes :arrow_right: created, mounted, destroyed
* two way props :arrow_right: whoops, maybe event emissions?
* directives :arrow_right: whoops, rethink your directives
* event model :arrow_right: whoops, use an eventbus or a store
* filters :arrow_right: use computed observables
* interpolation :arrow_right: use computed observables

---
---

## Event models

1. parent-child messaging
2. eventbus
3. (shared observable store)
4. (flux-style store)

---
---

## Event model: parent-child

* one level!
* down: prop
* up: event

https://jsfiddle.net/szabi/u1Lthsp9/

```
    ┌-------------┐
    |    Parent   |
    └-------------┘
      ▲         |
  evt |         | prop
      |         ▼
    ┌-------------┐
    |    Child    |
    └-------------┘
```

---
---

## Event model: eventbus

* up vs down: vue1 (dispatch vs broadcast)
* uniformed broadcaster:
  * vue2: `new Vue()`
  * node EventEmitter (using webpack/browserify)
  * html Custom Event on document

---
---

### EventEmitter example

```
const EventEmitter = require('events')
class EventBus extends EventEmitter {}
export default new EventBus()
```

* Fire: `eventBus.emit('errorEvent', 0)`
* Listen: `eventBus.on('errorEvent', code => console.error(code))`

---
---

WIP

Exploding complexity (early years :arrow_right: spa )
Spa problems: back/forward/reload
Session vs local

One way data flow

Event bus (new Vue vs EventEmitter vs HTML Custom events)

Single source of truth

URL

URL + storage

prop - event cycle

sync to session

world writable observable

store (vuex, redux etc.)
