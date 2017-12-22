# Hornet-js-gc-monitor

Gestionnaire du garbage collector de Nodejs (v8).

Ce package est testé uniquement avec les versions de node 0.10, 0.12 et 4.X.

_Note: ce module ne fonctionne actuellement que sous Linux_.

## Rapport de statistiques GC

Ce module collecte les statistiques GC et les place dans un objet Javascript.
Vous trouverez ci dessous une liste des données que le module rapporte periodiquement:

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

Cet objet est stocké dans l'objet "process" avec une clé personnalisable (par défaut: "gcMonitor").

## Installation

Avec [npm](http://npmjs.org), faites:

```
npm install hornet-js-gc-monitor
```

## Usage

```javascript
var hornetGcMonitor = require("hornet-js-gc-monitor");

// start sans argument -> statistiques disponibles dans : process.gcMonitor
hornetGcMonitor.start();
var gcStatistics = process.gcMonitor;

// ou start avec des arguments -> statistiques disponibles dans process.customKey
hornetGcMonitor.start("customKey"); 
var gcStatistics = process.customKey;

```