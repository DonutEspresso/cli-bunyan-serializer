# lumberjill

[![NPM Version](https://img.shields.io/npm/v/lumberjill.svg)](https://npmjs.org/package/lumberjill)
[![Build Status](https://travis-ci.org/DonutEspresso/lumberjill.svg?branch=master)](https://travis-ci.org/DonutEspresso/lumberjill)
[![Coverage Status](https://coveralls.io/repos/github/DonutEspresso/lumberjill/badge.svg?branch=master)](https://coveralls.io/github/DonutEspresso/lumberjill?branch=master)
[![Dependency Status](https://david-dm.org/DonutEspresso/lumberjill.svg)](https://david-dm.org/DonutEspresso/lumberjill)
[![devDependency Status](https://david-dm.org/DonutEspresso/lumberjill/dev-status.svg)](https://david-dm.org/DonutEspresso/lumberjill#info=devDependencies)


> a bunyan wrapper for simple CLT logging

A thin wrapper over bunyan for outputting simple style logs, which are most
useful for command line tools. Supports full stack trace outputting for Error
objects created via VError and restify-error.

## Getting Started

Install the module with: `npm install lumberjill`

## Usage

Create a logger first:

```js
var lumberjill = require('lumberjill');
var log = lumberjill.create({
    name: 'test-logger',
    level: lumberjill.INFO
});
```

Any logs go to stderr:

```js
log.info({
    hello: 'world'
}, 'my first message!');
```

```sh
$ node test.js
[test-logger] info my first message! {
  "hello": "world"
}
```

Support for [VError](https://github.com/joyent/node-verror) serialization is
built-in:

```sh
var VError = require('verror');

var underlyingErr = new Error('underlying boom!');
var wrapErr = new VError({
	name: 'SomethingBadHappenedError',
	cause: underlyingErr,
	info: {
		bar: 1,
		baz: 2
	}
}, 'something went boom!');

log.error({
	hello: 'world',
	err: wrapErr
}, 'oh noes!');

// you can also log the error directly, if you don't have any other context to
// log
log.error(wrapErr, 'oh noes!');
```

```sh
$ node test.js
[test-logger] error oh noes! {
  "hello": "world"
}
SomethingBadHappenedError: something went boom!: underlying boom! (bar=1, baz=2)
    at Object.<anonymous> (/Users/aliu/Sandbox/npm/lumberjill/test.js:11:15)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Function.Module.runMain (module.js:441:10)
    at startup (node.js:139:18)
    at node.js:974:3
Caused by: Error: underlying boom!
    at Object.<anonymous> (/Users/aliu/Sandbox/npm/lumberjill/test.js:10:21)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Function.Module.runMain (module.js:441:10)
    at startup (node.js:139:18)
    at node.js:974:3
```

Support for [restify-errors](https://github.com/restify/errors) is also
built-in:

```js
var restifyErrs = require('restify-errors');
restifyErrs.makeConstructor('SomethingBadHappenedError');

var underlyingErr = new Error('underlying boom!');
var wrapErr = new restifyErrs.SomethingBadHappenedError(underlyingErr, {
    message: 'something went boom!',
    cause: underlyingErr,
    context: {
        bar: 1,
        baz: 2
    }
});


log.error({
    hello: 'world',
    err: wrapErr
}, 'oh noes!');

// you can also log the error directly, if you don't have any other context to
// log
log.error(wrapErr, 'oh noes!');
```

```sh
$ node test.js
[test-logger] error oh noes! {
  "hello": "world"
}
SomethingBadHappenedError: something went boom! (bar=1, baz=2)
    at Object.<anonymous> (/Users/aliu/Sandbox/npm/lumberjill/test.js:13:15)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Function.Module.runMain (module.js:441:10)
    at startup (node.js:139:18)
    at node.js:974:3
Caused by: Error: underlying boom!
    at Object.<anonymous> (/Users/aliu/Sandbox/npm/lumberjill/test.js:12:21)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Function.Module.runMain (module.js:441:10)
    at startup (node.js:139:18)
    at node.js:974:3
```


## API


### TRACE, DEBUG, INFO, WARN, ERROR, FATAL
Properties on the main exports object that map to logging levels used by bunyan:
These are available primarily so you can instantiate loggers using friendly
level names instead of the numbers used by bunyan.

```sh
$ node
> require('lumberjill');
{ create: [Function: create],
  TRACE: 10,
  DEBUG: 20,
  INFO: 30,
  WARN: 40,
  ERROR: 50,
  FATAL: 60 }
```


### create(options)
Create an instance of a lumberjill logger, which is basically just a bunyan
logger. Takes the following options:

 * `options` {Object} an options object
 * `options.name` {String} name of the logger
 * `[options.raw]` {Boolean} when true, logs raw bunyan JSON records
 * `[options.timestamp]` {Boolean} when true, prepends logs with timestamp
 * `[options.level]` {Number} Possible values 0, 10, 20, 30, 40, 50, 60. Can
be conveniently used via the main exports object by using the logger friendly
name levels.

__Returns__: {Object} a bunyan logger. Supports all bunyan logger methods.


## Contributing

Ensure that all linting and codestyle tasks are passing. Add unit tests for any
new or changed functionality.

To start contributing, install the git prepush hooks:

```sh
make githooks
```

Before committing, lint and test your code using the included Makefile:
```sh
make prepush
```

## License

Copyright (c) 2018 Alex Liu

Licensed under the MIT license.
