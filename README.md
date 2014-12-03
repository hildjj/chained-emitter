# ChainedEventEmitter

ChainedEventEmitter is a an implementation of the EventEmitter found in Node.js,
based on EventEmitter2, but adding the ability to return a promise in an event
handler, so that the next handler waits for the previous to finish before
firing.  As well, `emit()` returns a promise that is fulfilled when all of the
handlers have fired.

## Features

 - Everything from EventEmitter2
 - Event chains

## Differences (Non breaking, compatible with existing EventEmitter, EventEmitter2)

 - The constructor takes a configuration object.

```javascript
    var ChainedEmitter = reuire('ChainedEmitter').EventEmitter;
    var server = new ChainedEmitter({
      parallel: true, // fire second handler before the first resolves, defaults to false
    });
```

 - Returning a promise

```javascript
    var bb = require('bluebird')
    server.on('foo.*', function(value1, value2) {
      return bb.defer().promise
    });
```

## API

#### emitter.addListener(event, listener)
#### emitter.on(event, listener)

Adds a listener to the end of the listeners array for the specified event.  If
the listener returns a [bluebird](https://github.com/petkaantonov/bluebird) promise, the promise returned by `emit()` will not
resolve until the returned promise resolves.  If the `parallel` configuration
is `false`, the next handler will also wait to fire before the returned promise
resolves.  If you return anything but a promise (including `undefined` or
`null`), the next action will happen immediately.

```javascript
    server.on('data', function(value1, value2, value3 /* accepts any number of expected values... */) {
      var me = bb.defer();
      return me.promise; // wait for me!
    });
```

```javascript
    server.on('data', function(value) {
      return; // don't wait!
    });
```

## Licence

(The MIT License)

Copyright (c) 2011 hij1nx <http://www.twitter.com/hij1nx>  
Copyright (c) 2013 hildjj <http://www.twitter.com/hildjj>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
