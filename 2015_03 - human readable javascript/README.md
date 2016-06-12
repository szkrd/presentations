# Érthető javascript

---
---

## Mi az érthető?

* nem a sarki fűszeres
* megérti egy programozásban járatos földi halandó (ismeri az alapvető programnyelvi szekvenciákat, típusokat: elágazás, ciklus, függvények stb.)

---
---

## Robosztus kódhoz nem árt

* Jól elkülönülő részegységek (egységbe zárás)
* Egy függvény fejezze ki értelmesen céljait, tegye azt, amit feladatként elvárnánk tőle (absztrakció)
* A kód legyen újrahasznosítható, újrafelhasználható (örökítés)
* Több, hasonló feladatot ellátó függvénnyel értelmesen tudjak kommunikálni (többalakúság, interfészek)

---
---

## Pár általános jótanács

* **dry** - don't repeat yourself
* **die** - duplication is evil
* **kiss** - keep it simple stupid
* **yagni** - you ain't gonna need it

---
---

## Milyen javascript?

* node? rhino? nashorn?
* most: ami a browserben fut.
* javascript !== java

---
---

## Java vs JavaScript, tévedések

* **eval** abuse (miért, lehet máshogy is?)
* **setTimeout** ugye akkor thread-el?
* **new** Boolean(), new String() stb. - omfg (MDN: do not use!)
* var **function** vs function name (wtf?)
* **new** keyword (TKK (totális kibaszás kategória))
* this, proto, constructor, super (kb minden, ami prototípusból fakad; többet árt, mint használ!)
* **delete**, GC prekoncepciók

---
---

## Deviáns elemek

* package
* import
* class
* private, protected, public
* this

---
---

## Package

* package-ek, namespace-ek: NINCSENEK.
* object !== namespace.
* Recursive object creation - fake namespacing
* myApp["suck.this"] - namespace separator vs. object notation rules

---
---

## Import

* véletlenül se találjunk ki olyat, aminek van előzménye! -> commonjs
* var example = require('./example.js');
* cjs -> rugalmas, de nincs async, elsősorban szerver (node), nyelv-specifikus
* kliens: legyen async és lehetőleg ne kelljen hozzányúlni a nyelvhez (hoztam is, meg nem is)
* AMD -> advanced module definition
* Yes, it is advanced. Async és a dependencia-problémát is megoldja (kb nincs is más megoldás a problémára -> max pp-k, builderek)

---
---

## OMFG

```javascript
define("BlogApp", [ // {{{
	"ui/magicalSkin/DropDown",
	"ui/magicalSkin/RotatingRadio",
	"ui/support/PasswordStrengthIndicator",
	"obj/stripMerge",
	"string/pwStrengthCalculator"
], function (
	"DropDown",
	"RotatingRadio",
	"PasswordStrengthIndicator",
	"objStripMerge",
	"strPwStrengthCalculator"
) { // }}}
```

---
---

## Classes

Love-hate relationship: egy valamirevaló frontendes gyűlöli és mindig elmondja, hogy js-ben nincs szükség osztály típusú öröklődésre.

Miközben minden eszközzel az OOP princípiumokat igyekszik reprodukálni.

---
---

* **Class emulation** (fake super/parent: runtime (re)binding) - resig szépen elmagyarázza **DE:** elwrappeli a függvényt, fals prekoncepciókat szül (de hát mi nem?)
* **Object mixins** ($.extend, v. végső soron Object.create is) -> külön szép, ahogy pl. Zakasék próbálják belegyúrni az encapsulation-t (es5 pseudo-getter/setter) **DE:** Nagyon nehéz dokumentálni, észnél kell lenni
* **Minden konstruktor**: `x = func(){ var _y = 1; this.y = 2;}` - illetve azon belül apply-jal elérhető a mixin **DE:** Erőforrás-igényes, new-abuse/new-itis knockout de facto style
* **Power constructors** (Crockford): olyan, mint a fat constructor, de param override-okkal kombinálva **DE:** két-három lépcsős "örökítés" után gyilkos, amúgyis nehezen követhető
* **Function prototype**: a jó öreg klasszikus (proto megadásánál lehet játszani) **DE:** Örökítés nem intuitív (a nyelv ismerete nélkül értelmezhetetlen), object-reference medvecsapda továbbra is ott figyel

---
---

## Láthatóság

* nincs (Crockford: ne is erőlködjetek, "nem az")
* pretend privacy, dangling
* instance privates (var seal): kb. hozzáférhetetlen power constructor pattern nélkül
* nem lesz: ES6-ban meg van mondva, h a class keyword syntactic sugar lesz!!
* látszik, h van rá igény (pl. ki hogyan akarja megerőszakolni a define property-t)
* eszement erőfeszítésekkel emulálható (pl. Troop library, v végső soron recompilerrel)

---
---

## Vakvágány példa

```javascript
var myClass = troop.base.extend()
    .addPrivate({
        secret: "ufo"
    }).addPublic({
        hello: "world"
    }).addConstant({
        pi: 3.14
    }).addMethod({
        init: function (place) {
            this.addPublic({
                hello: place
            });
        }
    });
```
