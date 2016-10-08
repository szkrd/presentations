# Node on Windows

---
---

## Scope

* use windows 10
* going native (no cygwin or virtualbox)
* 64bit
* server side node development (not just gulp/grunt)

---
---

## Git

* vanilla install
* stick to ~/Projects
* create _.bash_profile_
* use [cmder](http://cmder.net/) instead of cmd.exe
* launch console with Admin (symlinks/mklink work)

---
---

## Npm

* npm --version
* use npm >= 3 - may need [3rd party updater](https://github.com/felixrieseberg/npm-windows-upgrade#usage)
* avoid `npm -g foo` installs

---
---

## Build tools

* install Python 2.x (because of gyp, node-gyp)
* env var _GYP_MSVS_VERSION=2015_
* do install Visual Studio (VS2015, _Visual Studio 2015 Community Edition_ is fine)
* install with: visual C++, win 7/8.1 sdk

---
---

## Env vars, package.json

* use [cross-env](https://www.npmjs.com/package/cross-env)
* use [dotenv](https://www.npmjs.com/package/dotenv)
* put stuff in package.json scripts section, avoid global tools
* use scripts folder for complex scripts

---
---

## Nodemon

* never commit env files
* `git config --global core.excludesfile ~/.gitignore_global`
* may use `source nodemon.sh`
* consider [PM2](https://www.npmjs.com/package/pm2)

---
---

## Troubleshooting-20161001

* error: `error C2373: '__pfnDliNotifyHook2': redefinition`
* cause: npm version below 3.10.8
* fix: `npm -g install npm@next && npm config set msvs_version 2015 --global`
