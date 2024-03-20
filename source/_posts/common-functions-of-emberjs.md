title: Ember.js常用函数
tags:
  - emberjs
categories:
  - programming
  - javascript
  - emberjs
  
---



While developing stuff with Ember.js I stumbled upon some very nice and handy functions which are built in. This post lists some of them and describes in a short what they are used for.
当用Ember.js开发时，我发现了一些优美且实用的函数。本文列出了它们，并简短地说明了用法。

<!--more-->


The following functions are defined in `packages/ember-runtime/lib/core.js` and `packages/ember-metal/lib/utils.js` respectively.


Returns `true` if argument is `null` or `undefined`.

``` js none https://github.com/emberjs/ember.js/blob/3b60016c2b6b38646d954014d10f74fcfe1a3d54/packages/ember-runtime/lib/core.js#L82-84 none
    Ember.none(); // true
    Ember.none(null); // true
    Ember.none(undefined); // true
    Ember.none(''); // false
    Ember.none([]); // false
    Ember.none(function(){}); // false
```


This utility function constrains the rules on `Ember.none` by returning `false` for empty string and empty arrays.

``` js empty
    Ember.empty(); // true
    Ember.empty(null); // true
    Ember.empty(undefined); // true
    Ember.empty(''); // true
    Ember.empty([]); // true
    Ember.empty('tobias fünke'); // false
    Ember.empty([0,1,2]); // false
```

Use this to check if a value is an array. This method also returns `true` for array-like objects, like the ones implementing `Ember.Array`.

``` js Array
    Ember.isArray(); // false
    Ember.isArray([]); // true
    Ember.isArray( Ember.ArrayProxy.create({ content: [] }) ); // true
```
### More useful String functions

There are some more functionalities for converting between different String notations: [camelize()](https://github.com/emberjs/ember.js/blob/3b60016c2b6b38646d954014d10f74fcfe1a3d54/packages/ember-runtime/lib/system/string.js#L168-172), [decamelize()](https://github.com/emberjs/ember.js/blob/3b60016c2b6b38646d954014d10f74fcfe1a3d54/packages/ember-runtime/lib/system/string.js#L124-126), [dasherize()](https://github.com/emberjs/ember.js/blob/3b60016c2b6b38646d954014d10f74fcfe1a3d54/packages/ember-runtime/lib/system/string.js#L141-153), [underscore()](https://github.com/emberjs/ember.js/blob/3b60016c2b6b38646d954014d10f74fcfe1a3d54/packages/ember-runtime/lib/system/string.js#L188-191)


Posted by Clemens Müller Mar 8th, 2012  
[Ember.js](http://code418.com/blog/categories/ember-js/)

<http://code418.com/blog/2012/03/08/useful-emberjs-functions/](http://code418.com/blog/2012/03/08/useful-emberjs-functions/>
