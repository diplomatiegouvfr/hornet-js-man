# hornet-js-gc-monitor

Nodejs v8 garbage collector monitoring

This package is tested only with Node versions 0.10, 0.12 and 4.x

_Note: This module currently works only on Linux operating systems_.

## GC statistics reporting

This module collects GC statistics and put them into a Javascript Object :
Here is the list of data the module reports periodically:
```
  {
    "total": {
      "count": 10,
      "time": 47.908777
    },
    "scavenge": {
      "count": 8,
      "time": 41.046463,
      "maxtime": 7.650448
    },
    "marksweep": {
      "count": 2,
      "time": 6.862314,
      "maxtime": 6.859382
    }
  }
```
The object is stored into the "process" object with a customizable key (default: "gcMonitor").

# Installation

With [npm](http://npmjs.org) do:

```
npm install hornet-js-gc-monitor
```

# Usage

```javascript
var hornetGcMonitor = require("hornet-js-gc-monitor");

// start without argument > statistics available at : process.gcMonitor
hornetGcMonitor.start();
var gcStatistics = process.gcMonitor;

// or start with argument. ex: statistics available at process.customKey
hornetGcMonitor.start("customKey"); 
var gcStatistics = process.customKey;

```