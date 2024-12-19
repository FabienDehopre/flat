# flat-esm [![Build Status](https://github.com/FabienDehopre/flat/actions/workflows/main.yml/badge.svg)](https://github.com/FabienDehopre/flat/actions/workflows/main.yml)

This package is a fork of the original [`flat`](https://github.com/hughsk/flat) library but the JavaScript files have the `.mjs` extension instead of the `.js` extension. This does not change the behavior of the library but it fixes the "jest encountered an unexpected token" issue when executing unit test with jest.

Take a nested Javascript object and flatten it, or unflatten an object with
delimited keys.

## Installation

``` bash
$ npm install flat-esm
```

## Methods

### flatten(original, options)

Flattens the object - it'll return an object one level deep, regardless of how
nested the original object was:

``` javascript
import { flatten } from 'flat'

flatten({
    key1: {
        keyA: 'valueI'
    },
    key2: {
        keyB: 'valueII'
    },
    key3: { a: { b: { c: 2 } } }
})

// {
//   'key1.keyA': 'valueI',
//   'key2.keyB': 'valueII',
//   'key3.a.b.c': 2
// }
```

### unflatten(original, options)

Flattening is reversible too, you can call `unflatten` on an object:

``` javascript
import { unflatten } from 'flat'

unflatten({
    'three.levels.deep': 42,
    'three.levels': {
        nested: true
    }
})

// {
//     three: {
//         levels: {
//             deep: 42,
//             nested: true
//         }
//     }
// }
```

## Options

### delimiter

Use a custom delimiter for (un)flattening your objects, instead of `.`.

### safe

When enabled, both `flat` and `unflatten` will preserve arrays and their
contents. This is disabled by default.

``` javascript
import { flatten } from 'flat'

flatten({
    this: [
        { contains: 'arrays' },
        { preserving: {
              them: 'for you'
        }}
    ]
}, {
    safe: true
})

// {
//     'this': [
//         { contains: 'arrays' },
//         { preserving: {
//             them: 'for you'
//         }}
//     ]
// }
```

### object

When enabled, arrays will not be created automatically when calling unflatten, like so:

``` javascript
unflatten({
    'hello.you.0': 'ipsum',
    'hello.you.1': 'lorem',
    'hello.other.world': 'foo'
}, { object: true })

// hello: {
//     you: {
//         0: 'ipsum',
//         1: 'lorem',
//     },
//     other: { world: 'foo' }
// }
```

### overwrite

When enabled, existing keys in the unflattened object may be overwritten if they cannot hold a newly encountered nested value:

```javascript
unflatten({
    'TRAVIS': 'true',
    'TRAVIS.DIR': '/home/travis/build/kvz/environmental'
}, { overwrite: true })

// TRAVIS: {
//     DIR: '/home/travis/build/kvz/environmental'
// }
```

Without `overwrite` set to `true`, the `TRAVIS` key would already have been set to a string, thus could not accept the nested `DIR` element.

This only makes sense on ordered arrays, and since we're overwriting data, should be used with care.


### maxDepth

Maximum number of nested objects to flatten.

``` javascript
import { flatten } from 'flat'

flatten({
    key1: {
        keyA: 'valueI'
    },
    key2: {
        keyB: 'valueII'
    },
    key3: { a: { b: { c: 2 } } }
}, { maxDepth: 2 })

// {
//   'key1.keyA': 'valueI',
//   'key2.keyB': 'valueII',
//   'key3.a': { b: { c: 2 } }
// }
```

### transformKey

Transform each part of a flat key before and after flattening.

```javascript
import { flatten, unflatten } from 'flat'

flatten({
    key1: {
        keyA: 'valueI'
    },
    key2: {
        keyB: 'valueII'
    },
    key3: { a: { b: { c: 2 } } }
}, {
    transformKey: function(key){
      return '__' + key + '__';
    }
})

// {
//   '__key1__.__keyA__': 'valueI',
//   '__key2__.__keyB__': 'valueII',
//   '__key3__.__a__.__b__.__c__': 2
// }

unflatten({
      '__key1__.__keyA__': 'valueI',
      '__key2__.__keyB__': 'valueII',
      '__key3__.__a__.__b__.__c__': 2
}, {
    transformKey: function(key){
      return key.substring(2, key.length - 2)
    }
})

// {
//     key1: {
//         keyA: 'valueI'
//     },
//     key2: {
//         keyB: 'valueII'
//     },
//     key3: { a: { b: { c: 2 } } }
// }
```

## Command Line Usage

`flat` is also available as a command line tool. You can run it with [`npx`](https://docs.npmjs.com/cli/v8/commands/npx):

```sh
npx flat-esm foo.json
```

Or install the `flat-esm` command globally:
 
```sh
npm i -g flat-esm && flat foo.json
```

Accepts a filename as an argument:

```sh
flat foo.json
```

Also accepts JSON on stdin:

```sh
cat foo.json | flat
```
