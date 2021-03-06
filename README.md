# localForage [![Build Status](https://secure.travis-ci.org/mozilla/localForage.png?branch=master)](http://travis-ci.org/mozilla/localForage)

localForage is a handy library that improves the offline experience of your web
app by using asynchronous storage (via IndexedDB or WebSQL where available) but
with a simple, `localStorage`-like API.

localForage includes a localStorage-backed fallback store for browsers with no
IndexedDB or WebSQL support. This means that asynchronous storage is available
in Chrome, Firefox, and Safari (including Safari Mobile).

## Browser Support

All of 'em. Worst-case localStorage fallback will be used, but asynchronous
storage will be used for:

* Android Browser (2.1+)
* Blackberry (7+)
* Chrome (23+)
* Chrome for Android (32+)
* Firefox (10+)
* Firefox for Android (25+)
* IE (10+)
* IE Mobile (10+)
* Opera (15+)
* Opera Mobile (11+)
* Phonegap/Apache Cordova (1.2.0+)
* Safari (3.1+)

Note that, because of WebSQL support, apps packaged with Phonegap will also
use asynchronous storage. Pretty slick!

## Support

Lost? Need help? This README has some simple guides and the code has decent
documentation, but I'm working on a real API guide. In the meantime, if you're
stuck using the library, running the tests, or want to contribute, you can
visit [irc.mozilla.org](https://wiki.mozilla.org/IRC) and head to the `#apps`
channel to ask questions about localForage.

# How to use localForage

## Callbacks

Because localForage uses async storage, it has an async API. It's otherwise
exactly the same as the
[localStorage API](https://hacks.mozilla.org/2009/06/localstorage/).

```javascript
// In localStorage, we would do:
localStorage.setItem('key', JSON.stringify('value'));
doSomethingElse();

// With localForage, we use callbacks:
localforage.setItem('key', 'value', doSomethingElse);
```

Similarly, please don't expect a return value from calls to
`localforage.getItem()`. Instead, use a callback:

```javascript
// Synchronous; slower!
var value = JSON.parse(localStorage.getItem('key'));
alert(value);

// Async, fast, and non-blocking!
localforage.getItem('key', alert);
```

You can store any type in localForage; you aren't limited to strings like in
localStorage. Even if localStorage is your storage backend, localForage
automatically does `JSON.parse()` and `JSON.stringify()` when getting/setting
values.

## Promises

Promises are pretty cool! If you'd rather use promises than callbacks,
localForage supports that too:

```javascript
function doSomethingElse(value) {
    console.log(value);
}

// With localForage, we allow promises:
localforage.setItem('key', 'value').then(doSomethingElse);
```

localForage relies on native [ES6 Promises](http://www.promisejs.org/), but
[ships with an awesome polyfill](https://github.com/jakearchibald/ES6-Promises)
for browsers that don't yet support ES6 Promises natively.

## Driver Selection (i.e. forcing localStorage)

For development, it can be easier to use the
slower--but easier to debug--localStorage driver (mostly because localStorage
can easily be inspected from the console). You can use the `setDriver()` method
to change the driver localForage is using at any time.
    
```javascript
// If you aren't using JS modules, things are loaded synchronously.
localforage.setDriver('localStorageWrapper');
alert(localforage.driver);
  => 'localStorageWrapper'

// If you're using modules, things load asynchronously, so you should use
// callbacks or promises to ensure things have loaded.
localforage.setDriver('localStorageWrapper', function() {
    alert(localforage.driver);
});
  => 'localStorageWrapper'

// The promises version:
localforage.setDriver('localStorageWrapper').then(function() {
    alert(localforage.driver);
});
  => 'localStorageWrapper'
```

You can actually force any available driver with this method, but given that
the best driver will be selected automatically when the library is loaded, this
method is mostly useful in forcing localStorage.

Note that trying to load a driver unavailable on the current browser (like
trying to load WebSQL in Gecko) will fail and the previously loaded "best
choice" will continue to be used.

## Backbone.js

localForage includes a [Backbone.js](http://backbonejs.org/) storage library
that you can use to store your Backbone collections offline with only a few
lines of really simple code.

Of course, Backbone.js is entirely optional and you can use localForage
without it!

# Running Tests

localForage is designed to run in the browser, so the tests explicitly require
a browser environment instead of any JavaScript environment (i.e. node.js).
The tests are run on both a headless WebKit (using
[PhantomJS](http://phantomjs.org)) and
["headless" Gecko](http://slimerjs.org/faq.html) (using
[SlimerJS](http://slimerjs.org/)). The tests are written using
[CasperJS's tester module](http://docs.casperjs.org/en/latest/modules/tester.html).
We run tests against Gecko and WebKit to ensure that IndexedDB and WebSQL
support is functioning as-expected.

On OS X, you'll need to install both PhantomJS and SlimerJS like so:

```shell
brew install phantomjs slimerjs
```

Generally you'll need a version of Firefox or XULRunner installed for SlimerJS
to run your tests. Once everything is installed you can simply type `make test`
to make sure the code is working as expected.

TODO: Provide Windows/Linux instructions; check into XULRunner setup.

# License

This program is free software; it is distributed under an
[Apache License](http://github.com/mozilla/localForage/blob/master/LICENSE).

---

Copyright (c) 2013-2014 [Mozilla](https://mozilla.org)
([Contributors](https://github.com/mozilla/localForage/graphs/contributors)).
