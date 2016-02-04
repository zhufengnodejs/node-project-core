[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][coveralls-image]][coveralls-url]
[![Gittip][gittip-image]][gittip-url]
[![David deps][david-image]][david-url]
[![node version][node-image]][node-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/project-core.svg?style=flat-square
[npm-url]: https://npmjs.org/package/project-core
[travis-image]: https://img.shields.io/travis/leizongmin/node-project-core.svg?style=flat-square
[travis-url]: https://travis-ci.org/leizongmin/node-project-core
[coveralls-image]: https://img.shields.io/coveralls/leizongmin/node-project-core.svg?style=flat-square
[coveralls-url]: https://coveralls.io/r/leizongmin/node-project-core?branch=master
[gittip-image]: https://img.shields.io/gittip/leizongmin.svg?style=flat-square
[gittip-url]: https://www.gittip.com/leizongmin/
[david-image]: https://img.shields.io/david/leizongmin/node-project-core.svg?style=flat-square
[david-url]: https://david-dm.org/leizongmin/node-project-core
[node-image]: https://img.shields.io/badge/node.js-%3E=_4.0-green.svg?style=flat-square
[node-url]: http://nodejs.org/download/
[download-image]: https://img.shields.io/npm/dm/project-core.svg?style=flat-square
[download-url]: https://npmjs.org/package/project-core

# project-core

## Installation

```bash
$ npm install project-core --save
```


## Usage

```javascript
'use strict';

const ProjectCore = require('project-core');

// create new instance
const project = new ProjectCore();

// namespace, you can store data in memory
project.data.set('abc.e.f', 123);
console.log(project.data.get('abc'));
// => {e: {f: 123}}

// util functions, extends from lei-utils
console.log(project.utils.md5('haha'));
// add your own function
project.utils.say = function () {};

// methods
// register, must be async function
// the first argument is an params object
// the last argument is callback function
project.method('my.hello').register(function (params, callback) {
  callback(null, params.a + params.b);
});
// register hook: before
project.method('my.hello').before(function (params, callback) {
  params.a = Number(params.a);
  params.b = Number(params.b);
  callback(null, params);
});
// register hook: after
project.method('my.hello').after(function (params, callback) {
  if (isNaN(params)) {
    callback(new TypeError('result is not a number'));
  } else {
    callback(null, params);
  }
});
// notes: when register hook, you can use wildcard in the method name
// for example, "my.*"
// call function
project.method('my.hello').call({a: 123, b: 456}, function (err, ret) {
  console.log(err, ret); // => null, 6
  // if passed {c: 123, d: 456} the result would be => TypeError: result is not a number
});

// extends
project.extends({
  // before: optional, will be called before initializing plugins
  before: function (next) {},
  // init: initialize plugin
  init: function (next) {},
  // after: optional, will be called after plugins initialized
  after: function (next) {},
});

// init queue
// all the functions that added to init queue will be called sequentially
project.init.add(function () {});
project.init.add(function () {});

// events
// ready: will be emitted when project inited
project.event.once('ready', function () {});
// you can emit a custom event
project.event.emit('haha', Math.random());

// add ready event listener
// if project was inited, callback immediately
project.ready(function () {});

// config
project.config.load('./config');
console.log(project.config.get('web.port'));
console.log(project.config.has('web.port'));

// init
project.init();
```

### Example config file

```javascript
module.exports = function (set, get, has) {

  set('web.session.secret', '11111111');
  // you can use the get(name) and has(name) to get specific config value

};
```

### Extends and republish your own `project-core`

```javascript
'use strict';

const ProjectCore = require('project-core');

const project = new ProjectCore();

// extends example
project.extends(require('./extends_config'));
project.extends(require('./extends_orm'));
project.extends(require('./extends_cache'));
project.extends(require('./extends_module'));
project.extends(require('./extends_status'));

// do something when ready
project.event.once('ready', function () {
  // after ready event has been emitted, you can not change anything on project-core instance any more
});

// exports
module.exports = project;
```


## License

```
The MIT License (MIT)

Copyright (c) 2016 Zongmin Lei <leizongmin@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
