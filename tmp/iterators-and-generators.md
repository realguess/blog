The `yield` keyword is used to pause and resume a generator.

# Iterators and Generators

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators

New stuff from JavaScript 1.7: https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/1.7

```
function *fn() {
           ^
SyntaxError: Unexpected token *
    at exports.runInThisContext (vm.js:69:16)
    at Module._compile (module.js:432:25)
    at Object.Module._extensions..js (module.js:467:10)
    at Module.load (module.js:349:32)
    at Function.Module._load (module.js:305:12)
    at Function.Module.runMain (module.js:490:10)
    at startup (node.js:123:16)
    at node.js:1031:3
```

Must use `alias node='node --harmony'.

`man node`:

```
--harmony (enable all harmony features (except typeof))
      type: bool  default: false
```

ECMAScript Harmony (6th Edition)
