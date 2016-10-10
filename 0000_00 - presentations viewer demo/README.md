# Presentations Viewer demo

---
---

## Usage

* `npm i`
* `npm run dev` = starts both the client and the server with webpack + nodemon (default ports are 3000 + 3001)
* `npm run build` = builds the client, output is _./dist_
* `npm start` = starts koa static (default port is 3000)

---
---

## Keyboard shortcuts

* __HOME/0__ = first page
* __END/9__ = last page
* __f__ = toggle fullscreen
* __h__ = table of contents
* __+__ = increase font
* __-__ = decrease font
* __i__ = invert (switch theme)

---
---

## Markdown

* uses [marked](https://github.com/chjj/marked) with [github flavour](https://help.github.com/categories/writing-on-github/)
* __bold__ _italic_ ~~strikethrough~~
* inline quoted code `git status`
* and so on

### Highlighted code blocks

#### with highlight.js

```handlebars
<div class="entry">
  {{!-- only show if author exists --}}
  {{#if author}}
    <h1>{{firstName}} {{lastName}}</h1>
  {{/if}}
</div>
```

---
---

## Emojis

* emojione svg sprites
* works with unicode entities and shorthands
* [cheat sheet](http://www.webpagefx.com/tools/emoji-cheat-sheet/)

### Inline normal size

:joy: :astonished: :scream: :tired_face: :angry: :rage:
:triumph: :sleepy: :yum: :mask: :sunglasses: :dizzy_face: :imp:
:smiling_imp: :neutral_face: :no_mouth: :innocent: :alien:
:yellow_heart: :blue_heart: :purple_heart: :heart:

### Inline large

****:cop: :angel: :smiley_cat: :smile_cat:
:scream_cat: :crying_cat_face: :see_no_evil: :hear_no_evil: :speak_no_evil:****

(two strong tags required, will be broken on github)

---
---

## Loading images

* images must not be placed in subdirectories (path separators are not allowed)
* filename valid chars (all lowercase): a-z 0-9 -_.
* filename must end with jpg, jpeg, png, gif, or svg
* server has no real cache control or etag support, max age is one year

### Local

![Hamsterdance](hamster.gif)![Hamsterdance](hamster.gif)

### Online

![Placehold it](https://placeholdit.imgix.net/~text?txtsize=33&txt=online%20image&w=350&h=80)

(github may try to resolve it as a local url, though it really shouldn't)
