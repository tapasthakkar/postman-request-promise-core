# request-promise-core

This package is the core for the following packages:

- [`postman-request-promise`](https://github.com/tapasthakkar/postman-request-promise)

`postman-request-promise-core` contains the core logic to add Promise support to [`postman-request`](https://github.com/postmanlabs/postman-request).

Please use one of the libraries above. It is only recommended to use this library directly, if you have very specific requirements.

## Installation for `request@^2.34`

This module is installed via npm:

```
npm install --save postman-request
npm install --save postman-request-promise-core
```

`postman-request` is defined as a peer-dependency and thus has to be installed separately.

## Usage for `postman-request`

``` js
// 1. Load the request library

// Only use a direct require if you are 100% sure that:
// - Your project does not use request directly. That is without the Promise capabilities by calling require('postman-request').
// - Any of the installed libraries use request.
// ...because Request's prototype will be patched in step 2.
/* var request = require('postman-request'); */

// Instead use:
var stealthyRequire = require('stealthy-require');
var request = stealthyRequire(require.cache, function () {
    return require('postman-request');
});


// 2. Add Promise support to request

var configure = require('postman-request-promise-core/configure/request2');

configure({
    request: request,
	// Pass your favorite ES6-compatible promise implementation
    PromiseImpl: Promise,
	// Expose all methods of the promise instance you want to call on the request(...) call
    expose: [
        'then',   // Allows to use request(...).then(...)
        'catch',  // Allows to use request(...).catch(...)
        'promise' // Allows to use request(...).promise() which returns the promise instance
    ],
    // Optional: Pass a callback that is called within the Promise constructor
    constructorMixin: function (resolve, reject) {
        // `this` is the request object
        // Additional arguments may be passed depending on the PromiseImpl used
    }
});


// 3. Use request with its promise capabilities

// E.g. crawl a web page:
request('http://www.google.com')
    .then(function (htmlString) {
        // Process html...
    })
    .catch(function (err) {
        // Crawling failed...
    });
```

## Change History

- 1.1.4 (2020-07-21)
    - Security fix: bumped `lodash` to `^4.17.19` following [this advisory](https://www.npmjs.com/advisories/1523).
- 1.1.3 (2019-11-03)
    - Security fix: bumped `lodash` to `^4.17.15`. See [vulnerabilty reports](https://snyk.io/vuln/search?q=lodash&type=npm).
      *(Thanks to @daniel-nagy for pull request [#20](https://github.com/request/promise-core/pull/20) and thanks to @quetzaluz for reporting this in issue [#21](https://github.com/request/promise-core/issues/21).)*
- 1.1.2 (2019-02-14)
    - Security fix: bumped `lodash` to `^4.17.11`. See [vulnerabilty reports](https://snyk.io/vuln/search?q=lodash&type=npm).
      *(Thanks to @lucaswillering and @sam-warren-finnair for reporting this in issues [#12](https://github.com/request/promise-core/issues/12) and [#13](https://github.com/request/promise-core/issues/13) and thanks to @Alec321 for pull request [#14](https://github.com/request/promise-core/pull/14).)*
- 1.1.1 (2016-08-08)
    - Renamed package to `request-promise-core` because there were [too](https://github.com/request/request-promise/issues/137) [many](https://github.com/request/request-promise/issues/141) issues with the scoped package name `@request/promise-core`
- 1.1.0 (2016-07-30)
    - Added `constructorMixin` option to enable [request/request-promise#123](https://github.com/request/request-promise/pull/123)
- 1.0.0 (2016-07-15)
    - All tests green, ready for prime time
- 1.0.0-rc.1 (2016-07-10)
    - Reimplementation of core logic based on `request-promise@3.0.0`
    - Plus `transform2xxOnly` option (fixes [request/request-promise#131](https://github.com/request/request-promise/issues/131))

## License (ISC)

In case you never heard about the [ISC license](http://en.wikipedia.org/wiki/ISC_license) it is functionally equivalent to the MIT license.

See the [LICENSE file](LICENSE) for details.
