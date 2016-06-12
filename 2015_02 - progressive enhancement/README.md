# Progressive enhancement

---
---

* graceful degradation `<----`
* progressive enhancement `---->`

---
---

## graceful degradation

* page must work without javascript
* fallback to something simpler
* we care for the past

---
---

## the web is a moving target

* browsers are more up to date
* users are more tech-savvy
* everything is going to be wired (from electrical grids and cars to tv sets, refrigerators etc.)
* popular services (twitter, instagram, foursquare, facebook, google technologies, sky drive etc.)
* javascript is the esperanto of the web, turning it off is not an option anymore
* mobile first movement

---
---

## progressive enhancement

* moving forward
* no more pixelfucking
* we care for modern browser engines (desktop, mobile, tablet etc.)
different user experience (but that's okay)
* both layout AND functionality may differ

---
---

* depends on browser support
* emulate less
* caniuse.com
* feature detection (minimize user agent hacks!) -> modernizr

---
---

## some notable functional differences

* workers - speed
* session/local storage - state
* websockets, video - flash
* canvas - 2d features, drawing... flash
* webgl - 3d animation
* geo location - map services
* input granularity - date, number, search, colorpicker
* history - navigation

---
---

## responsive layouts

* respond to the capabilities of different gadgets
* smaller screen estate
* rearrange content dynamically
* desktop resize vs. device resolution
* less relevant today (may be a dead end?!)
*not a solution for high-dpi devices (retina, full-hd)

---
---

## what is a bug?

* is this usable?
* life-like scenarios
* subtle differences are fine, showstoppers are not
