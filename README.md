#dprof

> Sync but mostly async profiling and visualizing done dynamically

## Installation

```sheel
npm install dprof
npm install -g dprof
```

## Example

* Add `require('dprof')` to the beginning of you script to profile it.
* Run the script, when done a `dprof.json.gz` is created in your `cwd`.
* To start the visualizer run `gzcat dprof.json.gz | dprof`
* Now open [http://localhost:3343](http://localhost:3343) in your browser

```javascript
require('dprof');

var fs = require('fs');

fs.open(__filename, 'r', function (err, fd) {

  var count = 0;
  var a = new Buffer(10);
  fs.read(fd, a, 0, 10, 0, function (err2) {
    if (++count === 2) close();
  });

  var b = new Buffer(10);
  fs.read(fd, b, 0, 10, 10, function (err2) {
    if (++count === 2) close();
  });

  function close() {
    fs.close(fd, function (err) {

    });
  }
});
```

## Visualizer

```shell
gzcat dprof.json.gz | dprof
```

The visualizer is WIP, you are welcome to contribute with major changes to the existing one.

![Visualizer](https://github.com/AndreasMadsen/dprof/blob/master/visualizer.png)

* Blue: time spend waiting for async response
* Red: time spend doing callback (sync)
* Black: Indicates when the async request was made.

## Format

The `dprof.json.gz` file is a GZIP compressed JSON file. It is possible
to get an uncompressed file, just set the environment variable `NODE_DPROF_DEBUG`.

The format `dprof.json` is simple, there is an initial part:

```javascript
{
  version: String, // the version of dprof there generated this JSON file
  total: Number, // execution time in nanoseconds
  root: Node
}
```

Each `Node` then have the format:

```javascript
{
  name: String,    // Handle name of the async operation
  stack: [         // Contains the stack leading up to the async operation
    {
      description: String,
      filename: String,
      column: Number,
      line: Number
    }, ...
  ],
  init: Number,    // Timestamp for when the async operation is requested.
  before: Number,  // Timestamp for when the callback is about to be called.
  after: Number    // Timestamp for when the callback is finished.
                   // All timestamps are relative to the process startup time
                   // and the unit is nanoseconds.

  children: [      // Shows async operations created in the callback
    Node, ...
  ]
}
```

## License

**This software is licensed under "MIT"**

> Copyright (c) 2015 Andreas Madsen
>
> Permission is hereby granted, free of charge, to any person obtaining a copy
> of this software and associated documentation files (the "Software"), to deal
> in the Software without restriction, including without limitation the rights
> to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
> copies of the Software, and to permit persons to whom the Software is
> furnished to do so, subject to the following conditions:
>
> The above copyright notice and this permission notice shall be included in
> all copies or substantial portions of the Software.
>
> THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
> IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
> FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
> AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
> LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
> OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
> THE SOFTWARE.
