# JavaScript

## JavaScript Engine

Parser
'-> AST
    '-> Interpreter -> Bytecode
        '-> Profiler -> Compiler -> Optimized Code

JIT (Just In Time) Compiler

*Is JavaScript an interpreted language?*

It depends on the implementation. There are solutions that are interpreted and others that are compiled.

## Node.js

A JavaScript runtime built on Chrome's V8 JavaScript engine.

- V8 JavaScript Engine
- Node.js Bindings (Node API)
- LibUV (Asynchronous I/O)

## Interesting Tools

### AST Explorer
www.astexplorer.net

Abstract Syntax Tree

## Functions

```js
// Function Expression
var hello = function () {
    console.log('Hello World!')
}
// Function Declaration
function greeting() {
    console.log('Hello World!')
}

// Function Invocation/Call/Execution
hello()
greeting()
```

`call()`  and `apply()` 

All functions have these properties which allows us to invoke a function. `call()` can be used to call a function within an object to apply to a second object by passing the second object as an argument. Both functions can take additional arguments, the difference is `apply()` takes an array of arguments.

```js
obj1.aFunc.call(obj2, arg1, arg2)
obj1.aFunc.apply(obj2, [arg1, arg2])
```

`bind()`

This function operates similarly to `call()` and `apply()` except that it returns a new function that can be stored and used later.

```js
const aFuncObj2 = obj1.aFunc.bind(obj2, arg1, arg2)
aFuncObj2()
```