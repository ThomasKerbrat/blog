# How to expose your JavaScript library?

It all started with [this tweet](https://twitter.com/ThePracticalDev/status/767860358858076160) from ThePracticalDev.
Despite its uselessness, this repository shows the bare minimum for exposing a JavaScript library.

In the repository, we can find an `index.js` file which contain only one line of code:

``` js
module.exports = require('./love.js');
```

This is how you export your library when someone gets your npm package.
It allow the consumer of your package to write:

``` js
var love = require('love');
```

NodeJS will look for the `love` package in its `/node_modules` directory and find an `index.js` file.
This file will be called to resolve the require instruction.

So let's have an overview of the love.js file:

``` js
 1.  (function () {
 2.    var love = function() { return 'love'; };
 3.    /* some translation of 'love' in other languages */
 4.   love.morseCode = function() { return '.-.. --- ...- .'; };  
 5.
 6.   if(typeof module !== 'undefined' && module.exports) {
 7.     module.exports = love;
 8.   } else if (window) {
 9.     window.love = love;
10.   }
11. }());
```

Here we can see how this library exposes itself to the outer world.

The author declares a `love` variable that will later be exposed.
Then, on line 6, the author checks if the `module` global variable is defined and if an `exports` property exists on it.
If so, the author use the NodeJS way to export modules with the instruction on line 7.
Otherwise, the author exposes its library on the global object `window` if available.
If none of `module` or `window` is available, the library will exposes nothing.

All this code is wrapped around an [Immediately Invoked Function Expression](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)
so the `love` variable (and all other intermediate variables) don't leak outside of this library file.
What goes into line 2 through 4 is completely library specific stuff.
You can split you code across different files and require them - you're in NodeJS.
