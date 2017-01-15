---
layout: default
id: index
title: Getting started
next: why
---


**BunnyJS** is a modern **Vanilla JS** and ES6 library and next-generation front-end framework, package of small stand-alone components without dependencies.

* No dependencies - can be used in any project anywhere anytime
* 0 learning curve - you can start right now, just plain JavaScript with simple architecture easy to maintain and extend
* Designed in mind to build modern, complicated, real world business apps
* Faster, simpler, enjoyable than any frontend framework
* Large set of ready components, custom UI elements and utils
* LTS

For help & ideas - [DM me on Twitter](https://twitter.com/Mevrael)

## Browser support

IE 11, latest Chrome, Firefox, Edge, Safari, Android 5.1+, iOS 10+

Almost every component may be used also on very old platforms by using a transpilation and polyfills.

## Installation

1. Install via `npm install bunnyjs --save`
2. [Rollup.js](http://rollupjs.org) with babel and npm plugins is recommended for transpiling and bundling.
3. Or you can just use [Assets Builder](https://github.com/Mevrael/assets-builder) which will automatically build your future JS and CSS with 1 command.
4. Or just include into HTML already transpiled and minified JS from `dists` folder or any [CDN](https://unpkg.com/bunnyjs/dist).
5. Probably some polyfills for IE might be required depending on Component.

```html
<script src="https://unpkg.com/bunnyjs/dist/..."></script>
```

## Usage

BunnyJS is written on the latest ECMAScript standard (latest version of JavaScript) which already works in every platform.

<div class="alert alert-info">BunnyJS can be used without any transpilation (process transforming latest JS into old one) and without any build tools!

BunnyJS can be used in any Vanilla, React, Vue, Angular or other application.</div>

<div class="alert alert-danger">However, browsers does not support ES6 modules (export/import) yet. It is also recommended to bundle all your scripts into a single file. For that job you can use [Rollup.js](http://rollupjs.org) - the next-generation JavaScript module bundler.</div>

Rollup.js is like a CSS @import rule for JS. It just allows you to import other JS files into your app.js.

<div class="alert alert-warning">If you need to support IE or other old platforms then you also need to transpile your code. BunnyJS is easy to integrate into your existing build process. If you don't have any - you may use [Babel](https://babeljs.io) - a powerful compiler for writing next generation JavaScript.</div>

Finally, you can just use already compiled BunnyJS files from the `dists` folder. You can test it by using a [CDN](https://unpkg.com/bunnyjs/dist).

## Basic example

### A. Using a build process (recommended)

Compiling your application not only decreases the size of your production code, but also automatically includes only those functions you really use. As a result your app loads faster for end-users and your codebase is more maintainable.

Usually you have a main `index.js` or `app.js` which is called an **entry file** and just imports other JS files.

<div class="alert alert-success">Today there are many build tools like Webpack, Browserify, Gulp, Grunt and many others. If you don't have own build process in your project or don't want to waste a lot of time reading many documentations and configuring different tools which often may fail, then you can just use a **[Bunny Assets Builder](https://github.com/Mevrael/assets-builder)** - simple assets builder which compiles your JS and CSS and it just works</div>

Skip Bunny Assets Builder installation if you have own build process already configured.

```javascript
npm install assets-builder --save-dev
```

1. Create `resources/assets/js` folder
1. Create `app.js` file
1. Create `public/build` folder
1. Create `build.js` file

In build.js:

```javascript
const CLI = require('assets-builder');

CLI.init();

CLI.run();
```

In resources/assets/js/app.js:

```javascript
import { ready } from 'bunnyjs/src/utils/DOM';

ready(() => {
  console.log('DOM content loaded');
});
```

Now let the Bunny Assets Builder do the magic for you:
```javascript
node build js -p
```

Compiled and minified file generated in `public/build/app.js`.

Make sure `public` folder is your document root (application's entry point for your web server)

Include it into your app within HTML layout before the `</body>`:

```html
  <script src="/build/app.js"></script>
</body>
```

Now open your app in browser, open Dev Tools (F12) and you should see in the console - `DOM content loaded`

### B. Using a compiled min.js

Download files you need from a [CDN](https://unpkg.com/bunnyjs/dist/). For example download [utils-dom.min.js](https://unpkg.com/bunnyjs/dist/utils-dom.min.js) and put it into `public/vendor/bunnyjs/utils-dom.min.js`;

Create `public/js/app.js` and put:

```javascript
ready(function() {
  console.log('DOM content loaded');
});
```

Finally include files into HTML layout before the `</body>`:

```html
  <script src="/vendor/bunnyjs/utils-dom.min.js"></script>
  <script src="/js/app.js"></script>
</body>
```